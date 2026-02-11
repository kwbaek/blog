---
title: "OpenClaw 크론으로 블로그 자동 포스팅하기: AI 비서가 매일 글을 써준다"
date: 2026-02-11T21:00:00+09:00
draft: false
categories: ["openclaw"]
tags: ["automation", "cron", "blog", "productivity", "ai-agent"]
comments: true
---

매일 블로그 글을 쓰는 것은 쉽지 않은 일입니다. 하지만 **OpenClaw의 크론 작업**을 활용하면, AI 에이전트가 자동으로 콘텐츠를 수집하고 정리해서 블로그 글을 작성할 수 있습니다. 이 글에서는 제가 실제로 사용하고 있는 자동 블로그 포스팅 시스템을 소개합니다.

## 왜 크론을 사용하는가?

OpenClaw에는 두 가지 자동화 방식이 있습니다:

### 1. Heartbeat (심장박동)

- **타이밍**: 대략 30분마다, 약간의 편차 있음
- **용도**: 여러 체크를 배치 처리 (이메일+캘린더+알림 등)
- **컨텍스트**: 최근 대화 내역 접근 가능
- **적합한 경우**: 정확한 시간이 중요하지 않을 때, 대화형 컨텍스트가 필요할 때

### 2. Cron (크론)

- **타이밍**: 정확한 스케줄 (예: 매일 오후 9시)
- **용도**: 독립적인 작업 실행
- **격리**: 메인 세션과 분리된 독립 세션
- **적합한 경우**: 정확한 시간이 중요할 때, 독립적인 작업일 때

저는 **매일 정해진 시간에 블로그 글을 작성하고 배포**해야 하기 때문에 크론을 선택했습니다.

## 실제 크론 작업 구조

제 크론 작업은 다음과 같이 구성되어 있습니다:

```json
{
  "name": "Daily Blog Post - AI/ML Trends & OpenClaw",
  "schedule": {
    "kind": "cron",
    "expr": "0 21 * * *",
    "tz": "Asia/Seoul"
  },
  "payload": {
    "kind": "agentTurn",
    "message": "매일 블로그 포스팅 시간이야! 다음 순서로 진행해줘:

1. Discord #ai-ml-trends 채널의 오늘 메시지를 읽어서 가장 흥미롭고 중요한 트렌드를 선별해
2. 선별한 트렌드로 **AI/ML 트렌드** 블로그 글 1개 작성
3. **OpenClaw** 블로그 글 1개 작성

포스팅 형식:
- Hugo 블로그, 경로: /Users/kwbaekleo/.openclaw/workspace/blog/content/posts/
- 파일명: YYYY-MM-DD-slug.md
- Front matter: title, date (KST), draft: false, categories, tags, comments: true
- 내용은 최소 500자 이상, 마크다운 활용

작성 후:
1. blog 레포에서 git add, commit, push
2. hugo 빌드
3. public/ 폴더에서 git add, commit, push (kwbaek.github.io 배포)",
    "timeoutSeconds": 600
  },
  "sessionTarget": "isolated",
  "enabled": true
}
```

### 핵심 포인트

**1. 스케줄 설정**
```json
"schedule": {
  "kind": "cron",
  "expr": "0 21 * * *",  // 매일 21시
  "tz": "Asia/Seoul"     // 한국 시간 기준
}
```

**2. Isolated Session 사용**
```json
"sessionTarget": "isolated"
```
메인 세션과 분리되어 독립적으로 실행됩니다. 작업이 완료되면 메인 세션에 결과를 알려줍니다.

**3. 명확한 지시사항**
AI 에이전트가 정확히 무엇을 해야 하는지 단계별로 명시했습니다:
- 어디서 데이터를 수집할지
- 어떤 형식으로 글을 작성할지
- 어떻게 배포할지

## 작동 방식

### 1단계: 데이터 수집

```
Discord #ai-ml-trends 채널의 오늘 메시지를 읽어서 
가장 흥미롭고 중요한 트렌드를 선별해
```

저는 Discord 채널에 AI/ML 관련 뉴스를 자동으로 수집하고 있습니다. 크론 작업이 실행되면 AI 에이전트가 이 채널을 읽고 중요한 내용을 선별합니다.

### 2단계: 콘텐츠 생성

선별한 정보를 바탕으로:
- **AI/ML 트렌드 글**: 기술 뉴스, 새로운 모델 출시, 연구 동향
- **OpenClaw 활용 글**: 실제 사용 경험, 팁, 자동화 방법

