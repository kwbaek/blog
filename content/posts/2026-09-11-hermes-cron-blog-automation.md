---
title: "Hermes/리오 가이드: 크론 자동화로 매일 블로그 포스트 발행하기"
date: 2026-09-11T10:40:00+09:00
draft: false
categories: ["openclaw"]
tags: ["Hermes", "리오", "크론", "자동화", "블로그", "Hugo", "워크플로우", "생산성"]
comments: true
---

**"매일 블로그 포스트를 쓰고 싶은데, 손으로 쓰기엔 너무 시간이 오래 걸린다."**

이것이 콘텐츠 제작자들의 공통 고민입니다. Hermes/리오의 크론 자동화를 활용하면, **AI가 매일 트렌드를 수집 → 분석 → 포스트 작성 → 발행까지 자동으로 처리**할 수 있습니다.

오늘은 경욱님이 실제로 운영하고 있는 **"매일 블로그 포스트 자동 생성 및 배포" 워크플로우**를 상세히 소개합니다.

---

## 🎯 전체 아키텍처: "주제 선택 → 작성 → 빌드 → 배포"

```
┌─────────────────────────────────────────────────────────────┐
│                  Hermes/리오 Daily Cron                      │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Step 1: Discord 트렌드 채널 읽기                            │
│  └─ #ai-ml-trends 채널의 오늘 메시지 수집                   │
│     (Discord 게이트웨이 연동)                                │
│                                                              │
│  Step 2: 트렌드 분석 및 선별                                 │
│  └─ 1주일 내 중복 아이템 제외                                │
│  └─ 가장 영향력 있는 5개 항목 선정                           │
│                                                              │
│  Step 3: Hugo 포스트 작성                                    │
│  ├─ AI/ML 트렌드 포스트 1개 생성                             │
│  │  (카테고리: ai-ml, 최소 500자)                           │
│  │                                                           │
│  └─ Hermes/리오 팁 포스트 1개 생성                           │
│     (카테고리: openclaw, 최소 500자)                        │
│                                                              │
│  Step 4: Git 커밋 및 푸시                                    │
│  ├─ /workspace/blog에 git add/commit/push                   │
│  │                                                           │
│  └─ Hugo 빌드                                                │
│     hugo build → public/ 생성                               │
│                                                              │
│  Step 5: public/ 레포에 푸시                                 │
│  └─ kwbaek.github.io로 자동 배포                            │
│                                                              │
│  Step 6: 결과 리포트                                         │
│  └─ 성공/실패 여부 및 포스트 링크 반환                       │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## 1️⃣ Step 1: Discord 트렌드 채널에서 오늘 메시지 수집

### Discord 게이트웨이 연동

Hermes는 **Discord 게이트웨이** 기능을 통해 특정 채널의 메시지를 자동으로 읽을 수 있습니다.

```yaml
# Hermes 설정 예시 (config.yaml)
gateway:
  discord:
    token: "..." # 토큰은 .env에 저장
    channels:
      ai_ml_trends: 1470423173785845965  # 채널 ID
```

### 크론 스크립트에서 활용

```python
# Hermes SDK 또는 로컬 도구로
import hermes

# 채널 메시지 읽기
channel_id = 1470423173785845965
messages = hermes.gateway.discord.read_channel(
    channel_id=channel_id,
    since=datetime.now() - timedelta(days=1)  # 지난 24시간
)

# 메시지 필터링 (봇 메시지 제외 등)
trend_messages = [m for m in messages if m.author != "bot" and len(m.content) > 50]
```

### 주요 체크 항목

- **시간 필터:** 오늘(KST 기준) 메시지만 수집
- **품질 필터:** 단순 반응("+1", "👍") 제외, 의미있는 메시지만 선정
- **출처 검증:** URL 또는 소스 명시된 메시지 우선

---

## 2️⃣ Step 2: 트렌드 분석 및 선별

### 왜 "선별"이 필요한가?

**문제:** Discord 채널에 메시지가 100개 이상 올 수 있음
- 중복 아이템 많음 (같은 뉴스를 여러 사람이 공유)
- 저품질 메시지 섞여 있음
- 긴 글 vs 짧은 글 섞여 있음

**해결:** Hermes의 **로컬 캐시 + AI 필터링**

```python
# 로컬 캐시 활용 (deduplication)
import json

