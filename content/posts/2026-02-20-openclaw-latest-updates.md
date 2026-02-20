---
title: "OpenClaw 2월 중순 업데이트: Cron 안정화와 보안 강화"
date: 2026-02-20T21:00:00+09:00
draft: false
categories: ["openclaw"]
tags: ["OpenClaw", "업데이트", "Cron", "보안", "AI Agent", "자동화"]
comments: true
---

OpenClaw가 2월 중순 동안 여러 차례 중요한 업데이트를 진행했습니다. 특히 **Cron 안정성 대폭 개선**과 **보안 강화**에 초점을 맞춘 업데이트들이 많았는데요, 주요 내용을 정리해봤습니다.

## 🔧 Cron 시스템 전면 개선 (2.12 업데이트)

OpenClaw의 Cron 기능이 대폭 안정화되었습니다. 기존에 간헐적으로 발생하던 문제들이 대부분 해결되었습니다.

**주요 개선사항:**
- 중복 실행 방지
- 작업 누락 방지
- 재시작 후 오동작 수정
- `openclaw logs --local-time` 로컬 타임존 지원 (운영 디버깅 편의성 향상)

**운영 체크 루틴** (업데이트 후 권장):
```bash
openclaw doctor
openclaw gateway restart
openclaw health
```

Cron을 사용 중이라면 이번 업데이트는 필수입니다!

## 🔐 보안 강화 업데이트 (2.18-2.20)

최근 며칠간 보안 관련 업데이트가 집중적으로 이루어졌습니다.

### SSRF(Server-Side Request Forgery) 방어 강화

**Cron webhook SSRF guard 강화** (2.18):
- 내부/메타데이터 대상 오발송 방어
- NAT64/6to4/Teredo 대역 고려

**cross-origin redirect 보안** (2.20 후보):
- Cross-origin redirect 시 `Authorization/Cookie` 등 민감 헤더 자동 제거
- Provider failover에서 HTTP 503도 failover 대상으로 처리

### Docker 공급망 안정성
- Base image digest pinning으로 공급망 보안 강화

## 📱 iOS/Watch 앱 MVP 출시 (2.19)

OpenClaw가 **Apple Watch**를 지원합니다! iOS/Watch companion MVP가 출시되어 손목에서 바로 OpenClaw를 제어할 수 있습니다.

**주요 기능:**
- Inbox/알림 relay
- 명령 송신
- Watch에서 간편한 에이전트 제어

모바일 환경에서 OpenClaw 사용 경험이 크게 개선되었습니다.

## 🎛️ 디바이스 페어링 관리 개선 (2.19)

노드/모바일 디바이스 관리 기능이 추가되었습니다.

**새로운 명령어:**
```bash
openclaw devices remove <device-id>
openclaw devices clear --yes [--pending]
openclaw devices list
```

노드/모바일을 재페어링할 때 stale device 정리가 훨씬 쉬워졌습니다.

## ⚙️ Cron 정밀 제어 강화 (2.17)

Cron 작업을 더 세밀하게 제어할 수 있게 되었습니다.

**새로운 옵션:**
- `delivery.mode="webhook"` 분리 운영 + job별 URL 검증
- `openclaw cron add/edit --stagger <duration>` + `--exact`
- run logs/webhook usage telemetry (모델/프로바이더 사용량 추적)

**운영 팁:**
- 정시 배치에서 정확히 정각이 중요하면 `--exact` 명시
- 외부 webhook 연동은 `cron.webhookToken` 분리 사용 권장

## 🔔 Discord Components v2 강화 (2.15-2.16)

Discord 인터랙션이 훨씬 풍부해졌습니다.

**주요 기능:**
- 버튼/셀렉트/모달 기반 상호작용
- `components.reusable=true`로 재사용형 인터랙션 가능
- 버튼별 `allowedUsers` allowlist로 클릭 권한 제한

상태/승인형 플로우 구현이 훨씬 쉬워졌습니다!

## 🌐 Web 도구 보안 옵션 (2.17)

`web_search`/`web_fetch`에 URL allowlist 지원이 추가되었습니다. 웹 리서치 자동화 시 URL allowlist를 먼저 걸고 시작하면 더 안전합니다 (최소권한 원칙).

## 🛠️ 실용적인 운영 팁

### Gateway 설정 주의사항
- Gateway를 `mode=none`으로 열어둘 때는 loopback 한정 + reverse proxy ACL을 반드시 함께 적용하세요.
- `gateway.auth.mode="none"`으로 HTTP API가 노출되면 `gateway.http.no_auth` 진단 경고가 뜹니다.

### Webhook 보안
- Cron 웹훅 사용 시 Gateway 메인 토큰 대신 **`cron.webhookToken` 별도 발급** 권장
- 토큰을 쿼리스트링(`?token=`)으로 전달하는 방식은 deprecated (헤더 `Authorization: Bearer` 권장)

### 업데이트 후 점검 루틴
```bash
openclaw doctor
openclaw gateway restart
openclaw health
```

이 세 명령어로 시스템 상태를 빠르게 점검할 수 있습니다.

## 🚀 Nested Subagent 지원 (2.15)

`agents.defaults.subagents.maxSpawnDepth`로 서브에이전트의 하위 에이전트 실행이 가능해졌습니다. 복잡한 에이전트 오케스트레이션을 구현할 수 있습니다!

## 📊 Anthropic Sonnet 4.6 + 1M context beta (2.17)

OpenClaw에서 Anthropic의 최신 모델을 바로 사용할 수 있습니다:
- `anthropic/claude-sonnet-4-6`
- `params.context1m: true` (1M 컨텍스트 윈도우 베타)

장문 문서 처리나 복잡한 작업에 유용합니다.

## 💡 마치며

OpenClaw는 2월 중순 동안 **안정성**과 **보안**에 집중한 업데이트를 진행했습니다. 특히 Cron 시스템의 안정화와 SSRF 방어 강화는 프로덕션 환경에서 OpenClaw를 운영하는 사용자들에게 큰 도움이 될 것입니다.

**업데이트 권장사항:**
1. Cron 사용 중이라면 즉시 업데이트 권장
2. 업데이트 후 `openclaw doctor` 실행
3. Gateway 재시작
4. `openclaw health`로 상태 확인

OpenClaw의 다음 업데이트도 기대해주세요! 🚀

---

*참고: 2026.2.20 후보 버전의 일부 기능은 정식 릴리즈 전이므로, 실제 사용 전 릴리즈 노트를 재확인하세요.*
