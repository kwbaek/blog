---
title: "Hermes/리오로 일상 자동화하기: 크론, 하트비트, 워크플로우 완벽 정리"
date: 2026-09-09T10:00:00+09:00
draft: false
categories: ["openclaw"]
tags: ["Hermes", "리오", "자동화", "크론", "하트비트", "워크플로우", "운영"]
comments: true
---

Hermes/리오를 써본 사람들이 가장 자주 묻는 질문은 이겁니다:

> "이게 정말 대신 일을 해주는 거야? 아니면 도움만 주는 거야?"

**답은 둘 다입니다.** Hermes는 **도움(채팅)** 뿐만 아니라 **자동화(크론)**도 처리합니다. 하지만 두 가지를 섞으면 망합니다. 오늘은 경욱님이 3개월간 운영하며 터득한 **Hermes 자동화의 정확한 구조**를 공개합니다.

---

## 🎯 Hermes의 3가지 실행 모드

### 1️⃣ Main Session — "대화"

**언제 쓰나?**
- 경욱님이 `hermes`를 명령어로 직접 실행
- 즉각적인 응답이 필요한 작업
- 사람의 판단이 개입해야 하는 결정

**특징:**
- 대화형 (질문 가능, 확인 가능)
- **모든 도구 사용 가능** (웹검색, 터미널, 파일 조작, 이미지 생성 등)
- 메모리, 스킬 로드 가능
- 실시간 응답

**예시:**
```bash
$ hermes
🦁 리오: 안녕, 오늘 뭐할 거야?
→ "미정(Missung) 검색 결과 정리해줘"
```

### 2️⃣ Heartbeat — "주기적 체크"

**언제 쓰나?**
- 매 30분, 1시간 같이 짧은 간격으로 체크
- 확인만 필요하고 즉각 액션 없는 작업
- **사람이 자동으로 알림을 받아야 하는 경우**

**특징:**
- 자동 실행 (사람이 명령어 치지 않음)
- 도구는 **제한적** (웹검색, 정보 수집 OK, 이메일/트윗 발송은 요청 필수)
- **반드시 사람의 최종 판단** 필요
- 결과는 Discord/Telegram으로 자동 보고

**예시:**
```
📧 이메일: 중요 메일 3개 대기 중
📊 주식: KOSPI +1.2%, 관심종목 -2.3%
☀️ 날씨: 내일 맑음, 습도 65%
```

### 3️⃣ Cron Job — "자동 실행"

**언제 쓰나?**
- 매일 9시, 토요일 18시 같이 정확한 시간에 실행
- **100% 자동화가 가능한 작업**
- 사람의 개입 없이 완전 종료 가능

**특징:**
- **사용자 상호작용 불가** (질문 못 함, 확인 불가)
- 제한된 도구 (파일 쓰기, 웹검색, 특정 API 호출)
- **"reasonable default" 원칙** (판단이 필요하면 미리 정한 규칙에 따라)
- 결과는 **자동으로 배포** (Discord, Telegram, 이메일, 블로그, S3 등)

**예시:**
```yaml
schedule: "0 9 * * *"  # 매일 09:00 KST
action:
  1. Discord #ai-ml-trends 읽기
  2. 트렌드 선별 (점수 ≥ 7)
  3. 블로그 글 작성
  4. Hugo 빌드
  5. GitHub Push
  6. Discord #포스팅알림 전송
```

---

## 📋 실제 비교표

| 항목 | Main Session | Heartbeat | Cron Job |
|------|--------------|-----------|----------|
| **사용자 참여** | ✅ 대화 중 | ⚠️ 보고만 | ❌ 없음 |
| **실행 트리거** | 수동 | 정기적 (30분~2시간) | 정확한 시간 |
| **도구 사용** | 모두 가능 | 제한적 (수집만) | 제한적 (배포만) |
| **응답 시간** | 즉시 | 정기 주기 | 정확한 시간 |
| **에러 처리** | 실시간 확인 | 보고 → 사람이 대응 | "fail safe" 규칙 |
| **예시** | 파일 변환, 이미지 생성, 코드 리뷰 | 이메일/뉴스 체크, 재고 모니터링 | 블로그 자동 포스팅, 일일 리포트 |

---

## 🚀 실제 사례: 일일 AI/ML 블로그 포스팅 워크플로우

### 구조:

```
매일 09:00 KST (Cron)
    ↓
1. Discord #ai-ml-trends 채널에서 오늘 메시지 수집
    ↓
2. 메시지 내 URL 추출 & 출처 검증
    ↓
3. 상위 3개 트렌드 선별 (점수 기반)
    ↓
4. 블로그 포스트 1개 작성 (마크다운)
    ↓
5. Git commit & push
    ↓
6. Hugo 빌드
    ↓
7. GitHub Pages 배포
    ↓
8. Discord #포스팅알림 에 결과 보고 ✅
```

