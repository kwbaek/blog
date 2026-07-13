---
title: "Hermes/리오 팁: 크론에 '자원 정산 게이트'를 붙이자"
date: 2026-07-13T21:00:00+09:00
draft: false
categories: ["openclaw"]
tags: ["Hermes", "리오", "Cron", "AI Automation", "Sandbox", "Cleanup", "Reliability"]
comments: true
---

오늘 AI/ML 트렌드 글에서 다룬 주제 — 에이전트의 '완료'가 응답 생성에서 실행 정산으로 이동한다 — 는 개인 자동화에도 그대로 적용됩니다. Hermes/리오로 크론을 돌리다 보면, 최종 메시지는 멀쩡히 나왔는데 뒤에서 뭔가 계속 남아 있는 상황을 자주 만납니다. 임시 파일이 쌓이고, background 프로세스가 안 죽고, 열어 둔 파일 핸들이 다음 실행과 충돌합니다. 오늘은 이걸 막는 **자원 정산 게이트(resource settlement gate)** 를 소개합니다.

## 왜 '보고 완료'가 진짜 완료가 아닌가

블로그 크론을 예로 들어 봅니다. 글을 쓰고, git commit·push하고, hugo 빌드하고, public을 배포합니다. 여기서 "글을 잘 썼다"는 것과 "실제 공개 페이지에 반영됐다"는 것은 전혀 다른 사건입니다. 마찬가지로 트렌드 스캐너 크론에서 "요약을 만들었다"와 "중복 캐시를 갱신하고 JSON을 검증했다"는 다릅니다. 자동화가 앞 단계에서 멈추면, 매일 실행돼도 상태가 조용히 어긋납니다.

핵심은 **각 크론이 만들어 낸 부작용(side effect)을 목록화하고, 그것이 전부 정리·확정됐는지 확인한 다음에만 '완료'를 선언**하는 것입니다.

## 크론에 붙이는 정산 체크리스트

```yaml
settlement_gate:
  # 이번 실행이 만든 것들을 명시
  produced:
    - temp files under tmp/
    - background processes / subagents
    - open file handles
    - cache mutations (latest / history / posted)
    - external commits / pushes
  # 완료 전 반드시 확인
  verify:
    - temp scripts removed or archived
    - no lingering background process
    - cache JSON validated (json.tool passes)
    - newest cache entry matches what was posted
    - build succeeded AND output artifact exists
  # 실패 시
  on_incomplete:
    - do NOT report success
    - emit one blocker + one next action
```

이 게이트의 힘은 "완료"의 정의를 **논리적 종결 + 자원 정산** 두 축으로 나누는 데 있습니다. 논리적으로 글은 다 썼어도(첫 번째 축), 캐시 검증이 실패했거나 임시 스크립트가 남았으면(두 번째 축) 아직 완료가 아닙니다.

## 실전 적용 세 가지

**첫째, 임시 산출물을 추적하세요.** 캐시 갱신용으로 임시 Python 스크립트를 쓴다면, 실행 후 정리하거나 타임스탬프를 붙여 archive하세요. `tmp/` 아래에 정체불명의 파일이 쌓이면 다음 실행의 dedupe가 오염됩니다.

**둘째, 캐시 변경은 검증까지가 한 세트입니다.** JSON을 수정했다면 `python3 -m json.tool`로 문법을 확인하고, 방금 게시한 항목이 latest/history 캐시의 최신 항목과 실제로 일치하는지 되읽어 확인하세요. "썼다"가 아니라 "되읽어 확인했다"가 정산입니다.

**셋째, 빌드·배포는 결과물 존재로 증명하세요.** hugo 빌드가 성공 코드를 반환했다는 것과 새 HTML 파일이 실제로 `public/`에 생겼다는 것은 다릅니다. 파일 존재, 파일 시각, 배포 커밋 반영을 함께 확인해야 배포가 조용히 누락되는 사고를 막습니다.

기업 AI가 데모에서 '실행 정산'으로 신뢰성 기준을 옮기듯, 개인 크론도 '보고 완료'에서 '자원 정산 완료'로 옮겨가야 합니다. 정산 게이트를 붙이면 Hermes/리오 크론은 단순히 무언가를 생성하는 봇을 넘어, **자기가 벌인 일을 끝까지 책임지고 닫는 작은 운영 시스템**이 됩니다.
