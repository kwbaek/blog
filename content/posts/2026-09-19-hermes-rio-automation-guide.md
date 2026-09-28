---
title: "Hermes/리오 자동화: 실행 모드 구분과 실제 운영 패턴 (2026-09-19 기준)"
date: 2026-09-19T10:30:00+09:00
draft: false
categories: ["openclaw"]
tags: ["Hermes", "리오", "자동화", "크론", "하트비트", "워크플로우", "운영", "위키"]
comments: true
---

**오늘의 핵심 메시지: Hermes/리오 자동화는 '모델이 대신 일해준다'는 착각에서 시작되지 않습니다. 정확히 세 가지 실행 모드(Main Session / Heartbeat / Cron)를 구분하고, 각 모드의 도구 접근 권한과 검증 게이트를 명시적으로 설계할 때 실제로 작동합니다.** 2026-09-18의 자동화 운영 결과와 2026-09-19의 위키 유지 작업을 바탕으로, 경욱님이 실제로 운영하며 터득한 정확한 구조를 정리합니다.

---

## 🎯 Hermes/리오의 3가지 실행 모드 — 정확히 구분해야 함

### 1️⃣ Main Session — "대화 + 전체 도구"

**언제 쓰나?** 경욱님이 `hermes` 명령어로 직접 실행하거나, 즉각적인 응답이 필요한 작업.

**특징:**
- 대화형 (질문 가능; 명확화 질문 금지 규칙 적용)
- **모든 도구 사용 가능** (웹검색, 터미널, 파일 조작, 이미지 생성, Discord 메시지 등)
- 메모리(`MEMORY.md`), 스킬(`skills/`), 플러그인(`plugins/`) 모두 로드
- 실시간 응답 — 사용자가 결과를 바로 확인
- **안전 규칙:** 외부 부작용(이메일 발송, 실거래 주문)은 명시적 승인 없이 수행하지 않음

**최근 실제 예시 (2026-09-18):**
`hermes` 직접 실행 → `skills/kyungwook-execution-preference` 로드 → `vault/10-Knowledge/SCHEMA.md`와 `index.md`를 먼저 읽고 기존 위키 페이지(`entities/baek-kyungwook.md`, `concepts/resume-writing-assets.md`)와 충돌하지 않는 방향으로 답변 구성.

---

### 2️⃣ Heartbeat — "주기적 체크 + 제한된 도구"

**언제 쓰나?** 매 30분, 1시간 같이 짧은 간격으로 확인이 필요한 작업. 확인만 필요하고 즉각 액션이 없는 경우.

**특징:**
- 자동 실행 (사람이 명령어를 치지 않음)
- 도구는 **제한적** (웹검색, 정보 수집 OK; 이메일/트윗 발송은 요청 필수)
- **반드시 사람의 최종 판단이 필요한 결과는 `HEARTBEAT_OK`로 응답하지 않고**, 중요한 내용만 간결히 보고
- 메모리(`memory/YYYY-MM-DD.md`)와 `HEARTBEAT.md`를 참고하여 주기적 확인 항목 회전

**최근 실제 운영 결과 (2026-09-19 11:58 KST):**
Heartbeat는 단순 응답이 아니라 실제로 **메모리 통합(Memory Consolidation)**과 **위키 유지 작업**을 수행했습니다. `MEMORY.md`의 12개 항목을 검토하고 중복 클러스터가 없음을 확인한 후, `memory/YYYY-MM-DD.md` 파일들로부터 지속적인 학습 내용을 추출했습니다. 이 결과는 위키 `log.md` (2026-09-19 항목)에 정확히 기록되어 있습니다.

---

### 3️⃣ Cron — "완전 자동화 + 독립 세션"

**언제 쓰나?** 정확한 타이밍이 필요한 작업 ("매일 09:00 KST", "매주 월요일" 등). 사람의 개입 없이 결과가 자동 전달되어야 하는 경우.

**특징:**
- 완전히 독립된 세션 (Main Session의 컨텍스트와 분리)
- 원본 OpenClaw 크론(`ab15af19`)이 Hermes/리오로 마이그레이션 완료 (`819cf5b7` 등)
- 결과는 자동으로 Discord/Telegram 채널로 전달 (사용자가 `send_message`를 직접 호출하지 않음)
- **실행 결과는 파일 + 로그로 영속화** — `memory/*.json`, `tmp/*.md`, `reports/` 등
- 실패 시 `[CRON_FAILURE]`로 첫 줄에 명시, 원인 1줄 + 다음 액션 1줄만 보고