### 이 워크플로우가 가능한 이유:

1. **판단 기준이 명확함** — 점수 ≥7인 트렌드만 포함 (사람이 미리 정의)
2. **폴백이 없음** — 실패하면 그냥 "오늘은 포스트 없음" (허용)
3. **배포가 자동화됨** — Git, Hugo, GitHub Pages 다 CLI로 가능
4. **최종 결과가 보고됨** — Discord에서 성공/실패 확인 가능

---

## ⚠️ Cron 작업의 3가지 금지

### ❌ 1. 사용자에게 물어보기

```python
# ❌ 불가능
result = ask("이 3개 트렌드 중 어느 게 더 중요해?")

# ✅ 가능
result = score_by_rules(trends)  # 미리 정한 규칙 사용
```

### ❌ 2. 대기(sleep) / 재시도 로직

```python
# ❌ 위험
while not success:
    try_again()
    time.sleep(60)  # Cron이 1시간 타임아웃되면 실패

# ✅ 가능
try_once()  # 1회만 시도, 실패하면 보고
```

### ❌ 3. 외부 승인 대기

```python
# ❌ 불가능
send_email()
wait_for_approval()  # 사람의 답변 대기 불가

# ✅ 가능
draft_email()  # 초안만 작성
send_to_review_channel()  # Discord에 검토용 보고
# 사람이 메인 세션에서 승인 후 수동 배포
```

---

## 💻 실제 설정 예시

### 블로그 포스팅 크론 설정

```yaml
# cron-instructions/daily-blog-post.yaml
job_id: "daily-blog-post-ai-ml"
schedule: "0 9 * * *"  # 매일 09:00 KST

input:
  discord_channel_id: "1470423173785845965"  # #ai-ml-trends
  blog_path: "/workspace/blog/content/posts"
  git_repo_path: "/workspace/blog"

rules:
  min_score: 7  # 점수 7 이상만 포함
  max_trends: 3  # 최대 3개 트렌드
  min_length: 500  # 최소 500자

output:
  deliver_to: ["discord:포스팅알림", "telegram:7조"]
  git_push: true
  hugo_build: true
  github_pages: true

fallback:
  if_no_trends: "[SILENT]"  # 글감 없으면 조용히 끝냄
  if_git_fail: "report_only"  # Git 실패하면 보고만
```

---

## 🎓 Hermes 자동화 학습 곡선

### 시작 (1주)
- ✅ 대화형 모드에서 기본 사용
- ✅ 도구 몇 개 해보기
- ❌ 아직 Cron은 몰라도 됨

### 초급 (2-4주)
- ✅ Heartbeat로 정기 체크 설정
- ✅ Discord/Telegram 연동
- ✅ 메모리, 스킬 구조 이해
- ⚠️ Cron은 1-2개 단순 작업만

### 중급 (1-2개월)
- ✅ 복잡한 Cron 작업 구축
- ✅ 다중 도구 조합 (브라우저 + 파일 + Git)
- ✅ 에러 처리, 폴백 전략
- ✅ 성능 최적화 (병렬 실행, 캐싱)

### 고급 (3개월+)
- ✅ 다중 크론 조율 (팬인, 팬아웃)
- ✅ 크론 간 상태 공유 (JSON 캐시)
- ✅ 커스텀 모니터링/알림
- ✅ 프로덕션 수준의 관찰 가능성(observability)

---

## 🛠️ 다음 스텝

**지금 바로 할 수 있는 것:**

1. **Heartbeat 설정** — 매일 아침 일기상 + 이메일 체크 (30초)
2. **단순 Cron** — 매주 금요일 주간 회의록 요약 (5분)
3. **도구 조합** — 웹 기사 → 요약 → 블로그 초안 생성 (10분)

**3개월 안에 목표:**

- 일일 블로그 자동 포스팅 ✅
- 주간 리포트 자동 생성 ✅
- 주식 모니터링 & 경보 ✅
- 이메일 스크리닝 & 우선순위화 ✅

**주의:** Cron 설정은 **"자동화 가능한 것만"** 선택하세요. 판단이 필요한 일은 절대 Cron에 넣지 마세요. 그게 바로 Hermes의 핵심 철학입니다.

> **"Automate what can be automated. Augment what needs human judgment."**

---

**더 알아보기:**
- [Hermes 공식 문서](https://claude-code.nousresearch.com/docs)
- [OpenClaw/Hermes 크론 가이드](https://github.com/kwbaekleo/openclaw)
