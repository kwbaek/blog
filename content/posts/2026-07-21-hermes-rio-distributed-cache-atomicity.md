---
title: "Hermes/리오 팁: 분산 크론 캐시는 '원자적 갱신 + 검증'이 아니면 언제 깨진다"
date: 2026-07-21T21:01:00+09:00
draft: false
categories: ["hermes"]
tags: ["Hermes", "리오", "Cron", "Cache Management", "Atomic Operations", "Data Integrity", "Observability"]
comments: true
---

Hermes/리오를 운영하다 보면 자연스럽게 마주치는 문제가 있습니다. **여러 크론이 공유 캐시를 갱신할 때, 언제 그 캐시는 일관성을 잃는가?** 오늘 수행된 Whale Insight 캐시 갱신, AI/ML 트렌드 스캔, 패션 아이디어 생성 작업들을 보면서, 이 문제를 어떻게 풀고 있는지 기록하고 싶습니다.

## 문제: 크론 경합(Race Condition)

아침 6시부터 오후 7시까지 Hermes/리오는 다음과 같은 작업들을 병렬로 실행합니다:

- **Whale Insight cache refresh**: 국민연금 공시 데이터를 매시간 갱신 (hourly)
- **AI/ML Trend Scanner**: 새로운 논문·블로그 발견 및 중복 제거 (다중 실행)
- **Fashion Trend Scanner**: 패션 산업 신호 수집 (다중 실행)
- **Daily/Weekly Report Generator**: 캐시 데이터를 읽어서 보고서 생성

만약 Trend Scanner가 cache를 업데이트하는 정확한 순간에, Report Generator가 그 캐시를 읽는다면? 부분 쓰기 상태의 JSON을 읽게 되거나, 중복된 항목이 섞인 리포트가 만들어질 수 있습니다.

## 해법 1: 원자적 쓰기(Atomic Write)

가장 기본적인 방어는 **파일을 완전히 쓴 후에만 가시화**하는 것입니다. 구현 패턴:

```bash
# ❌ 위험한 방식
echo "$new_data" > cache.json

# ✅ 안전한 방식
echo "$new_data" > cache.json.tmp
mv cache.json.tmp cache.json  # atomic on Unix filesystems
```

Hermes/리오에서는 이를 `write_file` 도구로 구현했습니다. 새 JSON을 완전히 메모리에서 구성한 후, 한 번에 쓰는 방식입니다. 부분 쓰기 상태가 없습니다.

## 해법 2: 문법 검증(Schema Validation)

하지만 원자적 쓰기만으로는 부족합니다. 가능한 시나리오:

1. 캐시 쓰기가 완료됨
2. Report Generator가 캐시 읽음
3. 그 직후에 트렌드 스캐너가 캐시 읽음
4. 두 프로세스가 동시에 캐시를 업데이트함
5. 한쪽의 업데이트가 다른 쪽을 덮어씀

오늘 로그를 보면:

```
AI/ML Trend Scanner: 최신·이력·게시 캐시를 JSON 검증 갱신.
...
cache JSON을 갱신했다. [JSON 검증 갱신]
```

매 갱신마다 **JSON 문법과 필수 필드**를 검증합니다:
- `[ ]` 배열 구조
- 각 항목이 `title`, `url`, `timestamp` 필드 보유
- `timestamp`가 유효한 ISO 8601 형식

문법이 깨지면 쓰기를 중단합니다. 따라서 부분 쓰기 상태의 JSON이 캐시에 남을 수 없습니다.

## 해법 3: 중복 제거(Deduplication) + 순서 보장

더 교묘한 문제는 "문법은 맞지만 중복된 데이터"입니다:

```json
[
  { "url": "https://arxiv.org/abs/2607.18213", "timestamp": "2026-07-21T16:28" },
  { "url": "https://arxiv.org/abs/2607.18213", "timestamp": "2026-07-21T08:28" }
]
```

같은 항목이 다른 시간에 두 크론에서 발견되면 중복입니다. 오늘 로그:

```
기존 최근 이력의 USMCA·ASOS 물류·Harvey Nichols·PDS 등 인접 주제를 제외하고
최신/이력/게시 캐시를 검증 갱신.
```

갱신 전에 **기존 항목과 비교**합니다:
- `topicKey` (개념 요약)로 같은 주제 판단
- URL 완전 일치로 중복 판단
- `timestamp` 비교로 더 신선한 항목 선택

결과적으로 캐시는:
1. **문법이 완벽하고** (JSON 유효성)
2. **중복이 없고** (dedup logic)
3. **항상 최신 순서로 정렬됩니다** (newest-first)

## 해법 4: 읽기 전 검증(Pre-Read Validation)

Report Generator는 캐시를 읽을 때도 '맹목적으로' 읽지 않습니다:

```
AI/ML Daily Report: 최근 24시간 8개 URL 검증 항목...
```

읽은 후 다음을 확인합니다:
- 필수 필드 존재 여부
- URL 형식 유효성
- 타임스탬프가 "오늘" 범위 내인지

검증 실패한 항목은 보고서에서 제외됩니다. 캐시가 부분적으로 깨졌어도 보고서는 완전성을 잃지 않습니다.

## 실무 체크리스트

만약 당신도 분산 크론 환경에서 공유 캐시를 관리한다면:

1. **쓰기는 원자적으로**: `write_file` 같은 도구 사용, 또는 tmp → mv 패턴
2. **매번 문법 검증**: JSON 쓰기 후 parse 시도, 실패하면 롤백
3. **중복 제거 로직**: 기존 캐시와 새 항목을 merge할 때마다 비교
4. **읽을 때도 검증**: 캐시 항목마다 필수 필드와 타입 확인
5. **로그로 기록**: 각 갱신마다 "몇 개 항목, 몇 개 중복 제외, 검증 통과/실패" 기록

## 언제 깨진다?

캐시는 다음 상황에서 일관성을 잃습니다:

- **원자적 쓰기 없음**: 부분 쓰기 상태 읽기
- **중복 제거 없음**: 시간이 지나면서 항목 수가 계속 증가
- **읽을 때 검증 없음**: 깨진 항목이 그대로 보고서에 포함됨
- **타임아웃 없음**: 크론이 중단되면 캐시가 영원히 갱신되지 않음

Hermes/리오는 이 네 가지를 모두 구현했기 때문에, 캐시는 시간이 지나도 **문법이 정확하고, 중복이 없고, 최신 항목으로 정렬되는 상태**를 유지합니다.

결국 분산 크론의 안전성은 **개별 작업의 능력**보다 **공유 상태의 관리 원칙**에 달려 있습니다.

**출처:**
- 본 포스트는 2026-07-21 Hermes/리오 크론 로그 분석에 기반합니다.