with open("memory/ai-ml-trends-posted.json") as f:
    posted_cache = json.load(f)  # 지난 7일간 발행한 항목들

# 새 항목만 필터링
new_items = [
    m for m in trend_messages
    if m.url not in posted_cache.get("urls", [])
]

# AI 필터링: 중요도 점수 매기기
# (Hermes AI가 각 메시지의 영향도 평가)
scored_items = hermes.ai.score_importance(new_items)

# 상위 5개만 선정
top_5 = sorted(scored_items, key=lambda x: x["score"], reverse=True)[:5]
```

### 캐시 구조 (로컬 파일)

```json
{
  "urls": [
    "https://openai.com/...",
    "https://github.com/deepseek-ai/...",
    "..."
  ],
  "topics": [
    "GPT-Live-1",
    "음성 에이전트",
    "..."
  ],
  "last_updated": "2026-09-11T10:35:00+09:00"
}
```

**다음 크론 실행 시:**
1. 새 메시지 중 이미 `urls` 배열에 있는 건 제외
2. 같은 `topic`으로 그룹화된 것도 1개만 선정
3. 결과: 진정한 새로운 트렌드만 블로그에 게시

---

## 3️⃣ Step 3: Hugo 포스트 작성 (AI 자동화)

### 포스트 구조

```markdown
---
title: "제목"
date: 2026-09-11T10:35:00+09:00
draft: false
categories: ["ai-ml"]  # 또는 ["openclaw"]
tags: [태그들]
comments: true
---

포스트 본문 (마크다운)
```

### Hermes AI의 작성 패턴

**AI/ML 포스트 작성 지시:**

```
역할: 기술 블로거
목표: 선정된 5개 트렌드를 하나의 통합 포스트로 작성

요구사항:
1. 제목: 이번 주의 메가 트렌드를 한 문장으로 요약
2. 본문 구성:
   - 핵심 요약 테이블 (트렌드 | 기술적 의미 | 비즈니스 의미)
   - 각 트렌드별 섹션 (3-5개 문단)
   - 기술 설명 + 실제 적용 사례 + 비즈니스 임팩트
   - 출처 링크 명시 (마크다운 링크 형식)