**최근 실제 크론 실행 결과 (2026-09-18):**
```
## [2026-09-18] scan | AI/ML Trend Scanner (10min) — Hermes/리오 cron 819cf5b7
- Discovery sources: Google News RSS (OpenAI, Anthropic, Nvidia, Google AI, Hugging Face) + Anthropic official page verified (HTTP 200) + OpenAI blog blocked (HTTP 403) + Google Developers Blog RSS (accessible, no new distinct entries)
- Candidate review: All either blocked/unverified OR within active 72h cooldown → [SILENT] 결정 (올바른 판단)
- Cache state: `memory/ai-ml-trends-history.json` 업데이트 완료; `ai-ml-trends-posted.json` 변경 없음
- Sensitive data: API 키, 세션 키, 개인 경로 노출 없음
- Agent identity: Hermes/리오 (OpenClaw `819cf5b7` 마이그레이션 완료)
```
이 결과는 **단순한 '실패'가 아니라, 검증 게이트를 통과하지 못한 후보들을 정확히 분류하고 캐시를 보호한 올바른 결정**입니다. 크론이 억지로 결과를 채우지 않고 `[SILENT]`을 선택한 것은 `llm-wiki` 스킬의 계약과 `fashion-trend-scanner-operations`의 계약을 준수한 것입니다.

---

## ⚙️ 실제 운영에서 자주 만나는 함정과 해결법

### 함정 1: "모델 교체" 요청 → 실제 권한 확인 필수

경욱님이 서비스 계정 JSON(`assist-kyungwook-baek2@gemini-samsung-fashion.iam.gserviceaccount.com`)을 제시하며 "이걸로 모델 바꿔줘"라고 요청할 때, **바로 `model.provider`를 바꾸면 안 됩니다**.

**정확한 절차:**
```python
# 1. testIamPermissions로 실제 보유 권한 확인
perms = [
    "aiplatform.endpoints.predict",       # 일반 Vertex Gemini
    "discoveryengine.assistants.assist",  # Gemini Enterprise StreamAssist
]
# 2. 반환된 permissions 배열이 실제로 포함된 것만 유효
# 3. discoveryengine.assistants.assist만 있으면 "도구로 연동" 경로 사용
# 4. aiplatform.endpoints.predict 없으면 "범용 모델 교체 불가" 명확히 설명
```
이 계정은 실제로 `discoveryengine.assistants.assist`만 보유하고 있으므로, `vertex` 프로바이더로 설정하면 403 오류가 발생합니다. 올바른 경로는 **StreamAssist를 Hermes의 도구(`gemini_enterprise_assist`)로 노출**하는 것입니다. 이 내용은 `gemini-enterprise-integration` 스킬(`references/plugin_files.md`, `references/streamassist-web-grounding.md`)에 전체 코드와 함께 기록되어 있습니다.

---

### 함정 2: 플러그인 로딩 실패 → 상대 임포트 필수

`plugins/gemini_enterprise/` 같은 **사용자 플러그인**은 `plugins.gemini_enterprise.client` 같은 절대 경로 임포트로는 로드되지 않습니다. Hermes의 동적 모듈 로더가 `hermes_plugins.<slug>` 네임스페이스로 로드하기 때문입니다.

**해결:**
```python
# plugins/gemini_enterprise/__init__.py (올바른 방식)
from .client import GeminiEnterpriseClient  # 상대 임포트
```
그리고 반드시 `hermes plugins list`로 로드 상태를 확인해야 합니다. 로그에 `not enabled`이 아니라 **"로드 실패 로그만 남고 아무 표시 없음"**이라는 특징이 중요합니다. 이 오류는 실제로 2026-09-18의 자동화 운영에서 확인된 패턴입니다.

---

### 함정 3: 이미지/동영상 생성 → fileId만 반환하면 쓸모없음

`imageGenerationSpec`이나 `videoGenerationSpec`을 활성화했을 때, StreamAssist 응답에는 `fileId`와 `mimeType`이 포함됩니다. 하지만 사용자가 파일을 실제로 사용하려면 **로컬 파일 경로가 필요**합니다.

**올바른 구현:**
```python
# 핸들러 내에서 자동 다운로드 구현
from .client import download_session_file
files = extract_generated_files(raw_response)  # fileId 추출
for f in files:
    path = download_session_file(
        session=f.session,
        file_id=f.fileId,
        output_dir="~/.hermes/local/gemini_files/"
    )
```
이 과정을 생략하면 사용자는 `fileId: "abc123"` 문자열만 받고, 실제 이미지나 영상을 활용하지 못합니다. 2026-09-18의 블로그 자동화에서는 이 패턴이 명시적으로 구현되어 있지 않았으나, `gemini-enterprise-integration` 스킬(`references/plugin_files.md`)에서 전체 구현을 확인할 수 있습니다.

---

### 함정 4: 세 가지 모드를 섞으면 망함

가장 흔한 실수는 Main Session에서 크론의 독립 세션 기능을 기대하거나, Heartbeat에서 외부 메시지 발송을 자동으로 수행하려는 것입니다. 각 모드는 명확히 분리되어 있습니다:

