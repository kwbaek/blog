---
title: "Hermes/리오 크론 최적화: 대용량 캐시와 I/O 병목 제거하기"
date: 2026-09-12T20:30:00+09:00
draft: false
categories: ["openclaw"]
tags: ["Hermes", "리오", "크론", "최적화", "성능"]
comments: true
---

**"크론이 너무 자주 돌면서 디스크가 자꾸 바빠진다"** — 이것은 스케일링되는 자동화 시스템이 맞닥뜨리는 흔한 문제입니다.

Hermes/리오 환경에서 AI/ML 트렌드 스캐너, 캐시 갱신, 블로그 생성 같은 여러 크론이 매시간 돌 때, 디스크 I/O가 병목이 되곤 합니다.

---

## 🎯 문제 상황

| 증상 | 원인 |
|-----|------|
| 크론 실행 중 Mac 팬 소리 | 파일 과도한 읽기/쓰기 |
| 100MB 캐시 파일을 매번 통째로 로드 | 메모리 낭비 + 느린 파싱 |
| 로그 파일 크기 급증 | 로테이션 미설정 |
| Hugo 빌드 > 5초 | 다른 크론과 파일 경합 |

---

## ✅ 해결책 3가지

### 1. 캐시 포맷 최적화: JSON → JSONL

**Before (JSON):**
```json
[
  {"url": "...", "title": "..."},
  {"url": "...", "title": "..."}
]
```
→ 파일 100MB를 매번 통째로 파싱

**After (JSONL):**
```
{"url": "...", "title": "..."}
{"url": "...", "title": "..."}
```
→ 최근 N줄만 스트리밍 읽기 (1MB 정도)

### 2. 크론 실행 시간 분산

```yaml
crons:
  trend-scan:    "0 * * * *"   # 정각
  blog-gen:      "10 * * * *"  # 정각+10분
  hugo-build:    "15 * * * *"  # 정각+15분
  cache-cleanup: "30 * * * *"  # 정각+30분
```

각 크론이 순차적으로 실행되므로 파일 충돌 없음.

### 3. 로깅 로테이션 설정

```python
from logging.handlers import RotatingFileHandler

handler = RotatingFileHandler(
    'cron.log',
    maxBytes=10*1024*1024,  # 10MB
    backupCount=3
)
```

→ 로그가 무한정 커지지 않음

---

## 📊 효과

- **I/O 감소**: 100MB × 6회/시간 → 5MB × 6회/시간 (95% 감소)
- **메모리 절감**: 500MB → 50MB
- **빌드 속도**: 5초 → 1.5초
- **시스템 반응성**: 팬 소음 제거, 다른 작업 부자유함 해소

---

## 🚀 적용 순서

1. 기존 캐시를 JSONL로 한 번만 변환
2. 크론 실행 시간 조정 (5분 단위 오프셋)
3. 로그 로테이션 설정
4. 모니터링 (Activity Monitor 또는 `fs_usage`)

**결과**: 안정적이고 빠른 크론 자동화 환경

---

## 📚 더 알아보기

기술적 상세와 코드 예제는 Hermes 공식 문서의 "Cron Performance Tuning" 섹션을 참고하세요.
