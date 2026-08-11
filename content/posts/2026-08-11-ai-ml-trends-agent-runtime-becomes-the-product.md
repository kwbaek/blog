---
title: "에이전트 경쟁의 중심이 모델에서 실행 표면으로 이동한다 — Workspace Agents와 Copilot SDK"
date: 2026-08-11T21:00:00+09:00
draft: false
categories: ["ai-ml"]
tags: ["에이전트", "LLM", "워크플로우", "도구호출", "엔터프라이즈AI", "오케스트레이션", "생산성"]
comments: true
---

오늘의 AI/ML 흐름에서 가장 중요한 신호는 새로운 모델의 벤치마크 점수가 아니라, 에이전트가 실제 업무 시스템 안으로 들어오는 방식이 구체화되고 있다는 점입니다. OpenAI는 ChatGPT 안에서 팀의 컨텍스트와 도구를 묶어 업무를 수행하는 workspace agents를 소개했습니다. 같은 날 GitHub는 Java 서버 코드가 Copilot 에이전트 세션을 만들고, 도구를 등록하며, 구조화된 응답을 받도록 하는 Copilot SDK for Java를 공개했습니다.

두 발표는 서로 다른 제품이지만 공통된 방향을 보여줍니다. 에이전트가 단순한 채팅창이 아니라 **상태를 가진 실행 런타임**이 되고 있다는 것입니다. Workspace agent의 핵심은 팀 자료와 연결된 컨텍스트, 반복 업무를 수행하는 도구, 그리고 사람이 결과를 검토할 수 있는 업무 표면입니다. Copilot SDK for Java는 이를 애플리케이션 서버의 관점에서 구체화합니다. Java 코드가 세션을 생성하고, 함수나 애너테이션으로 도구를 정의하고, 여러 요청을 병렬로 처리하는 agent harness를 직접 구성할 수 있습니다.

이 변화는 기업 AI 도입의 평가 기준도 바꿉니다. 과거에는 “어떤 모델이 가장 똑똑한가?”가 첫 질문이었다면, 이제는 “어떤 업무 상태를 보존하고, 어떤 도구를 호출하며, 실패했을 때 어디까지 되돌릴 수 있는가?”가 더 중요합니다. 모델의 생성 품질이 높아도 권한 범위를 넘어선 도구 호출, 중복 실행, 구조화 응답의 스키마 이탈이 발생하면 업무 자동화의 총비용은 오히려 커집니다. 반대로 모델이 조금 덜 화려하더라도 세션·도구·승인·감사 로그를 안정적으로 관리하면 운영 가치가 훨씬 커집니다.

특히 Java SDK가 BYOK와 framework-agnostic 구성을 강조한 점은 엔터프라이즈 아키텍처에 의미가 있습니다. 애플리케이션이 특정 AI 프레임워크에 종속되지 않고 모델 제공자와 실행 계층을 분리하면, 비용·보안·지연시간에 따라 라우팅을 바꿀 여지가 생깁니다. 그러나 이 유연성은 자동으로 얻어지지 않습니다. 동일한 업무 입력을 여러 모델에 재생하고, 도구 인자 정확도, 재시도 횟수, 지연시간, 토큰 사용량, 개인정보 반출 여부를 함께 측정하는 회귀 세트가 필요합니다.

실무에서는 다음 순서가 안전합니다. 먼저 에이전트가 수행할 작업을 읽기 전용과 쓰기 작업으로 나눕니다. 다음으로 도구마다 허용 인자와 승인 조건을 명시하고, 세션 ID와 실행 결과를 감사 로그에 남깁니다. 마지막으로 정상 입력뿐 아니라 타임아웃, 빈 검색 결과, 권한 오류, 중복 요청을 포함한 장애 시나리오를 반복합니다. 이 테스트를 통과한 업무만 자동 실행으로 승격하고, 나머지는 사람 검토 단계에 둬야 합니다.

결국 에이전트 제품의 경쟁력은 모델 호출 자체가 아니라, 모델을 업무 상태·도구·권한·관측성에 연결하는 실행 표면의 완성도에서 결정될 가능성이 큽니다.

**출처**

- [OpenAI, Introducing workspace agents](https://openai.com/index/introducing-workspace-agents/) — 팀 컨텍스트와 도구를 결합한 ChatGPT 업무 에이전트
- [GitHub, Using the GitHub Copilot SDK for Java](https://github.blog/engineering/using-the-github-copilot-sdk-for-java/) — Java 서버에서 에이전트 세션과 도구를 오케스트레이션하는 SDK
- [GitHub Changelog, Copilot on web expands conversation controls](https://github.blog/changelog/2026-08-10-copilot-on-web-expands-conversation-controls/) — 세션 재개와 메시지·세션별 토큰 사용량 가시화
