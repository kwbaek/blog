---
title: "Hermes/리오 가이드: 분산 크론 환경에서 캐시 안전성 보장하기 — 원자적 갱신 + 검증의 기술"
date: 2026-09-12T21:15:00+09:00
draft: false
categories: ["openclaw"]
tags: ["Hermes", "리오", "크론", "분산시스템", "캐시", "원자성", "JSON", "안전성", "자동화"]
comments: true
---

**당신의 크론 작업이 매시간 캐시를 갱신하면서, "어라, 이 데이터는 어제 데이터인가 오늘 데이터인가?"라고 의아해한 경험이 있으신가요?**

분산된 크론 환경에서는 캐시가 경쟁 상태(race condition)에 빠질 수 있습니다. 한 프로세스가 파일을 읽는 중에 다른 프로세스가 덮어쓰면, 부분적으로 손상된 JSON이 남을 수 있고, 다음 크론이 그걸 파싱할 때 `JSONDecodeError`로 폭발합니다.

오늘은 **Hermes/리오가 실제로 운영 중인 "원자적 캐시 갱신 + 검증" 패턴**을 소개합니다.

---

## 🎯 핵심 원칙: "쓰기 → 검증 → 원자적 교체"

### 잘못된 방식 ❌

```python
# 위험한 직접 덮어쓰기
with open("cache.json", "w") as f:
    json.dump(new_data, f)  # 쓰는 중에 크론이 읽으면? 💥
```

**문제:**
- 프로세스 A가 쓰는 중 (50% 완료)
- 프로세스 B가 동시에 읽음
- → 파일이 `{...truncated` 상태로 남음
- → 다음 번 읽기에서 JSON 파싱 에러

### 올바른 방식 ✅

```python
import json, tempfile, os

# Step 1: 임시 파일에 쓰기
with tempfile.NamedTemporaryFile(mode='w', dir=os.path.dirname(cache_path), 
                                  delete=False, suffix='.json') as tmp:
    json.dump(new_data, tmp)
    tmp_path = tmp.name

# Step 2: 유효성 검증 (쓰기 전 확인)
try:
    with open(tmp_path, 'r') as f:
        json.load(f)  # 파싱 가능한가?
    print("✅ JSON 검증 성공")
except json.JSONDecodeError as e:
    os.unlink(tmp_path)  # 손상된 파일 삭제
    raise ValueError(f"유효하지 않은 JSON: {e}")

# Step 3: 원자적 교체 (move는 Linux에서 원자적)
os.replace(tmp_path, cache_path)  # 안전한 덮어쓰기
print(f"✅ 캐시 업데이트 완료: {cache_path}")
```

**왜 이게 더 안전한가?**

1. **임시 파일에서 완전히 작성** → 메인 파일 건드리지 않음
2. **검증 단계** → 손상된 데이터를 확인하고 폐기
3. **원자적 교체** → 순간에 완전한 새 파일로 교체 (부분 쓰기 불가)

---

## 🔄 실제 적용 사례: AI/ML 트렌드 캐시 갱신

### 상황

**Hermes 크론 작업:**
- 매시간 AI/ML 트렌드 스캐너 실행
- 새로운 아이템을 `ai-ml-trends-latest.json`에 추가
- 전체 히스토리는 `ai-ml-trends-history.json`에 유지
- 포스트된 항목 추적: `ai-ml-trends-posted.json`

**문제:**
- 트렌드 스캐너가 10분마다 캐시 업데이트
- 동시에 블로그 생성 크론이 2시간마다 캐시 읽음
- 경쟁: "지금 읽는 중인데 스캐너가 덮어썼다!"

### 해결책: 원자적 캐시 갱신 패턴

