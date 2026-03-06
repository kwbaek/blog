---
title: "OpenClaw v2026.3.2 실전 팁 — Secrets 워크플로와 스트리밍 설정 완전 정리"
date: 2026-03-06T21:00:00+09:00
draft: false
categories:
  - openclaw
tags:
  - OpenClaw
  - secrets
  - streaming
  - cron
  - 설정
  - 운영팁
comments: true
---

OpenClaw v2026.3.2가 안정화된 지 며칠이 지났다. 이번 릴리즈에서 조용히 바뀐 것들 중 실제 운영에서 체감하는 두 가지 — **Secrets 워크플로**와 **스트리밍 설정** — 를 집중 정리한다.

---

## Secrets 워크플로: `audit → configure → apply → reload`

v2026.2.26부터 정식화된 Secrets 관리 워크플로가 이제 꽤 안정적으로 쓸 수 있는 상태다. 기본 순서는 이렇다:

```bash
# 1. 현재 상태 점검
openclaw secrets audit --check

# 2. 미설정 항목 구성
openclaw secrets configure

# 3. 드라이런으로 영향 확인
openclaw secrets apply --dry-run

# 4. 실제 적용
openclaw secrets apply

# 5. 게이트웨이에 반영
openclaw secrets reload
```

특히 `--dry-run` 단계를 절대 건너뛰지 말 것. active surface에서 unresolved secret ref가 있으면 fail-fast로 즉시 실패 처리되기 때문에, 실수로 운영 중단이 생길 수 있다.

### 토큰 분리 원칙

운영에서 가장 중요한 건 토큰 분리다:

| 용도 | 설정 키 |
|------|---------|
| 게이트웨이 메인 인증 | `gateway.auth.token` |
| Webhook/Hooks | `hooks.token` |
| Cron 전용 | `cron.webhookToken` |

세 개를 모두 같은 값으로 쓰는 분들이 많은데, 분리하면 Cron 웹훅 연동 시 게이트웨이 마스터 토큰을 노출할 필요가 없어진다. 특히 외부 서비스와 연동하는 cron job이 있다면 `cron.webhookToken`은 꼭 별도 발급하자.

---

## 스트리밍 설정: canonical 키로 통일하기

v2026.2.22 이후 스트리밍 설정의 canonical 키가 정리됐다.

### 기본 형식

```yaml
channels:
  telegram:
    streaming: partial   # off | partial | block | progress
  discord:
    streaming: block
```

Slack은 예외:

```yaml
channels:
  slack:
    nativeStreaming: true  # Slack 전용 토글
```

`streamMode` 같은 레거시 키를 아직 쓰고 있다면:

```bash
openclaw doctor --fix
```

으로 자동 마이그레이션할 수 있다. 실제로 `openclaw doctor` 출력에서 deprecated key 경고가 뜬다면 바로 `--fix` 실행을 추천.

### 각 모드의 차이

- **off**: 스트리밍 완전 비활성. 응답이 완성된 후 한 번에 전송.
- **partial**: 청크 단위로 순차 전송. Telegram 기본값(신규 설치 기준).
- **block**: 문단/블록 단위로 묶어서 전송. Discord에서 메시지 도배 방지에 유용.
- **progress**: 진행 표시기 포함 전송. 긴 작업에서 "생각 중..." 피드백을 주고 싶을 때.

블록 단위 전송을 더 세밀하게 제어하고 싶다면:

```yaml
blockStreamingCoalesce:
  minChars: 100
  maxChars: 500
  idleMs: 300
```

`idleMs` 동안 새 청크가 없으면 묶음 전송한다. Discord에서 짧은 단어들이 따로따로 오는 현상을 방지하는 데 효과적이다.

---

## Heartbeat 운영 팁: DM 정책 명시

v2026.2.27 이후 heartbeat DM 기본값이 `allow`로 복귀됐다. DM을 원하지 않으면 명시적으로 설정해야 한다:

```yaml
agents:
  defaults:
    heartbeat:
      directPolicy: "block"
```

반대로 "특정 시간대에만 DM 허용" 같은 세밀한 제어는 현재 cron으로 우회하는 게 현실적이다.

---

## 운영 체크 루틴 (2026.3.x 기준)

업데이트 전후, 그리고 주기적으로 돌릴 루틴:

```bash
# 설정 유효성 검사
openclaw config validate --json

# 헬스 프로브 (v2026.3.1부터 내장)
curl -fsS http://127.0.0.1:18789/healthz
curl -fsS http://127.0.0.1:18789/readyz

# 보안 점검
openclaw secrets audit --check
openclaw security audit

# 세션 저장소 점검
openclaw sessions cleanup --all-agents --dry-run --json
```

`/healthz`와 `/readyz`는 v2026.3.1에서 게이트웨이에 내장된 프로브 엔드포인트다. Docker나 systemd 헬스체크에 바로 연결할 수 있어서, 컨테이너로 운영하는 분들에게 특히 유용하다.

---

## Cron: 정시성이 중요한 잡은 `--exact`

Cron 스케줄에서 정확히 정각이 중요한 잡은 `--exact` 플래그를 명시하자:

```bash
openclaw cron add "0 9 * * 1" --exact "weekly-report" -- openclaw agent "주간 리포트 생성"
```

`--exact` 없이는 스태거(jitter)가 걸릴 수 있다. 반면 배치 점검(메일·캘린더·날씨 등)은 heartbeat에 묶는 게 비용 면에서 훨씬 효율적이다. 개별 cron으로 5개 돌리는 것보다 heartbeat 하나에서 로직으로 분기하는 게 낫다.

---

## 마치며

OpenClaw가 v2026.3.x대에 들어서면서 운영 안정성 관련 기능들이 많이 성숙해졌다. Secrets 분리, 스트리밍 canonical 설정, 헬스 프로브 내장 — 이 세 가지만 제대로 정리해도 운영 중 당황할 일이 확 줄어든다.

다음 포스트에서는 **멀티에이전트 구성(sessions_spawn + subagents)** 실전 패턴을 다뤄볼 예정이다.