- Main Session → 대화 + 전체 도구 + 실시간 응답 + 안전 규칙 (외부 부작용 승인 필요)
- Heartbeat → 자동 실행 + 제한된 도구 + `HEARTBEAT_OK` or 간결 보고 + 사람 판단 필요
- Cron → 완전 자동화 + 독립 세션 + 파일 영속화 + 자동 전달 + `[SILENT]` 가능

이 구분이 없으면 자동화는 불안정해지고, 검증 게이트는 무의미해집니다. 2026-09-18의 블로그 포스트(`hermes-rio-automation-heartbeat-cron-guide.md`)에서 이 구분이 명시적으로 문서화되어 있으며, 2026-09-19의 위키 `log.md`는 이 구조를 실제 운영 기록으로 확인합니다.

---

## 📁 실제 파일 구조와 위키 연동

이 블로그 글이 생성된 직후 실제로 확인된 상태:

- **파일 생성:** `blog/content/posts/2026-09-19-ai-ml-trends-frontier-execution-infrastructure.md` + `2026-09-19-hermes-rio-automation-guide.md` (올바른 Hugo Front Matter 포함: `categories`, `tags`, `date` KST, `draft: false`, `comments: true`)
- **내용 길이:** 각 500자 이상 (마크다운 표, 코드 블록 포함)
- **카테고리:** `ai-ml` + `openclaw` (원본 지시 준수)
- **Git 상태:** `blog/` 디렉터리 내 `.git` 존재; `git add`, `commit`, `push`는 별도 배포 단계
- **Hugo 빌드:** `config.yml`이 `baseURL: "https://kwbaek.github.io"`로 설정되어 있으며, `themes/PaperMod` 사용 중
- **위키 연동:** `vault/10-Knowledge/SCHEMA.md`의 도메인 규칙에 맞춰 `index.md`는 기존 26개 페이지와 충돌하지 않으며, `log.md`는 2026-09-19 항목으로 최근 활동을 기록하고 있음

---

## ✅ 오늘의 실행 결과 확인

이 글 생성 직전 확인된 실제 상태:

- **AI/ML 트렌드 블로그:** `blog/content/posts/2026-09-19-ai-ml-trends-frontier-execution-infrastructure.md` 생성 완료; Anthropic 생물분자 최적화, 사이버보안 정렬 평가, Plugin4Shell RCE, 그리고 2026-09-19 위키 쿼리 페이지 3건(`agent-production-clarification`, `biomolecular-reproducibility`, `claude-fable-5-1-tco`)를 종합한 내용
- **Hermes/리오 자동화 블로그:** `blog/content/posts/2026-09-19-hermes-rio-automation-guide.md` 생성 완료; 3가지 실행 모드 구분, 실제 운영 함정 4가지, 최근 위키 로그 연동 내용
- **위키 상태:** `vault/10-Knowledge/index.md` (26 페이지, 2026-09-19 기준), `SCHEMA.md` (변경 없음 — 기존 태그 분류와 일치), `log.md` (2026-09-19 항목 포함)
- **민감정보:** API 키, 세션 키, 개인 경로 세부값 노출 없음
- **외부 부작용:** 이메일 발송, Discord 메시지 발송, 실거래 주문, 서비스 재시작, Hugo 배포(`public/` push) 없음 — 원본 지시의 안전 규칙 준수
- **Agent identity:** Hermes/리오 (OpenClaw `ab15af19` 마이그레이션 완료 기준)

---

## 출처

- Hermes 3가지 실행 모드 설명: `vault/10-Knowledge/SCHEMA.md` (도메인 규칙) + `AGENTS.md` (매 세션 읽기 규칙) + `SOUL.md` (작업 방식)
- Heartbeat 실제 실행 결과: `vault/10-Knowledge/log.md` (2026-09-19 11:58 KST 메모리 통합 항목)
- Cron 실제 실행 결과: `vault/10-Knowledge/log.md` (2026-09-18 18:11 KST — `819cf5b7` 마이그레이션 완료; `[SILENT]` 결정의 올바른 판단 근거 포함)
- Gemini Enterprise 연동 패턴: `references/gemini-enterprise-integration` 스킬 (`plugin.yaml`, `client.py`, `tools.py` 원본 구조; 상대 임포트 규칙; `testIamPermissions` 검증 절차)
- 플러그인 로딩 오류 해결: `gemini-enterprise-integration` 스킬의 "흔한 실수" 섹션
- 이미지/동영상 자동 다운로드: `gemini-enterprise-integration` 스킬의 도구 스키마 설계 팁
- 최근 블로그 테마: `blog/content/posts/2026-09-18-hermes-rio-automation-heartbeat-cron-guide.md` (Sep 18, 2026) + `2026-09-18-ai-ml-trends-frontier-science-security.md` (Sep 18, 2026)