두 개의 블로그 글을 자동으로 작성합니다.

### 3단계: 자동 배포

```bash
# 1. 블로그 레포에 커밋
cd /Users/kwbaekleo/.openclaw/workspace/blog
git add content/posts/2026-02-11-*.md
git commit -m "Add daily blog posts"
git push

# 2. Hugo 빌드
hugo

# 3. 빌드된 사이트 배포
cd public/
git add .
git commit -m "Deploy blog update"
git push
```

Hugo로 빌드하고 GitHub Pages에 자동으로 배포됩니다.

## 실제 결과

이 크론 작업은 매일 밤 9시에 실행됩니다:

1. ✅ Discord에서 오늘의 AI/ML 트렌드를 자동 수집
2. ✅ 2개의 블로그 글을 자동 작성 (각 500자 이상)
3. ✅ Git 커밋 & 푸시로 자동 배포
4. ✅ 완료되면 메인 세션에 결과 통보

**저는 아무것도 하지 않아도 매일 2개의 블로그 글이 발행됩니다.**

## 다른 활용 사례

같은 방식으로 다양한 작업을 자동화할 수 있습니다:

### 📧 이메일 요약
```json
{
  "name": "Morning Email Digest",
  "schedule": { "kind": "cron", "expr": "0 9 * * *" },
  "payload": {
    "kind": "agentTurn",
    "message": "지난 24시간 동안의 중요한 이메일을 요약해서 Telegram으로 보내줘"
  }
}
```

### 📊 주간 리포트
```json
{
  "name": "Weekly Project Status",
  "schedule": { "kind": "cron", "expr": "0 18 * * 5" },
  "payload": {
    "kind": "agentTurn",
    "message": "이번 주 GitHub 활동, PR 상태, 이슈 현황을 정리해서 팀 채널에 공유해줘"
  }
}
```

### 🏋️ 운동 리마인더
```json
{
  "name": "Gym Reminder",
  "schedule": { "kind": "cron", "expr": "0 19 * * 1,3,5" },
  "payload": {
    "kind": "agentTurn",
    "message": "오늘 운동 시간이야! 지난주 기록을 확인하고 오늘의 운동 계획을 알려줘"
  }
}
```

## 크론 관리 명령어

OpenClaw CLI로 크론을 쉽게 관리할 수 있습니다:

```bash
# 크론 목록 보기
openclaw cron list

# 크론 추가
openclaw cron add --file my-cron.json

# 크론 실행 (테스트용)
openclaw cron run <job-id>

# 크론 비활성화
openclaw cron update <job-id> --enabled false

# 크론 삭제
openclaw cron remove <job-id>
```

## 팁과 주의사항

### ✅ DO

1. **명확한 지시사항**: AI가 정확히 무엇을 해야 하는지 단계별로 명시
2. **타임아웃 설정**: 복잡한 작업은 `timeoutSeconds` 늘리기
3. **Isolated Session 사용**: 메인 세션 방해하지 않도록
4. **테스트**: `openclaw cron run <job-id>`로 먼저 테스트

### ❌ DON'T

1. **모호한 지시**: "적당히 해줘"는 안 됩니다
2. **너무 짧은 타임아웃**: 기본 120초는 복잡한 작업에 부족할 수 있음
3. **메인 세션 사용**: 대화 중일 때 크론이 끼어들 수 있음
4. **에러 처리 무시**: 실패 케이스도 고려하기

## 마무리

OpenClaw 크론은 단순한 스케줄러가 아닙니다. **AI 에이전트에게 정기적인 업무를 위임**하는 강력한 도구입니다.

- 매일 반복되는 작업? → 크론으로 자동화
- 정확한 시간이 중요? → 크론
- 독립적인 작업? → Isolated session으로 실행

저는 이제 블로그 글을 직접 쓰지 않습니다. 대신 AI 에이전트가 매일 밤 자동으로 글을 작성하고 배포합니다. **여러분도 반복 작업을 크론으로 자동화해보세요!** 🚀

---

*이 글 자체도 OpenClaw 크론이 자동으로 작성했습니다. 메타하지 않나요? 😄*

### 참고 자료

- [OpenClaw 크론 문서](https://docs.openclaw.ai/cron)
- [Isolated Sessions 가이드](https://docs.openclaw.ai/sessions#isolated)
- [GitHub: OpenClaw](https://github.com/openclaw/openclaw)
