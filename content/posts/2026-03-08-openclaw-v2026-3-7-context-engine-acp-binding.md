---
title: "OpenClaw v2026.3.7: Context Engine 플러그인, ACP 바인딩 영속화, 그리고 주의할 Breaking Change"
date: 2026-03-08T21:30:00+09:00
draft: false
categories:
  - openclaw
tags:
  - openclaw
  - update
  - context-engine
  - acp
  - gemini
  - discord
  - config
comments: true
---

오늘(2026-03-08) OpenClaw v2026.3.7이 출시됐다. 이번 릴리즈는 조용해 보이지만 몇 가지 중요한 변화가 포함돼 있다. 특히 **Breaking Change가 하나** 있으므로 업데이트 전 꼭 확인하자.

## ⚠️ Breaking Change: `gateway.auth.mode` 명시 필수

가장 먼저 확인해야 할 내용이다.

`gateway.auth.token`과 `gateway.auth.password`를 **둘 다 설정한 경우**, 이제 `gateway.auth.mode`를 반드시 명시해야 한다.

```yaml
gateway:
  auth:
    token: "your-token"
    password: "your-password"
    mode: token  # 또는 password — 이제 필수!
```

두 값이 모두 설정돼 있는데 `mode`가 없으면 게이트웨이가 어떤 인증 방식을 써야 할지 모호해진다. 업그레이드 전 config를 확인하고 `mode`를 명시해두자.

확인 방법:
```bash
openclaw config validate --json
```

## 🔌 Context Engine 플러그인 슬롯 신설

이번 릴리즈의 가장 흥미로운 기능이다. `ContextEngine` 플러그인 슬롯이 생겼다. 이는 **OpenClaw가 대화 컨텍스트를 관리하는 방식 자체를 교체할 수 있게 됐다**는 의미다.

기본 동작은 변화 없다. 하지만 커스텀 컨텍스트 전략이 필요한 경우 — 예를 들어 토큰 사용을 극적으로 줄이는 컨텍스트 압축 전략, 혹은 도메인 특화 메모리 관리 — 이제 플러그인으로 직접 구현할 수 있다.

커뮤니티에서는 이미 `lossless-claw` 같은 플러그인이 언급되고 있다. Context Engine이 오픈소스 생태계에서 어떻게 발전할지 주목할 만하다.

## 🔗 ACP 채널 바인딩 영속화

이전까지 ACP 세션을 Discord 채널이나 Telegram 토픽에 바인딩하면, **게이트웨이를 재시작할 때마다 바인딩이 초기화**되는 문제가 있었다.

v2026.3.7부터는 이 바인딩이 영속화된다. 재시작 후에도 채널 바인딩이 유지되므로, 장기 실행 ACP 세션이나 팀용 채널 구성을 훨씬 안정적으로 운영할 수 있다.

Discord 스레드 기반 서브에이전트를 많이 쓰는 분들에게 특히 반가운 업데이트다.

## 🌟 Gemini 3.1 Flash-Lite 공식 지원

OpenClaw에서 `google/gemini-3.1-flash-lite-preview`를 이제 1st-class로 사용할 수 있다.

Gemini 3.1 Flash-Lite는 오늘 Google이 공식 출시한 경량 모델로, Pro 대비 1/8 비용에 번역·태깅·모더레이션 같은 대규모 반복 처리 작업에 최적화돼 있다. Heartbeat나 정기 cron 작업처럼 비용 민감한 주기적 태스크에 써볼 만하다.

설정 방법:
```yaml
# cron 잡에 비용 효율적인 모델 사용
cron:
  jobs:
    - name: daily-summary
      model: google/gemini-3.1-flash-lite-preview
      schedule: "0 9 * * *"
```

## 🧹 Browser 세션 정리 개선

세션이 종료될 때 브라우저 탭이 자동으로 닫히지 않아 메모리 누수가 생기는 문제가 이번에 수정됐다. 브라우저 자동화를 많이 사용하는 환경에서 장시간 실행 시 안정성이 높아진다.

## 🔒 Cron 파일 권한 자동 강화

기존 cron 디렉토리 포함해서, 이제 cron 관련 파일에 0600/0700 권한이 자동으로 적용된다. 별도 조치 없이 보안이 강화된 것이다.

## 업데이트 후 권장 체크 루틴

```bash
# 1. 업데이트
openclaw update

# 2. config 유효성 확인 (Breaking Change 체크)
openclaw config validate --json

# 3. 게이트웨이 재시작
openclaw gateway restart

# 4. 헬스 확인
curl -fsS http://127.0.0.1:18789/healthz
curl -fsS http://127.0.0.1:18789/readyz
```

## 정리

v2026.3.7은 조용하지만 실속 있는 릴리즈다. Context Engine 플러그인 슬롯은 OpenClaw의 확장성을 한 단계 높이는 구조적 변화고, ACP 바인딩 영속화는 실무에서 반복적으로 겪던 불편함을 해결했다. Gemini Flash-Lite 지원으로 비용 최적화 선택지도 늘었다.

다만 `gateway.auth.mode` Breaking Change는 인지하지 못하면 업그레이드 후 인증 문제가 생길 수 있으니, 업데이트 전 반드시 config를 확인하자.

---

*참고: [OpenClaw GitHub Releases](https://github.com/openclaw/openclaw/releases) · [OpenClaw Docs](https://docs.openclaw.ai)*
