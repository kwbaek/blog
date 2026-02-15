---
title: "OpenClaw 업데이트 운영 체크리스트 (2026.2 버전 기준)"
date: 2026-02-15T21:00:00+09:00
draft: false
categories: ["openclaw"]
tags: ["OpenClaw", "업데이트", "운영", "체크리스트", "안정성"]
comments: true
---

OpenClaw는 빠르게 진화하는 AI 에이전트 플랫폼입니다. 2주에 한 번꼴로 새 기능과 버그 수정이 나오는데, 업데이트 후 시스템이 제대로 동작하는지 확인하는 **운영 체크리스트**가 필요합니다.

이번 글에서는 2026.2.13 업데이트를 기준으로 실전 검증 루틴을 정리해봤습니다.

## 1단계: 업데이트 전 준비

### 현재 버전 확인
```bash
openclaw --version
```

예: `2026.2.9`

### 백업 (선택)
중요한 설정이 있다면 백업:
```bash
cp ~/.openclaw/openclaw.json ~/.openclaw/openclaw.json.backup
```

## 2단계: 업데이트 실행

### npm 글로벌 설치 (가장 흔한 방식)
```bash
npm install -g openclaw@latest
```

### 소스 설치 (개발자)
```bash
cd ~/openclaw
git pull
npm install
openclaw update
```

## 3단계: 핵심 3종 체크 ⭐

업데이트 후 반드시 실행해야 하는 3가지 명령:

### 1) `openclaw doctor`
설정 파일 문제, 권한 이슈, 마이그레이션 필요 여부를 자동 진단합니다.

```bash
openclaw doctor
```

**주요 체크 항목:**
- ✅ 설정 스키마 버전 호환성
- ✅ 파일 권한
- ✅ 필수 디렉토리 존재 여부
- ✅ Deprecated 설정 사용 여부

**예시 출력:**
```
✓ Config schema version: 2026.2
⚠ Warning: channels.discord.dm.policy is deprecated
  Use channels.discord.dmPolicy instead
→ Run: openclaw doctor --fix
```

**자동 수정:**
```bash
openclaw doctor --fix
```

### 2) `openclaw gateway restart`
게이트웨이(백그라운드 서비스)를 재시작해서 새 코드를 로드합니다.

```bash
openclaw gateway restart
```

**체크 포인트:**
- 재시작 시간이 10초 이상 걸리면 로그 확인 필요
- 기존 크론잡/활성 세션이 정상 복구되는지 확인

### 3) `openclaw health` (또는 `openclaw status`)
전체 시스템 상태를 종합 점검합니다.

```bash
openclaw status
```

**주요 섹션:**
- **Gateway Status**: 실행 중 / PID / 포트
- **Channels**: Discord/Telegram/WhatsApp 연결 상태
- **Models**: 설정된 모델 프로바이더
- **Security Audit**: 보안 경고 (있으면 빨간색)

**예시 출력:**
```
Gateway Status
  Status: running (pid 12345)
  RPC probe: ok
  Listening: 127.0.0.1:18789

Channels
  ✓ telegram (1 account)
  ✓ discord (default)

Security Audit
  ⚠ CRITICAL: discord.groupPolicy=open
     → Risk: Prompt injection from strangers
     → Fix: Set groupPolicy to "pairing" or "allowlist"
```

## 보안 체크 (2026.2.13 기준)

최근 릴리즈에서 강화된 보안 항목:

### 1) Hook 인증 강화
`POST /hooks/agent`가 이제 임의 `sessionKey` override를 기본 차단합니다.

**Breaking Change:** 웹훅으로 특정 세션에 메시지 보내는 스크립트가 있다면 수정 필요.

### 2) Discord DM/Group 정책
```yaml
channels:
  discord:
    dmPolicy: pairing  # open은 위험
    groupPolicy: allowlist  # open은 프롬프트 인젝션 위험
```

**`open`은 왜 위험한가?**
- 아무나 DM/그룹 초대로 에이전트에게 명령 가능
- 악의적 프롬프트 인젝션 → 토큰 탈취, 파일 접근 등

**권장 설정:**
- **개인 용도**: `pairing` (명시적 페어링만 허용)
- **팀 용도**: `allowlist` (허용된 서버/사용자만)

### 3) Plugin 허용 범위
```yaml
plugins:
  allow: []  # 빈 배열 = 로컬 플러그인 로딩 차단
```

로컬에 `extensions/` 폴더가 있다면 명시적으로 허용 목록 관리.

## 크론잡 안정성 (2026.2.12~2.13 대폭 강화)

최근 릴리즈에서 크론 스케줄러 버그가 대거 수정되었습니다:

- ✅ 중복 실행 방지
- ✅ 재시작 후 중복 트리거 방지
- ✅ 동시 트리거 경합 조건(race condition) 수정

**체크 방법:**
```bash
openclaw cron status
```

정상이면 `Scheduler: running` 표시.

## 업데이트 후 테스트

### 1) 간단한 대화 테스트
```bash
openclaw chat "안녕?"
```

응답이 정상 오면 OK.

### 2) 웹 검색 테스트 (Brave Search 설정 시)
```bash
openclaw chat "최신 AI 뉴스 검색해줘"
```

`web_search` 도구가 정상 작동하는지 확인.

### 3) 크론잡 수동 실행
```bash
openclaw cron run <jobId>
```

특정 크론잡을 수동 실행해서 에러 없는지 확인.

## 문제 발생 시 대응

### 로그 확인
```bash
openclaw logs --follow
```

**2026.2.13 신기능:** `--local-time` 플래그로 로컬 타임존 표시
```bash
openclaw logs --local-time
```

### 롤백 (긴급)
```bash
npm install -g openclaw@2026.2.9
openclaw gateway restart
```

### 설정 초기화 (최후 수단)
```bash
mv ~/.openclaw/openclaw.json ~/.openclaw/openclaw.json.broken
openclaw configure
```

## 자주 묻는 질문 (FAQ)

### Q: 업데이트 후 Discord 봇이 응답 안 해요
**A:** `openclaw gateway restart` 후 Discord 재연결까지 5~10초 걸릴 수 있습니다. `openclaw status`로 Discord 상태 확인.

### Q: 크론잡이 2번씩 실행돼요
**A:** 2026.2.12 이전 버전 버그입니다. 최신 버전으로 업데이트하면 해결.

### Q: `doctor --fix` 실행 후 설정이 사라졌어요
**A:** `~/.openclaw/openclaw.json.bak` 백업 파일이 생성됩니다. 복원 후 수동 수정.

## 결론: 3종 체크만 기억하세요

OpenClaw 업데이트 후 반드시:

1. `openclaw doctor` (문제 진단)
2. `openclaw gateway restart` (코드 갱신)
3. `openclaw status` (상태 확인)

이 3가지만 실행하면 **95% 이상의 문제를 사전 발견**할 수 있습니다.

안정적인 AI 에이전트 운영, OpenClaw가 함께합니다. 🦁

---

**참고 링크:**
- [OpenClaw 공식 문서](https://docs.openclaw.ai)
- [GitHub Releases](https://github.com/openclaw/openclaw/releases)
- [Discord 커뮤니티](https://discord.com/invite/clawd)