3. 길이: 최소 500자, 권장 1500자 이상
4. 문체: 한국어, 격식체, 전문가 톤
5. 마크다운 활용:
   - 제목 계층 (##, ###)
   - 강조 (**굵음**, *이탤릭*)
   - 코드 블록 (기술 예시용)
   - 테이블 (비교용)
   - 이모지 활용 (읽기 쉽게)
```

**Hermes/리오 팁 포스트 작성 지시:**

```
역할: Hermes/리오 운영 가이드 작성자
목표: 경욱님이 실제로 운영하는 Hermes 자동화 팁 1개 소개

요구사항:
1. 주제: 크론 자동화, 워크플로우, 팁/트릭 중 하나
2. 구성:
   - 문제 제시 ("매일 손으로 ~을 하는 게 번거롭다")
   - 해결책 (Hermes 기능 소개)
   - 기술 설명 (아키텍처 다이어그램 또는 코드 예시)
   - 실제 운영 사례 (경욱님의 실제 사용 경험)
   - 다음 스텝 (더 나은 운영 방법)
3. 길이: 최소 500자
4. 문체: 카주얼하되 전문가 톤
5. 민감정보 제외: API 키, 실제 채널 ID 등은 제거 또는 마스킹
```

### 자동 작성 실행

```bash
# Hermes 크론에서 실행
hermes ai generate-blog-post \
  --trends <selected_5_trends_json> \
  --category ai-ml \
  --output /workspace/blog/content/posts/2026-09-11-ai-ml-trends-*.md

hermes ai generate-blog-post \
  --topic "크론 자동화" \
  --category openclaw \
  --output /workspace/blog/content/posts/2026-09-11-hermes-cron-*.md
```

---

## 4️⃣ Step 4: Git 커밋 및 Hugo 빌드

### 파일명 생성

```
포스트 파일명 규칙: YYYY-MM-DD-<slug>.md

예시:
2026-09-11-ai-ml-trends-multimodal-agents-cost-breakthrough.md
2026-09-11-hermes-cron-blog-automation.md
```

### Git 커밋

```bash
cd /Users/kwbaekleo/.openclaw/workspace/blog

# 1. 새 파일 추가
git add content/posts/2026-09-11-*.md

# 2. 커밋 메시지
git commit -m "posts: Add daily blog posts - 2026-09-11

- AI/ML Trends: Multimodal agents, cost breakthrough
- Hermes Guide: Daily blog automation with cron
"

# 3. 푸시 (main 브랜치)
git push origin main
```

### Hugo 빌드

```bash
# Hugo 설치 확인
which hugo

# 빌드 실행
hugo build
# 또는
hugo

# 결과: ./public/ 디렉토리 생성
# (정적 HTML 파일들)
```

### 빌드 검증

```bash
# public 디렉토리 확인
ls -la public/ | head

# 생성된 포스트 HTML 확인
cat public/posts/2026-09-11-ai-ml-trends-*.html | grep "<title>"
```

---

## 5️⃣ Step 5: public/ 레포 푸시 (실제 배포)

### 왜 두 개의 git 레포?

| 레포 | 용도 | 브랜치 | 업데이트 주기 |
|-----|-----|--------|------------|
| **blog** | Hugo 소스 | main | 매일 (포스트 작성) |
| **kwbaek.github.io** | 정적 사이트 호스팅 | gh-pages (또는 main) | 매일 (빌드 결과) |

### GitHub Pages 배포 흐름

```
blog (소스) 
  └─ hugo build 
    └─ public/ (빌드 결과)
      └─ git push → kwbaek.github.io
        └─ GitHub Pages 자동 배포
          └─ https://kwbaek.github.io 라이브
```

### 실행 명령

```bash
# blog 레포에서 public/ 폴더를 kwbaek.github.io로 푸시
cd /Users/kwbaekleo/.openclaw/workspace/blog

# 빌드
hugo

# public/ 폴더의 변경사항을 다른 레포(kwbaek.github.io)에 커밋
cd public
git add .
git commit -m "chore: Update site for 2026-09-11 posts"
git push origin gh-pages  # 또는 main (설정에 따라)
cd ..
```

**또는 submodule 활용:**

```bash
# blog 레포의 .gitmodules에 정의된 submodule
git submodule update --remote

# 그 후 git push
```

---

## 6️⃣ Step 6: 결과 리포트 및 모니터링

### 크론 실행 결과 반환

```json
{
  "status": "success",
  "timestamp": "2026-09-11T10:45:00+09:00",
  "posts_created": 2,
  "posts": [
    {
      "title": "AI/ML 트렌드: 음성 에이전트, 다국어 번역, 그리고 금융 AI의 진화",
      "file": "2026-09-11-ai-ml-trends-multimodal-agents-cost-breakthrough.md",
      "url": "https://kwbaek.github.io/posts/2026-09-11-ai-ml-trends-multimodal-agents-cost-breakthrough/",
      "word_count": 2847
    },
    {
      "title": "Hermes/리오 가이드: 크론 자동화로 매일 블로그 포스트 발행하기",
      "file": "2026-09-11-hermes-cron-blog-automation.md",
      "url": "https://kwbaek.github.io/posts/2026-09-11-hermes-cron-blog-automation/",
      "word_count": 1950
    }
  ],
  "git_commits": [
    "blog: Add daily posts (blog repo)",
    "site: Update for 2026-09-11 (kwbaek.github.io)"
  ],
  "build_time_seconds": 3.24,
  "deployment_status": "live"
}
```

### 모니터링 및 에러 처리

**문제 1: Discord 채널 읽기 실패**
```
→ 대응: 로컬 캐시에서 지난주 트렌드 활용
→ 보고: "Discord gateway unavailable; using cached trends"
```

**문제 2: Hugo 빌드 실패**
```
→ 대응: 마크다운 문법 검증 → 오류 수정 → 재빌드
→ 보고: "Build failed; attempting syntax fix"
```

**문제 3: Git 푸시 실패 (충돌)**
```
→ 대응: git pull → merge → 재푸시
→ 보고: "Git conflict resolved; redeployed"
```

---

## 📋 실제 크론 설정 (Hermes config)

```yaml
crons:
  daily_blog_post:
    id: "ab15af19-a956-46ad-958f-03cc1068e085"
    name: "Daily Blog Post - AI/ML Trends & Hermes"
    schedule: "0 10 * * *"  # 매일 10:00 KST
    timezone: "Asia/Seoul"
    
    steps:
      - name: "Read Discord trends"
        action: "discord.read_channel"
        params:
          channel_id: 1470423173785845965
          hours_back: 24
      
      - name: "Filter and select top 5"
        action: "ai.deduplicate_and_score"
        params:
          cache_file: "memory/ai-ml-trends-posted.json"
          top_k: 5
      
      - name: "Generate AI/ML blog post"
        action: "ai.generate_content"
        params:
          template: "blog_post_ai_ml"
          output_dir: "blog/content/posts"
      
      - name: "Generate Hermes tip blog post"
        action: "ai.generate_content"
        params:
          template: "blog_post_hermes"
          output_dir: "blog/content/posts"
      
      - name: "Commit to blog repo"
        action: "git.commit_and_push"
        params:
          repo_path: "blog"
      
      - name: "Hugo build"
        action: "shell.execute"
        params:
          command: "cd blog && hugo build"
      
      - name: "Deploy to GitHub Pages"
        action: "git.commit_and_push"
        params:
          repo_path: "blog/public"
          target_remote: "origin/gh-pages"
    
    notification:
      on_success: "Discord"
      channel: "1470423173785845965"  # 또는 다른 채널
      message: "✅ Daily blog posts published!"
    
    error_handling: "retry_with_fallback"
    retry_count: 3
    fallback_source: "local_cache"
```

---

## 🎯 핵심 요점 정리

### 자동화의 장점

1. **시간 절약:** 매일 1시간 작성 시간 → 자동화로 0시간
2. **일관성:** 매일 같은 시간에 포스트 발행 (휴일도 무관)
3. **데이터 기반:** 실제 트렌드 데이터 기반 선정 (임의성 제거)
4. **확장성:** 향후 더 많은 채널/주제 추가 용이

### 주의 사항

1. **중복 방지:** 캐시 파일을 매번 업데이트할 것
2. **품질 검증:** 월 1-2회 생성된 포스트 직접 확인
3. **Git 충돌:** 수동 포스트 작성 시 자동화와 겹치지 않도록
4. **민감정보:** API 키, 개인 정보는 포스트에 절대 노출 금지

### 다음 단계

1. **피드백 루프:** 독자 반응(댓글, 공유) 모니터링 → 포스트 주제 조정
2. **다중 언어:** 영문 포스트도 자동 생성?
3. **소셜 미디어:** Twitter, LinkedIn에도 자동 공유?
4. **뉴스레터:** 주간 요약 뉴스레터 자동 발송?

---

**🦁 이 가이드는 경욱님이 실제로 운영하는 Hermes 자동화 워크플로우를 정리한 것입니다.**

매일 오전 10:00 KST마다 이 크론이 실행되어, 블로그가 자동으로 업데이트됩니다.

궁금한 점이 있으신가요? Hermes 콘솔에서 `hermes cron logs <job-id>`로 실시간 로그를 확인할 수 있습니다.

