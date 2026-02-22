---
title: "OpenClaw 최신 업데이트: Cron 안정화, Discord 컴포넌트 v2, 그리고 보안 강화"
date: 2026-02-22T21:00:00+09:00
draft: false
categories: ["openclaw"]
tags: ["OpenClaw", "업데이트", "Cron", "Discord", "보안", "자동화"]
comments: true
---

## OpenClaw가 더 강력해졌습니다

지난 몇 주간 OpenClaw에 대규모 업데이트가 진행되었습니다. Cron 안정성 패치부터 Discord 상호작용 강화, 보안 기능 개선까지 - 운영 환경에서 꼭 필요한 기능들이 대거 추가되었습니다.

이번 포스트에서는 실전에서 바로 활용할 수 있는 주요 업데이트를 정리해드립니다.

## 1. Cron 시스템 안정화: 이제 믿고 쓸 수 있습니다

### 문제가 있었습니다

초기 Cron 시스템은 몇 가지 치명적인 문제가 있었습니다:

- **중복 실행**: 같은 작업이 여러 번 실행되는 경우
- **누락**: 예정된 작업이 실행되지 않는 경우
- **재시작 후 오동작**: Gateway 재시작 후 스케줄이 꼬이는 현상

### 이제 해결되었습니다

2월 중순 대규모 Cron 안정성 패치가 적용되면서 이 문제들이 모두 해결되었습니다. 특히:

```bash
# 정밀한 스케줄 제어
openclaw cron add "매일 정오 리포트" \
  --schedule "0 12 * * *" \
  --exact  # 정확히 정각에 실행

# 부하 분산을 위한 stagger 옵션
openclaw cron add "주간 백업" \
  --schedule "0 2 * * 0" \
  --stagger 10m  # 실행 시간을 10분 내에서 랜덤 분산
```

### Webhook 통합도 강화

Cron 작업 결과를 외부 시스템에 전달하는 webhook 기능도 개선되었습니다:

- **SSRF 방어**: private IP, 메타데이터 엔드포인트 접근 차단
- **전용 토큰**: `cron.webhookToken`으로 메인 Gateway 토큰과 분리
- **Usage 추적**: 모델·프로바이더 사용량을 webhook 페이로드에 포함

```yaml
# config.yml
cron:
  webhookToken: "cron-specific-secret-token-here"
```

## 2. Discord Components v2: 인터랙티브 메시지의 진화

Discord 메시지에 버튼, 셀렉트 메뉴, 모달 폼을 추가할 수 있는 Components v2가 추가되었습니다.

### 재사용 가능한 컴포넌트

이제 `reusable: true` 옵션으로 버튼을 여러 번 클릭할 수 있게 만들 수 있습니다:

```javascript
{
  "reusable": true,
  "blocks": [
    {
      "type": "button",
      "label": "승인",
      "style": "success",
      "allowedUsers": ["kwbaekleo"]  // 특정 사용자만 클릭 가능
    }
  ]
}
```

### 사용 예시: 승인 플로우

```bash
openclaw message send \
  --target "#work" \
  --message "배포 승인이 필요합니다" \
  --components '{
    "reusable": true,
    "blocks": [
      {"type": "button", "label": "승인", "style": "success"},
      {"type": "button", "label": "거부", "style": "danger"}
    ]
  }'
```

사용자가 버튼을 클릭하면 OpenClaw가 이벤트를 수신하고 후속 작업을 자동으로 처리할 수 있습니다.

## 3. 보안 강화: 운영 환경을 위한 필수 업데이트

### 토큰 분리 원칙

보안을 위해 토큰을 용도별로 분리할 수 있게 되었습니다:

```yaml
gateway:
  auth:
    token: "main-gateway-token"

hooks:
  token: "webhook-specific-token"

cron:
  webhookToken: "cron-webhook-token"
```

각 토큰은 서로 다른 권한과 범위를 가지므로, 하나가 유출되어도 전체 시스템이 위험해지지 않습니다.

### SSRF(Server-Side Request Forgery) 방어

