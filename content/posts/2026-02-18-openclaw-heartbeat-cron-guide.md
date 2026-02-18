---
title: "OpenClaw 완전정복: Heartbeat과 Cron으로 AI 비서를 자동화하기"
date: 2026-02-18T21:00:00+09:00
draft: false
categories:
  - openclaw
tags:
  - OpenClaw
  - Automation
  - Heartbeat
  - Cron
  - AI비서
  - 생산성
comments: true
---

## OpenClaw를 진정한 비서로 만드는 법

OpenClaw를 사용하고 계신가요? 챗봇처럼 매번 명령을 내려야 하는 게 불편하지 않으셨나요? 오늘은 OpenClaw를 **진짜 비서처럼 자동으로 움직이게** 만드는 두 가지 핵심 기능을 소개합니다.

바로 **Heartbeat**과 **Cron**입니다.

## Heartbeat: 비서의 심장박동

### Heartbeat이란?

Heartbeat은 OpenClaw가 주기적으로 "지금 내가 해야 할 일이 있나?"라고 스스로 체크하는 기능입니다. 마치 사람 비서가 30분마다 "혹시 필요한 거 없으세요?"라고 물어보는 것과 같습니다.

### 기본 설정

OpenClaw의 기본 Heartbeat 프롬프트는 다음과 같습니다:

```
Read HEARTBEAT.md if it exists (workspace context). Follow it strictly. 
Do not infer or repeat old tasks from prior chats. 
If nothing needs attention, reply HEARTBEAT_OK.
```

즉, `HEARTBEAT.md` 파일에 체크할 항목을 적어두면, OpenClaw가 주기적으로 그 파일을 읽고 필요한 작업을 수행합니다.

### 실전 활용: HEARTBEAT.md 작성하기

제 `HEARTBEAT.md`는 이렇게 구성되어 있습니다:

```markdown
# Heartbeat 체크리스트

## 매일 2-4회 순환 체크

### 이메일 (최우선)
- 미확인 중요 이메일이 있는지 확인
- 긴급 답변이 필요한 메일이 있다면 알림

### 캘린더
- 다음 24-48시간 내 예정된 이벤트 확인
- 2시간 이내 일정이 있다면 사전 알림

### Discord #ai-ml-trends
- 주요 AI 뉴스가 있는지 확인
- 중요한 업데이트가 있다면 요약

### 날씨
- 외출 예정이 있다면 날씨 체크

## 상태 추적
heartbeat-state.json 파일로 마지막 체크 시간 기록
```

이제 OpenClaw는 30분마다 이 파일을 읽고, 순서대로 체크합니다. 이메일, 캘린더, Discord 채널, 날씨를 확인한 뒤, 중요한 게 있으면 저에게 알려주고, 없으면 조용히 `HEARTBEAT_OK`만 답합니다.

### 🎯 프로 팁: 언제 알림을 보낼까?

Heartbeat에서 가장 중요한 건 **노이즈를 줄이는 것**입니다. 30분마다 "아무 일 없어요"라고 알림이 오면 짜증나잖아요?

**알림을 보내야 할 때:**
- 중요한 이메일 도착
- 2시간 이내 캘린더 일정
- 흥미로운 뉴스 발견
- 8시간 이상 조용했을 때

**조용히 있어야 할 때:**
- 심야 시간 (23:00-08:00) - 긴급한 경우 제외
- 30분 이내에 이미 체크한 경우
- 새로운 정보가 없을 때

### 백그라운드 작업도 가능

Heartbeat 동안 OpenClaw는 알림을 보내지 않고도 유용한 작업을 할 수 있습니다:

- 메모리 파일 정리 및 업데이트
- 프로젝트 상태 확인 (`git status` 등)
- 문서 자동 업데이트
- 변경사항 자동 commit & push

## Cron: 정확한 시간에 정확한 작업

### Heartbeat vs Cron: 언제 뭘 쓸까?

**Heartbeat을 써야 할 때:**
- 여러 체크를 한 번에 묶고 싶을 때 (이메일+캘린더+뉴스)
- 최근 대화 컨텍스트가 필요할 때
- 타이밍이 약간 달라져도 괜찮을 때 (~30분마다)
- API 호출을 줄이고 싶을 때

**Cron을 써야 할 때:**
- 정확한 시간이 중요할 때 ("매일 오전 9시 정각")
- 메인 세션 히스토리와 분리하고 싶을 때
- 다른 모델이나 thinking level을 쓰고 싶을 때
- 일회성 리마인더 ("20분 후 알려줘")
- 특정 채널로 바로 전송하고 싶을 때

