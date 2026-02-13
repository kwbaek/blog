---
title: "OpenClaw 크론잡으로 블로그 자동 포스팅 구축하기"
date: 2026-02-13T21:00:00+09:00
draft: false
categories:
  - openclaw
tags:
  - openclaw
  - automation
  - cron
  - blog
  - hugo
  - ai
comments: true
---

OpenClaw의 강력한 기능 중 하나는 **크론잡(Cron Job)** 시스템입니다. 저는 이 기능을 활용해 매일 밤 9시에 자동으로 블로그 포스팅을 작성하고 배포하는 시스템을 구축했습니다. 오늘은 그 과정을 공유합니다.

## 🎯 목표

- **매일 밤 9시 자동 실행**: AI/ML 트렌드 + OpenClaw 활용법 블로그 글 작성
- **Discord 채널 연동**: 1시간마다 수집된 트렌드 데이터 활용
- **Hugo + GitHub Pages 배포**: 자동 빌드 및 배포

## 📋 시스템 구성

### 1. 트렌드 수집 크론잡

먼저 1시간마다 AI/ML 트렌드를 수집하는 크론잡을 설정했습니다.

```bash
# Camofox 브라우저로 Google 검색 → Discord #ai-ml-trends 채널에 포스팅
# 매시 정각 실행
```

주요 키워드:
- AI models 2026
- LLM trends
- Anthropic, OpenAI, Google AI news
- Agentic AI

### 2. 블로그 자동 포스팅 크론잡

매일 밤 9시에 실행되는 메인 크론잡입니다.

**크론잡 설정:**
```javascript
{
  "schedule": {
    "kind": "cron",
    "expr": "0 21 * * *",  // 매일 21:00 (KST)
    "tz": "Asia/Seoul"
  },
  "payload": {
    "kind": "agentTurn",
    "message": "매일 블로그 포스팅 시간이야! ...",
    "timeoutSeconds": 600
  },
  "sessionTarget": "isolated"
}
```

## 🔧 구현 단계

### Step 1: Discord 채널에서 트렌드 읽기

OpenClaw의 `message` 도구를 활용해 Discord 채널의 메시지를 읽습니다.

```javascript
message({
  action: "read",
  channel: "discord",
  channelId: "1470423173785845965",  // #ai-ml-trends
  limit: 50
})
```

### Step 2: 트렌드 선별 및 블로그 글 작성

AI 에이전트가 자동으로:
1. 흥미롭고 중요한 트렌드 선별
2. AI/ML 트렌드 글 1개 작성 (카테고리: ai-ml)
3. OpenClaw 활용법 글 1개 작성 (카테고리: openclaw)

**Hugo 블로그 형식:**
```markdown
---
title: "글 제목"
date: 2026-02-13T21:00:00+09:00
draft: false
categories:
  - ai-ml
tags:
  - tag1
  - tag2
comments: true
---

내용...
```

### Step 3: Git 커밋 및 배포

```bash
# blog 레포지토리
cd /Users/kwbaekleo/.openclaw/workspace/blog
git add content/posts/
git commit -m "Add daily blog posts"
git push

# Hugo 빌드
hugo

# public/ 폴더 (kwbaek.github.io)
cd public/
git add .
git commit -m "Deploy blog"
git push
```

## 💡 핵심 포인트

### 1. Isolated Session 사용

크론잡은 **isolated session**에서 실행됩니다. 이렇게 하면:
- 메인 세션 히스토리와 분리
- 독립적인 컨텍스트에서 작업 수행
- 크론잡 전용 모델 설정 가능 (Sonnet 4.5로 비용 절약)

### 2. 타임아웃 설정

블로그 작성은 시간이 걸리므로 `timeoutSeconds: 600` (10분)으로 설정했습니다.

### 3. Delivery 설정

결과를 Discord #work 채널에 자동으로 알림:

```javascript
"delivery": {
  "mode": "announce",
  "channel": "discord",
  "to": "1470585978874757191"  // #work
}
```

## 📊 실제 운영 결과

**장점:**
- ✅ 완전 자동화: 손 하나 안 대고 매일 블로그 업데이트
- ✅ 최신 트렌드 반영: 1시간마다 수집된 데이터 활용
- ✅ 비용 효율적: Sonnet 4.5 사용으로 크론잡 비용 절감
- ✅ 안정적: 격리된 세션에서 실행되어 메인 작업에 영향 없음

**개선 사항:**
- ⚠️ Brave Search API 무료 플랜 제한 (월 2000건) → Camofox로 대체
- ⚠️ 가끔 Camofox 서버 연결 실패 → 자동 재시작 로직 추가 (HEARTBEAT.md)

## 🔮 향후 계획

1. **SEO 최적화**: 자동 메타 태그, OG 이미지 생성
2. **다양한 콘텐츠**: 주간 요약, 월간 리포트 추가
3. **소셜 미디어 자동 포스팅**: Twitter, LinkedIn 동시 발행
4. **Analytics 연동**: 조회수 기반 인기 주제 분석

## 마무리

OpenClaw의 크론잡은 단순한 스케줄러를 넘어 **AI 에이전트가 자율적으로 작업을 수행하는 강력한 도구**입니다. 블로그 자동화는 시작에 불과하고, 이메일 요약, 리포트 작성, 데이터 분석 등 다양한 자동화 작업에 활용할 수 있습니다.

OpenClaw를 사용하시는 분들께 이 글이 도움이 되길 바랍니다!

---

**참고 자료:**
- [OpenClaw 공식 문서](https://docs.openclaw.ai)
- [OpenClaw Cron 도구 가이드](https://docs.openclaw.ai/tools/cron)
- [GitHub: kwbaek.github.io](https://github.com/kwbaek/kwbaek.github.io)
