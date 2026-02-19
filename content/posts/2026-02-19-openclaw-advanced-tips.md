---
title: "OpenClaw 고급 활용법: Cron, Heartbeat, 그리고 서브에이전트 마스터하기"
date: 2026-02-19T21:00:00+09:00
draft: false
categories:
  - openclaw
tags:
  - openclaw
  - automation
  - cron
  - agents
  - productivity
comments: true
---

OpenClaw를 단순한 AI 어시스턴트로 사용하고 계신가요? 이 강력한 플랫폼은 훨씬 더 많은 것을 할 수 있습니다. 이번 포스트에서는 OpenClaw의 고급 기능들을 활용하여 진정한 자동화와 자율성을 구현하는 방법을 다룹니다.

## Cron: 스마트 스케줄링의 힘

OpenClaw의 cron 시스템은 단순한 작업 스케줄러가 아닙니다. AI 에이전트가 정해진 시간에 자율적으로 작업을 수행하고, 결과를 분석하고, 당신에게 보고하는 완전한 자동화 시스템입니다.

### 기본 cron 작업 생성

```bash
# 매일 오전 9시에 이메일 체크
openclaw cron add \
  --schedule "0 9 * * *" \
  --task "Check my Gmail inbox and summarize important unread emails" \
  --session isolated \
  --delivery announce
```

### Webhook 통합: 외부 시스템과 연동

cron 작업 완료 시 외부 시스템에 알림을 보낼 수 있습니다:

```bash
# 작업 완료 시 webhook으로 결과 전송
openclaw cron add \
  --schedule "0 */6 * * *" \
  --task "Analyze latest AI news and generate summary" \
  --delivery webhook \
  --webhook-url "https://your-server.com/webhook" \
  --session isolated
```

**중요:** 보안을 위해 Gateway 메인 토큰 대신 **`cron.webhookToken`을 별도로 설정**하세요.

### Cron과 Heartbeat: 언제 뭘 써야 할까?

**Cron을 사용할 때:**
- 정확한 시간이 중요한 작업 (예: "매일 오전 9시 정각")
- 독립적인 작업으로 격리가 필요한 경우
- 다른 모델이나 thinking level을 사용하고 싶을 때
- 일회성 리마인더 (예: "20분 후 알려줘")

**Heartbeat를 사용할 때:**
- 여러 체크를 한 번에 묶을 수 있을 때 (이메일 + 캘린더 + 날씨)
- 최근 대화 컨텍스트가 필요한 경우
- 약간의 시간 차이는 괜찮은 경우 (정각이 아니어도 됨)
- API 호출을 줄여 비용을 절감하고 싶을 때

### 최신 Cron 기능 (2026.2.17 업데이트)

```bash
# stagger로 cron 작업 분산 실행
openclaw cron add --stagger 5m ...

# 정확히 정각에 실행
openclaw cron add --exact ...

# usage telemetry로 모델 사용량 추적
openclaw cron runs <job-id>  # 모델/프로바이더 사용량 확인 가능
```

## Heartbeat: 능동적인 AI 어시스턴트

Heartbeat는 OpenClaw가 당신을 위해 능동적으로 일하게 만드는 기능입니다. 단순히 반응하는 것이 아니라, 스스로 판단하고 행동합니다.

### HEARTBEAT.md 설정

workspace에 `HEARTBEAT.md` 파일을 생성하여 체크리스트를 만드세요:

```markdown
# Heartbeat Checklist

## Daily Checks (rotate 2-4 times per day)
- [ ] Email: Check for urgent unread messages
- [ ] Calendar: Upcoming events in next 24-48h
- [ ] Mentions: Twitter/Discord notifications
- [ ] Weather: If outdoor activities planned

## Proactive Work
- Review and update memory files
- Check project status (git, build health)
- Update MEMORY.md with learnings
- Commit and push changes

## Quiet Time
23:00-08:00 unless urgent
```

### Heartbeat 상태 추적

`memory/heartbeat-state.json`으로 마지막 체크 시간을 기록하세요:

```json
{
  "lastChecks": {
    "email": 1703275200,
    "calendar": 1703260800,
    "weather": null
  }
}
```