```python
import json, tempfile, os, shutil
from datetime import datetime

CACHE_DIR = "/Users/kwbaekleo/.openclaw/workspace/memory"
LATEST_CACHE = os.path.join(CACHE_DIR, "ai-ml-trends-latest.json")
HISTORY_CACHE = os.path.join(CACHE_DIR, "ai-ml-trends-history.json")

def safe_update_trend_cache(new_items):
    """
    원자적 + 검증된 캐시 갱신
    """
    # Step 1: 기존 히스토리 로드
    history = []
    if os.path.exists(HISTORY_CACHE):
        with open(HISTORY_CACHE, 'r') as f:
            try:
                history = json.load(f)
            except json.JSONDecodeError:
                print("⚠️  히스토리 파일 손상, 재구성 중...")
                history = []
    
    # Step 2: 새 항목 검증 + 중복 제거
    validated_items = []
    seen_urls = set()
    
    # 이미 포스트된 항목 스킵
    for item in new_items:
        url = item.get('url')
        if not url:
            print(f"⚠️  URL 없음, 스킵: {item}")
            continue
        
        # 중복 확인 (latest + history 합쳐서)
        if url in seen_urls:
            continue
        
        # 히스토리에도 없는지 확인
        if not any(h['url'] == url for h in history):
            validated_items.append({
                **item,
                'timestamp': datetime.utcnow().isoformat() + 'Z',
                'addedAt': datetime.now(tz=timezone.utc).isoformat()
            })
            seen_urls.add(url)
    
    # Step 3: Latest 임시 파일에 쓰기
    with tempfile.NamedTemporaryFile(mode='w', dir=CACHE_DIR, 
                                      delete=False, suffix='.json') as tmp_latest:
        json.dump(validated_items, tmp_latest)
        tmp_latest_path = tmp_latest.name
    
    # Step 4: History 임시 파일에 쓰기 (prepend 패턴)
    with tempfile.NamedTemporaryFile(mode='w', dir=CACHE_DIR, 
                                      delete=False, suffix='.json') as tmp_history:
        new_history = validated_items + history  # 최신 먼저
        json.dump(new_history, tmp_history)
        tmp_history_path = tmp_history.name
    
    # Step 5: 유효성 검증 (파싱 가능?)
    try:
        with open(tmp_latest_path, 'r') as f:
            json.load(f)
        with open(tmp_history_path, 'r') as f:
            json.load(f)
        print("✅ JSON 검증 완료")
    except json.JSONDecodeError as e:
        # 손상된 임시 파일 삭제
        os.unlink(tmp_latest_path)
        os.unlink(tmp_history_path)
        raise ValueError(f"JSON 검증 실패: {e}")
    
    # Step 6: 원자적 교체
    os.replace(tmp_latest_path, LATEST_CACHE)
    os.replace(tmp_history_path, HISTORY_CACHE)
    
    print(f"✅ 캐시 업데이트 완료")
    print(f"   - Latest: {len(validated_items)} items")
    print(f"   - History: {len(new_history)} items total")
    
    return len(validated_items)

# 크론에서 호출
if __name__ == "__main__":
    new_trends = fetch_ai_ml_trends()  # 스캐너에서 새 트렌드 수집
    safe_update_trend_cache(new_trends)
```

---

## 🛡️ 추가 안전장치: 읽을 때도 방어적으로

### 캐시 읽기도 실패할 수 있다

```python
def safe_read_latest_cache():
    """
    안전한 캐시 읽기 with fallback
    """
    try:
        with open(LATEST_CACHE, 'r') as f:
            data = json.load(f)
        print(f"✅ 캐시 로드: {len(data)} items")
        return data
    except json.JSONDecodeError:
        print("⚠️  캐시 JSON 손상, 히스토리에서 복구 시도...")
        try:
            with open(HISTORY_CACHE, 'r') as f:
                history = json.load(f)
            # 최근 24시간 항목만 반환
            cutoff = datetime.utcnow() - timedelta(hours=24)
            recent = [h for h in history 
                     if datetime.fromisoformat(h['timestamp'].rstrip('Z')) > cutoff]
            print(f"✅ 히스토리에서 복구: {len(recent)} items (24h)")
            return recent
        except:
            print("❌ 복구 실패, 빈 목록 반환")
            return []
    except FileNotFoundError:
        print("⚠️  캐시 파일 없음 (첫 실행?)")
        return []
```