Cron webhook과 outbound 요청에서 다음 대상을 자동 차단합니다:

- **Private IP 대역**: 192.168.x.x, 10.x.x.x, 172.16-31.x.x
- **Loopback**: 127.x.x.x, localhost
- **메타데이터 엔드포인트**: 169.254.169.254 (AWS/GCP/Azure)
- **특수 대역**: NAT64, 6to4, Teredo 등

공격자가 내부 네트워크를 탐색하거나 클라우드 메타데이터를 탈취하는 것을 원천 차단합니다.

## 4. 설정 마이그레이션: Streaming 키 통합

여러 곳에 흩어져 있던 스트리밍 관련 설정이 통합되었습니다:

```yaml
channels:
  discord:
    streaming: "partial"  # off | partial | block | progress
  slack:
    nativeStreaming: true  # Slack은 별도 설정 유지
```

**업데이트 후 체크:**

```bash
openclaw doctor --fix  # 자동으로 legacy 설정을 새 포맷으로 변환
```

## 5. iOS/Watch Companion: 모바일에서도 OpenClaw

알파 버전이지만 iOS와 Apple Watch 앱이 출시되었습니다!

### 주요 기능

- **Inbox 확인**: 중요한 메시지와 알림을 손목에서 확인
- **명령 전송**: 간단한 명령을 Watch에서 바로 실행
- **알림 Relay**: 중요한 작업 완료 알림을 실시간으로 수신

### 페어링 관리

```bash
# 연결된 디바이스 확인
openclaw devices list

# 오래된 디바이스 정리
openclaw devices remove <device-id>

# 대량 정리 (pending 포함)
openclaw devices clear --yes --pending
```

## 운영 체크리스트: 업데이트 후 꼭 해야 할 일

```bash
# 1. 시스템 진단
openclaw doctor

# 2. Gateway 재시작
openclaw gateway restart

# 3. 헬스 체크
openclaw health

# 4. Cron 작업 확인
openclaw cron list

# 5. 로그 확인 (로컬 타임존으로!)
openclaw logs --local-time
```

## 실전 팁: Heartbeat으로 주기적 작업 자동화

Cron 대신 Heartbeat을 사용하면 더 유연한 주기 작업을 만들 수 있습니다:

```markdown
<!-- HEARTBEAT.md -->
# 정기 점검 체크리스트

## 매일 2-4회
- [ ] 이메일 확인 (긴급 메일만)
- [ ] 캘린더 확인 (다음 24시간)

## 주 2-3회
- [ ] GitHub PR 상태
- [ ] 디스크 용량 체크

날이 저물면 조용히.
```

### Heartbeat vs Cron: 언제 무엇을 쓸까?

| 상황 | 추천 |
|------|------|
| 정확한 시간 필요 (오전 9시 정각) | Cron + `--exact` |
| 여러 체크를 묶어서 (이메일+캘린더+날씨) | Heartbeat |
| 독립된 모델/설정 필요 | Cron |
| 대화 컨텍스트 필요 | Heartbeat |

## 마무리: 운영 안정성이 우선입니다

이번 업데이트의 핵심은 **안정성**과 **보안**입니다. 화려한 신기능보다 실제 운영 환경에서 문제없이 돌아가는 것이 우선입니다.

- ✅ Cron이 안정화되어 자동화를 믿고 맡길 수 있습니다
- ✅ Discord Components로 사용자 인터랙션이 풍부해졌습니다
- ✅ 토큰 분리와 SSRF 방어로 보안이 강화되었습니다
- ✅ iOS/Watch 앱으로 언제 어디서나 접근 가능합니다

OpenClaw를 프로덕션에서 사용하고 계신다면, 이번 업데이트는 **필수**입니다.

---

**더 알아보기:**
- [OpenClaw GitHub](https://github.com/openclaw/openclaw)
- [공식 문서](https://docs.openclaw.dev)
- [Changelog 전체 보기](https://github.com/openclaw/openclaw/blob/main/CHANGELOG.md)