### 언제 말하고 언제 침묵할까?

**목소리를 낼 때 (메시지 전송):**
- 중요한 이메일이 도착했을 때
- 2시간 이내 일정이 있을 때
- 8시간 이상 침묵했을 때
- 흥미로운 발견을 했을 때

**침묵할 때 (HEARTBEAT_OK 반환):**
- 심야 시간 (23:00-08:00) 비긴급 상황
- 사용자가 바쁜 것이 명확할 때
- 30분 전 체크한 내용과 동일할 때
- 새로운 정보가 없을 때

## 서브에이전트: 작업 위임의 기술

복잡한 작업은 서브에이전트에게 위임하세요. 메인 세션은 깨끗하게 유지하면서 백그라운드에서 작업이 진행됩니다.

### 서브에이전트 생성

```bash
# CLI에서
openclaw sessions spawn \
  --task "Research and write a comprehensive report on quantum computing trends in 2026" \
  --label "quantum-research" \
  --cleanup delete

# 채팅에서
/subagents spawn "Analyze the last 100 commits in the project repo and identify potential bugs"
```

### 서브에이전트 관리

```bash
# 실행 중인 서브에이전트 목록
openclaw subagents list

# 특정 서브에이전트에 지시 전달
openclaw subagents steer <id> "Also include security vulnerability analysis"

# 서브에이전트 종료
openclaw subagents kill <id>
```

### 중첩 서브에이전트 (2026.2.15 신규)

서브에이전트가 다시 서브에이전트를 생성할 수 있습니다:

```yaml
# config.yaml
agents:
  defaults:
    subagents:
      maxSpawnDepth: 3  # 서브에이전트의 서브에이전트의 서브에이전트까지
```

## 고급 팁 모음

### 1. 세션별 모델 오버라이드

```bash
openclaw session_status --model "anthropic/claude-sonnet-4-6"
```

### 2. Hooks로 자동화 강화

```bash
openclaw hooks enable session-memory  # /new 시 세션 자동 저장
openclaw hooks enable command-logger  # 명령 로깅
```

### 3. Discord Components v2 활용

버튼, 셀렉트, 모달로 인터랙티브한 워크플로우 구축:

```bash
openclaw message send \
  --channel "#work" \
  --components '{
    "blocks": [
      {
        "type": "section",
        "text": "Choose an action:",
        "buttons": [
          {"label": "Approve", "style": "success"},
          {"label": "Reject", "style": "danger"}
        ]
      }
    ],
    "reusable": true
  }'
```

### 4. 메모리 검색 활용

```bash
# 과거 작업 내용 검색
openclaw memory_search "quantum computing project decisions"
```

### 5. Git 자동화

```bash
# 매일 자동 커밋 & 푸시
openclaw cron add \
  --schedule "0 18 * * *" \
  --task "Review today's changes in workspace, create meaningful commit message, and push to main branch" \
  --session isolated
```

## 운영 체크리스트

업데이트 후 항상 이 루틴을 실행하세요:

```bash
openclaw doctor
openclaw gateway restart
openclaw health
openclaw version
```

## 결론: 자율적인 AI 어시스턴트로

OpenClaw는 당신이 명령을 내릴 때만 반응하는 도구가 아닙니다. Cron, Heartbeat, 서브에이전트를 적절히 조합하면 **자율적으로 판단하고 행동하는 진정한 AI 어시스턴트**가 됩니다.

- **Cron**으로 예측 가능한 작업을 스케줄링하고
- **Heartbeat**로 능동적인 모니터링과 알림을 구현하고
- **서브에이전트**로 복잡한 작업을 위임하세요

당신이 잠들어 있는 동안에도 OpenClaw는 깨어 있습니다. 🌙

---

**참고 자료:**
- [OpenClaw 공식 문서](https://openclaw.ai/docs)
- [Cron 가이드](https://openclaw.ai/docs/cron)
- [Heartbeat 설정](https://openclaw.ai/docs/heartbeat)
- [서브에이전트 활용](https://openclaw.ai/docs/subagents)

**다음 포스트 예고:** OpenClaw와 외부 서비스 통합 - Webhook, API, 그리고 Zapier 연동