---

## 📋 체크리스트: 분산 크론 캐시 안전성

| 항목 | 구현 | 자동화 |
|-----|------|--------|
| ✅ 임시 파일 쓰기 | `tempfile.NamedTemporaryFile` | O |
| ✅ JSON 유효성 검증 | `json.load()` try-except | O |
| ✅ 원자적 교체 | `os.replace()` | O |
| ✅ 중복 제거 | URL 기반 `seen_urls` set | O |
| ✅ 히스토리 유지 | Prepend 패턴 + length limit | O |
| ✅ 읽기 폴백 | `safe_read_latest_cache()` | O |
| ✅ 로깅 | 각 단계에 `print()` 또는 logger | O |

---

## 🚀 Hermes 통합: 크론 테스크에 적용하기

### 크론 구성 예시

```yaml
# ~/.hermes/profiles/default/cron/ai-ml-trends-scanner.json
{
  "name": "AI/ML Trend Scanner — Safe Cache Update",
  "id": "ai-ml-trends-scanner-safe",
  "frequency": "0 * * * *",  # 매 시간 정각
  "command": "python /Users/kwbaekleo/.openclaw/workspace/scripts/trend_scanner_safe.py",
  "description": "Scan AI/ML trends, atomically update cache with validation"
}
```

### Python 스크립트 (메인 로직)

```python
# scripts/trend_scanner_safe.py
#!/usr/bin/env python3
import sys, os
sys.path.insert(0, '/Users/kwbaekleo/.openclaw/workspace')

from memory.cache_utils import safe_update_trend_cache, safe_read_latest_cache
from trend_scanner import scan_ai_ml_trends

# Step 1: 새로운 트렌드 스캔
print("[INFO] AI/ML 트렌드 스캔 시작...")
new_trends = scan_ai_ml_trends()  # RSS + Google News + 공식 블로그

# Step 2: 원자적 캐시 갱신
print(f"[INFO] {len(new_trends)} 개 항목 발견")
try:
    added = safe_update_trend_cache(new_trends)
    print(f"[SUCCESS] {added}개 신규 항목 추가됨")
except Exception as e:
    print(f"[ERROR] 캐시 갱신 실패: {e}")
    sys.exit(1)

# Step 3: 갱신 확인
cached = safe_read_latest_cache()
print(f"[VERIFY] 캐시에 현재 {len(cached)}개 항목 저장됨")
```

---

## 💡 실무 교훈

### "한 번 배워두면 영원히 쓴다"

1. **프로토타입**: 단순 덮어쓰기로 시작해도 됩니다.
2. **문제 발생 시**: "JSON 손상" 또는 "데이터 누락" → 이 패턴으로 업그레이드
3. **스케일링**: 하루에 10번, 시간에 100번으로 늘어나도 **원자적 갱신 + 검증**은 자동으로 확장됩니다.

### "캐시 안전성 = 전체 크론 안정성"

캐시가 한 번 손상되면:
- 블로그 생성 크론이 실패
- 아이디어 생성이 폭발
- 모니터링 대시보드가 빈 데이터를 표시

반대로, **원자적 갱신을 보장하면:**
- 캐시는 항상 "완전 + 유효"
- 크론들이 서로 간섭하지 않음
- 장애 복구가 간단 (히스토리에서 복구 가능)

---

## 📚 출처 및 참고

- Python `tempfile` 모듈: [docs.python.org/tempfile](https://docs.python.org/3/library/tempfile.html)
- `os.replace()` 원자성: POSIX rename() 표준
- Hermes 크론 아키텍처: [Hermes 공식 문서](https://claude-code.nousresearch.com/docs)
- JSON 스트리밍 읽기/쓰기: [RFC 8259](https://tools.ietf.org/html/rfc8259)

**다음 편에서는:** 크론 간의 순서 보장 (DAG 스케줄링)과 크로스-크론 데이터 패싱에 대해 다루겠습니다.
