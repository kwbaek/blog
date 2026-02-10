---
title: "OpenClaw Heartbeat 활용법: AI가 알아서 챙겨주는 똑똑한 비서 만들기"
date: 2026-02-10T21:00:00+09:00
draft: false
categories: ["openclaw"]
tags: ["OpenClaw", "Heartbeat", "Automation", "Productivity", "AI Assistant"]
comments: true
---

## AI 비서가 내 일을 알아서 챙긴다면?

OpenClaw의 **Heartbeat** 기능을 제대로 활용하면, AI 비서가 단순한 대화 상대를 넘어 **진짜 일을 대신 처리해주는 존재**로 진화합니다. 이 글에서는 Heartbeat를 실전에서 어떻게 활용할 수 있는지 소개합니다.

## Heartbeat란?

Heartbeat는 **주기적으로 AI가 깨어나 체크리스트를 수행하는 기능**입니다. 사용자가 명령을 내리지 않아도, AI가 스스로 필요한 작업을 확인하고 보고합니다.

기본 동작 방식:
- OpenClaw Gateway가 설정된 주기(기본 30분)마다 AI 세션에 heartbeat 메시지를 전송
- AI가 `HEARTBEAT.md` 파일을 읽고 작성된 작업을 수행
- 특별한 일이 없으면 `HEARTBEAT_OK` 응답으로 조용히 넘어감
- 중요한 사항이 있으면 사용자에게 알림

## 실전 활용 예시

### 1. 이메일 모니터링

```markdown
# HEARTBEAT.md

## 이메일 체크 (하루 3-4회)
- Gmail 미읽은 메일 확인
- "urgent", "ASAP", "중요" 키워드 포함 메일은 즉시 알림
- 그 외 메일은 요약 정리
```

AI가 알아서 inbox를 스캔하고, 급한 메일만 골라서 알려줍니다.

### 2. 캘린더 리마인더

```markdown
## 일정 확인 (오전 8시, 점심, 퇴근 전)
- 앞으로 2시간 이내 일정이 있으면 15분 전에 알림
- 내일 일정 요약 (밤 10시)
- 준비물이 필요한 미팅은 하루 전에 미리 알림
```

### 3. 프로젝트 상태 모니터링

```markdown
## GitHub 이슈/PR 체크 (하루 2회)
- 내가 멘션된 이슈/PR 확인
- CI/CD 실패한 빌드 알림
- 오래된 PR 리뷰 요청 리마인드
```

### 4. 소셜 미디어 트렌드 수집

```markdown
## AI/ML 트렌드 수집 (하루 1회, 저녁 9시)
- Discord #ai-ml-trends 채널에서 오늘의 주요 뉴스 요약
- 중요한 논문 발표나 모델 출시 정보 정리
- 블로그 포스팅용 소재로 저장
```

이 글을 쓰는 지금도, AI가 Discord에서 수집한 트렌드를 바탕으로 자동으로 블로그 글을 작성하고 있습니다!

## Heartbeat vs Cron: 언제 무엇을 쓸까?

### Heartbeat를 쓸 때:
- 여러 체크를 한 번에 묶어서 처리 (이메일 + 캘린더 + 알림)
- 최근 대화 맥락이 필요한 작업
- 타이밍이 약간 밀려도 괜찮은 경우 (~30분 단위)
- API 호출을 줄이고 싶을 때

### Cron을 쓸 때:
- 정확한 시간에 실행해야 하는 작업 ("매일 오전 9시 정각")
- 독립적인 작업으로 실행하고 싶을 때
- 다른 모델이나 thinking level을 사용하고 싶을 때
- 일회성 리마인더 ("20분 후에 알려줘")

## 프로 팁: 상태 파일로 중복 방지

Heartbeat가 너무 자주 같은 내용을 반복하면 성가시겠죠? 간단한 JSON 파일로 마지막 체크 시간을 기록하세요:

```json
// memory/heartbeat-state.json
{
  "lastChecks": {
    "email": 1770708987,
    "calendar": 1770705400,
    "github": 1770650000
  }
}
```

AI가 이 파일을 읽고, "마지막 체크가 6시간 이상 지났으면" 같은 조건으로 똑똑하게 판단할 수 있습니다.

## 조용한 밤, 바쁜 낮

```markdown
## 조용한 시간 설정
- 밤 11시 ~ 아침 8시: 긴급 키워드 제외하고는 HEARTBEAT_OK
- 회의 중: Discord presence 확인 후 조용히
```

AI가 상황을 파악하고 적절히 조용해지는 것도 중요합니다. 사람처럼요.

## 마치며

Heartbeat는 **"물어보지 않아도 알아서 챙기는 비서"**를 만드는 핵심 기능입니다. 처음엔 간단한 체크리스트로 시작해서, 점차 더 복잡한 자동화를 추가해보세요.

AI가 당신의 루틴을 학습하고, 필요한 순간에 필요한 정보를 제공하는 **진짜 개인 비서**가 되는 경험을 해보실 수 있을 겁니다.

---

**관련 문서:**
- [OpenClaw Cron 가이드](https://docs.openclaw.ai/features/cron)
- [AGENTS.md 템플릿](https://github.com/openclaw/openclaw/blob/main/docs/workspace/AGENTS.md)
- [Workspace 구조](https://docs.openclaw.ai/workspace)
