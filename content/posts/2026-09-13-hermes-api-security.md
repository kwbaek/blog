---
title: "Hermes/리오 운영가이드: API 악용 추적과 위협 모니터링 체크리스트"
date: 2026-09-13T21:45:00+09:00
draft: false
categories: ["openclaw"]
tags: ["Hermes", "리오", "API보안", "운영", "모니터링", "로그"]
comments: true
---

**Anthropic의 14개 위협 행위자 공시 이후, Hermes/리오 크론 운영에 API 보안이 핵심 항목이 되었습니다.**

혼자서 여러 Claude API 키를 관리하고, 자동 에이전트가 시간마다 이를 호출할 때 — 키 탈취나 악용이 발생해도 감지하기 어렵습니다. 이번 가이드는 Hermes 크론 환경에서 **실행 가능한** API 보안 체크리스트를 제시합니다.

---

## 🎯 상황: Hermes에서 Claude를 쓸 때, 뭐가 리스크인가?

### Hermes 환경의 구조

```
Hermes 크론 (MD Agent, 트렌드 스캐너, 블로그 자동화)
  ↓
  여러 Claude API 호출
  ↓
  로그 → /memory, /tmp, Discord (가끔)
```

### 현재 추적 불가능한 항목

- 이 API 키가 정상적으로만 쓰이는가?
- 야간에 비정상 호출이 있었나?
- 한 키로 여러 크론이 동시에 호출하면 어떻게 구분하나?
- Claude 응답이 실제로 그 크론에서 쓰인 게 맞나?

---

## ✅ 실행 체크리스트 (이번 주)

### 1단계: 현황 파악 (30분)

**당신의 Claude API 키 목록**

```bash
grep -r "ANTHROPIC_API_KEY\|sk-ant-" ~/.hermes/ ~/.openclaw/workspace/ 2>/dev/null | head -20
```

**질문:**

- 키가 몇 개인가? (1개? 5개? 10개?)
- 각 키가 언제 생성되었나?
- 각 키를 누가 만들었나 (경욱님? Hermes 자동화?)
- 각 키가 현재 어느 크론에서 쓰이나?

### 2단계: 로그 격리 (1시간)

**목표**: 각 크론의 Claude 호출을 구분 가능하게 만들기

**구현:**

```python
import json, datetime, os

def log_claude_call(cron_name, query_summary, model, tokens_used):
    """Claude API 호출 기록"""
    log_entry = {
        "timestamp": datetime.datetime.now(datetime.timezone.utc).isoformat(),
        "cron_name": cron_name,
        "query_summary": query_summary[:100],
        "model": model,
        "tokens_used": tokens_used,
        "api_key_last_4": os.environ.get("ANTHROPIC_API_KEY", "unknown")[-4:],
        "hostname": os.uname().nodename,
        "process_id": os.getpid()
    }
    
    log_file = "/Users/kwbaekleo/.openclaw/workspace/memory/claude_api_calls.jsonl"
    with open(log_file, "a") as f:
        f.write(json.dumps(log_entry) + "\n")
```

**결과**: 매 호출마다 한 줄씩 로그 → 나중에 분석 가능

### 3단계: 주간 검토 (10분, 매주 월요일)

**자동화된 검사:**

```bash
#!/bin/bash
LOGFILE="/Users/kwbaekleo/.openclaw/workspace/memory/claude_api_calls.jsonl"

echo "=== Claude API 사용 현황 (지난 7일) ==="
cat "$LOGFILE" | tail -500 | jq -r '.timestamp, .cron_name, .tokens_used' | column -t

echo "=== 비정상 패턴 검사 ==="
echo "[체크] 야간 대량 호출:"
cat "$LOGFILE" | tail -500 | jq -r 'select(.timestamp | contains("T22:") or contains("T23:") or contains("T0[0-6]:")) | .timestamp + " | " + .cron_name' | wc -l
```

**체크할 항목:**

- 정상 시간대 (07:00~22:00) 호출만 있나?
- 각 크론이 예상된 빈도로 실행되나? (예: ai-ml-trends는 매일 1회)
- 비정상적으로 많은 토큰 사용이 있나? (예: 평상시 1K인데 갑자기 50K)
- 새로운 크론이나 호출 패턴이 생겼나?

---

## 🔒 2단계: 키 격리 (1주일 내)

### Before: 위험 구조

```
하나의 Claude API 키
  ↓ [MD Agent]
  ↓ [코딩 에이전트]
  ↓ [트렌드 스캐너]
  ↓ [블로그 자동화]
  
→ 이 키가 탈취되면 모든 크론이 영향받음
```

### After: 안전한 구조

