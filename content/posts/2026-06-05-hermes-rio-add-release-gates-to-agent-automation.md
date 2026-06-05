---
title: "Hermes/리오 팁: 에이전트 자동화에 릴리스 게이트를 붙이기"
date: 2026-06-05T21:00:00+09:00
draft: false
categories: ["hermes"]
tags: ["Hermes", "리오", "Agent", "Automation", "Cron", "Release", "Reliability", "Workflow"]
comments: true
---

Hermes/리오 자동화를 매일 운영하다 보면 한 가지 원칙이 점점 선명해집니다. **에이전트 작업도 작은 소프트웨어 릴리스처럼 다뤄야 한다**는 것입니다. 글을 쓰는 크론, 리포트를 보내는 크론, 트렌드를 요약하는 크론은 겉으로는 “프롬프트를 실행한다”로 보이지만 실제로는 입력 수집, 초안 생성, 파일 작성, 빌드, 배포, 공개 결과 검증까지 이어지는 파이프라인입니다. 어느 한 단계라도 조용히 실패하면 사용자는 그럴듯한 결과를 받거나, 반대로 아무 결과도 받지 못합니다. 그래서 반복 자동화에는 프롬프트보다 더 중요한 릴리스 게이트가 필요합니다.

첫 번째 게이트는 입력 확인입니다. 원래 읽어야 하는 채널이나 API가 항상 열려 있다고 가정하면 자동화는 쉽게 깨집니다. 좋은 Hermes/리오 작업은 1순위 입력, 2순위 캐시, 3순위 최근 리포트를 구분합니다. 예를 들어 트렌드 글을 쓰는 작업이라면 최신 채널 메시지를 먼저 보되, 접근이 막히면 같은 날 생성된 트렌드 캐시나 일간 리포트에서 근거를 가져올 수 있어야 합니다. 중요한 것은 폴백을 쓰더라도 “새로운 척” 하지 않는 것입니다. 근거가 오래되었으면 오래되었다고 내부적으로 판단하고, 충분히 새롭지 않으면 조용히 멈출 수 있어야 합니다.

두 번째 게이트는 작성 규칙입니다. 파일명, front matter, 카테고리, 태그, 금지 표현, 최소 분량은 매번 모델의 감에 맡기면 안 됩니다. 특히 마이그레이션된 자동화에서는 사용자에게 보이는 이름과 실제 시스템 식별자를 분리해야 합니다. 사용자-facing 문구는 현재 기준인 Hermes/리오로 정리하되, 실제 파일 경로나 레거시 서비스명처럼 바꾸면 동작이 깨지는 값은 그대로 두는 식입니다. 이 구분이 없으면 글에는 새 이름을 쓰면서도 스크립트는 옛 식별자를 찾아야 하는 애매한 상태가 생깁니다.

세 번째 게이트는 빌드와 생성물 확인입니다. “Markdown 파일을 썼다”는 완료가 아닙니다. Hugo 같은 정적 사이트에서는 front matter의 시간이 미래로 들어가면 글이 생성되지 않을 수 있고, permalink 규칙 때문에 예상과 다른 디렉터리에 HTML이 생길 수도 있습니다. 따라서 완료 기준은 새 Markdown이 존재하는 것, 빌드 명령이 성공하는 것, 새 HTML이 실제로 생성되는 것, 그리고 그 HTML 안에 민감한 문자열이나 의도하지 않은 레거시 표현이 없는 것까지 포함해야 합니다.

네 번째 게이트는 배포 저장소 상태입니다. 소스 저장소와 public 배포 저장소가 분리되어 있거나 submodule로 연결되어 있으면, 소스만 push하고 끝내는 것은 절반짜리 완료입니다. generated site 저장소를 따로 commit/push하고, 필요하면 부모 저장소의 submodule pointer까지 갱신해야 실제 배포가 따라옵니다. 반대로 관련 없는 theme submodule 변경이 이미 더럽혀져 있다면 자동화가 건드리지 말아야 합니다. 릴리스 게이트는 “무엇을 커밋할지”뿐 아니라 “무엇을 건드리지 않을지”도 정합니다.

실무 템플릿은 아주 짧아도 충분합니다.

```markdown
## Release Gates
- Input gate: primary source, fallback cache, freshness check
- Draft gate: filename, front matter, category, forbidden terms
- Build gate: static build success, generated HTML exists
- Safety gate: no sensitive strings, no unintended legacy wording, no local path exposure
- Deploy gate: source commit/push, public commit/push, submodule pointer check
```

Hermes/리오의 강점은 단순히 작업을 대신하는 것이 아니라, 작업을 끝까지 검증 가능한 상태로 밀어붙일 수 있다는 데 있습니다. 에이전트 자동화가 신뢰를 얻으려면 “좋은 답변”보다 “릴리스 가능한 결과”가 중요합니다. 매일 반복되는 작은 크론부터 릴리스 게이트를 붙이면, 자동화는 운 좋게 돌아가는 스크립트가 아니라 관리 가능한 운영 시스템이 됩니다.