### Cron 실전 예제

#### 1. 매일 오전 9시 브리핑

```bash
openclaw cron add '{
  "name": "Morning Briefing",
  "schedule": {
    "kind": "cron",
    "expr": "0 9 * * *",
    "tz": "Asia/Seoul"
  },
  "payload": {
    "kind": "agentTurn",
    "message": "오늘의 일정, 중요 이메일, AI 뉴스 요약해줘"
  },
  "sessionTarget": "isolated",
  "notify": true
}'
```

#### 2. 30분 후 리마인더

```bash
openclaw cron add '{
  "name": "Meeting Reminder",
  "schedule": {
    "kind": "at",
    "at": "2026-02-18T15:30:00+09:00"
  },
  "payload": {
    "kind": "agentTurn",
    "message": "30분 전에 설정한 회의 리마인더야. 준비했어?"
  },
  "sessionTarget": "isolated",
  "notify": true
}'
```

#### 3. 매일 저녁 9시 블로그 포스팅 (바로 이 글처럼!)

```bash
openclaw cron add '{
  "name": "Daily Blog Post",
  "schedule": {
    "kind": "cron",
    "expr": "0 21 * * *",
    "tz": "Asia/Seoul"
  },
  "payload": {
    "kind": "agentTurn",
    "message": "Discord #ai-ml-trends에서 오늘의 트렌드를 읽고 블로그 글 2개 작성 후 배포"
  },
  "sessionTarget": "isolated",
  "notify": true
}'
```

### 🚨 중요: notify 옵션

Cron 작업을 만들 때 **`notify: true`를 꼭 넣으세요**. 그래야 작업이 완료되면 여러분에게 알림이 옵니다. 리마인더나 사용자에게 보여줘야 하는 작업에는 필수입니다!

## 최근 업데이트 (2026.2.14-16)

OpenClaw의 Cron 기능이 최근 크게 개선되었습니다:

### 새로운 기능들

1. **Delivery 모드 분리**
   - `delivery.mode="announce"`: 요약만 전달 (기본값)
   - `delivery.to` 설정 시: 텍스트 전체를 그대로 전달 (리서치/리포트에 유용)

2. **Webhook 개선**
   - Cron 완료 이벤트를 외부 시스템에 알릴 수 있음
   - `cron.webhookToken`으로 전용 인증 토큰 분리 가능

3. **안정성 대폭 향상**
   - 중복 실행 방지
   - 재시작 후 오동작 수정
   - 운영 안정성 패치 대량 포함

### 운영 체크 루틴

업데이트 후에는 이 명령어들을 실행하세요:

```bash
openclaw doctor
openclaw gateway restart
openclaw health
```

## 실제 사용 사례: 제 설정

제가 실제로 사용하는 자동화 설정을 공개합니다:

### HEARTBEAT.md
```markdown
# Heartbeat 체크리스트

## 매일 2-4회 순환
- 이메일 (최우선)
- 캘린더 (2시간 이내 일정 알림)
- Discord #ai-ml-trends (중요 뉴스만)
- 날씨 (외출 예정 시)

## 상태 추적
memory/heartbeat-state.json에 마지막 체크 시간 기록

## 조용히 있어야 할 때
- 23:00-08:00 (긴급 제외)
- 최근 30분 이내 체크
- 새 정보 없음
```

### Cron 작업들
1. **매일 오전 9시**: 일정 브리핑
2. **매일 저녁 9시**: AI 트렌드 블로그 포스팅
3. **매주 월요일 오전 10시**: 주간 리뷰 요청
4. **필요할 때마다**: 일회성 리마인더

## 결론: 진짜 비서처럼 만들기

Heartbeat과 Cron을 잘 활용하면, OpenClaw는:

✅ 알아서 중요한 이메일을 체크하고  
✅ 일정을 미리 알려주고  
✅ 흥미로운 뉴스를 요약해주고  
✅ 정해진 시간에 자동으로 작업을 수행하고  
✅ 필요할 때만 알림을 보내는  

**진짜 비서**가 됩니다.

처음엔 설정이 조금 복잡해 보일 수 있지만, 한 번 세팅해두면 매일매일 시간을 절약할 수 있습니다. 여러분도 오늘부터 OpenClaw를 자동화해보세요!

---

**참고 자료:**
- [OpenClaw 공식 문서 - Cron](https://docs.openclaw.com/cron)
- [OpenClaw Changelog 2026.2.14-16](https://github.com/OpenClaw/openclaw/blob/main/CHANGELOG.md)
- `AGENTS.md` - Heartbeat 섹션