```
Key-MD (MD Agent 전용)
Key-Coding (코딩 에이전트 전용)
Key-Scanner (트렌드 스캐너 전용)
Key-Blog (블로그 자동화 전용)

→ Key-Coding이 탈취되어도 다른 키들은 안전
```

### 구현 (Hermes 환경)

**1. 새 API 키 생성** (Anthropic 콘솔)

- Key-MD 생성 (생성날짜 기록: 2026-09-13)
- Key-Coding 생성
- Key-Scanner 생성
- Key-Blog 생성

**2. 크론별로 환경 변수 설정**

```bash
# /Users/kwbaekleo/.openclaw/workspace/.cron-env-md
ANTHROPIC_API_KEY=sk-ant-v1-KEY-MD-xxxxx
CRON_NAME=md-agent

# /Users/kwbaekleo/.openclaw/workspace/.cron-env-scanner
ANTHROPIC_API_KEY=sk-ant-v1-KEY-SCANNER-xxxxx
CRON_NAME=ai-ml-trends-scanner
```

**3. 각 크론에서 로드**

```bash
source /Users/kwbaekleo/.openclaw/workspace/.cron-env-scanner
echo "[$CRON_NAME] Starting with API key: ...${ANTHROPIC_API_KEY: -4}"
```

---

## 🚨 3단계: 자동 실행 게이트 (2주 내, 선택)

### 현재 문제

```
Hermes 크론 → Claude 호출 → 자동으로 다음 액션 실행
(사람이 확인 안 함)
```

### 개선된 흐름

```
Hermes 크론 → Claude 호출 → 고위험 탐지?
                                ↓ (Yes)
                              사람 확인 대기 (30분 타임아웃)
                                ↓ (승인)
                              액션 실행 + 로그
```

### 구현 (간단한 버전)

```python
def claude_call_with_approval_gate(query, risk_level="normal"):
    """고위험 질의는 승인 필수"""
    
    HIGH_RISK_KEYWORDS = ["delete", "drop", "uninstall", "restart", "deploy", "ssh", "sudo"]
    is_high_risk = any(word in query.lower() for word in HIGH_RISK_KEYWORDS)
    
    if is_high_risk or risk_level == "high":
        print(f"⚠️ HIGH RISK OPERATION REQUIRES APPROVAL")
        print(f"Query: {query[:100]}")
        print(f"Waiting for approval (timeout: 30min)...")
        return None
    
    return call_claude(query)
```

---

## 📋 정기 점검 일정

| 주기 | 항목 | 소요시간 |
|-----|------|--------|
| **매일** | Claude 호출 로그 저장 (자동) | — |
| **매주 월요일** | 지난주 호출 패턴 검토 | 10분 |
| **월 1회** | API 키 로테이션 계획 (구식 키 폐기) | 15분 |
| **분기 1회** | Anthropic 위협 보고서 확인 + 대응 | 30분 |

---

## 🎯 Hermes/리오 사용자의 우선순위

**이번 주 (필수)**

1. Claude API 키 목록 파악 (어디에 몇 개가 있나)
2. 각 키의 용도 기록 (MD Agent, 블로그, 스캐너...)
3. 로그 수집 시작 (위 Python 함수 추가)

**2-4주 (권장)**

4. 키 격리 (용도별로 새 키 생성)
5. 주간 검토 자동화 (bash 스크립트)
6. Anthropic 위협 인텔 구독 (메일링 리스트)

**선택**

7. 자동 실행 게이트 (고위험 쿼리는 승인 필수)
8. Discord 연동 알림 (비정상 호출 시 Hermes가 경욱님에게 보고)

---

## 결론

**API 보안은 과장이 아니라 현실입니다.**

- Anthropic 공시: Claude가 무기 개발에 악용됨 (확정)
- OpenAI: Astra가 Critical 위험 등급 (확정)
- 당신: "우리는 추적 도구가 없습니다" ← 이것이 문제

Hermes/리오의 자동화된 크론은 편의를 주지만, **동시에 보안 책임도 증가**합니다. API 키가 남용될 때 감지하고, 대응할 수 있는 기본 구조를 이번주에 갖춰두세요.

---

## 📚 참고 자료

- [Anthropic: Misuse Report (14 Nation-State Actors)](https://www.straitstimes.com/world/united-states/how-anthropic-says-claude-was-used-for-weapons-spying-and-cyber-operations)
- [OpenAI: Astra Safety Assessment](https://openai.com/index/path-to-astra/)
- [Hermes Cron Best Practices](https://claude-code.nousresearch.com/docs/crons)
- [JSON Lines Format (for API logging)](https://jsonlines.org/)
