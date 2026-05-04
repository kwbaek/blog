---
title: "AI/ML 트렌드: 에이전트 인프라는 모델 선택에서 런타임 경제성으로 이동 중이다"
date: 2026-05-04T21:00:00+09:00
draft: false
categories: ["ai-ml"]
tags: ["AI", "ML", "Agents", "Inference", "Robotics", "Security", "Infrastructure"]
comments: true
---

오늘 `#ai-ml-trends`에서 가장 흥미로운 흐름은 AI/ML 경쟁의 초점이 “어떤 모델을 쓰느냐”에서 **어떤 런타임과 운영 구조로 에이전트를 굴리느냐**로 내려오고 있다는 점입니다. Reuters의 Cerebras IPO 밸류에이션, SK Hynix·MediaTek·F1 AI 파트너십, 그리고 `TokenArena`, `SAGA`, `AgentFloor`, `To Call or Not to Call` 같은 연구는 모두 같은 방향을 가리킵니다. 모델 성능은 여전히 중요하지만, 실제 제품에서는 endpoint 선택, GPU 스케줄링, 도구 호출 판단, 소형 모델 라우팅, 보안 평가가 함께 경쟁력이 됩니다.

가장 직접적인 신호는 inference 경제성입니다. `TokenArena`는 같은 모델도 제공 endpoint에 따라 정확도, tail latency, joules per correct answer가 크게 달라질 수 있음을 보여줍니다. 이는 에이전트 운영에서 “GPT 계열을 쓸까, Claude 계열을 쓸까”보다 더 낮은 레이어의 질문이 중요해진다는 뜻입니다. 같은 모델명이라도 어떤 provider, 어떤 serving SKU, 어떤 batching 정책, 어떤 latency budget에서 부르느냐에 따라 비용과 품질이 갈립니다. 에이전트가 한 작업에 수십 번 도구와 모델을 호출한다면 이 차이는 곧 제품 마진과 사용자 경험 차이가 됩니다.

두 번째 축은 workflow 단위 인프라입니다. `SAGA`는 수십~수백 LLM call을 개별 요청이 아니라 하나의 workflow로 스케줄해 KV cache 재사용과 session affinity를 최적화하려는 접근입니다. 에이전트가 길게 생각하고 여러 도구를 넘나드는 시대에는 request-level 최적화만으로 부족합니다. 이제 inference 플랫폼은 “한 번의 답변”보다 “하나의 작업 프로그램”을 어떻게 싸고 빠르게 끝낼지 고민해야 합니다. 이는 데이터베이스가 단일 쿼리보다 트랜잭션과 실행 계획을 중요하게 다루는 것과 비슷한 변화입니다.

세 번째 축은 모델 계층화입니다. `AgentFloor`는 작은 오픈웨이트 모델이 agent workflow의 어디까지 맡을 수 있는지를 16,000회 이상 평가해, 짧고 구조화된 tool-use 일부는 소형 모델로 충분할 수 있음을 보여줍니다. 여기에 `To Call or Not to Call` 연구가 제시한 necessity, utility, affordability 기준을 붙이면 다음 그림이 보입니다. 좋은 에이전트는 무조건 강한 모델과 많은 도구를 쓰는 시스템이 아니라, 언제 검색하지 않을지, 언제 작은 모델로 충분한지, 언제 비싼 frontier model로 escalate할지를 판단하는 시스템입니다.

네 번째 축은 physical AI와 보안입니다. Linkerbot의 로봇 손 밸류에이션, MediaTek의 advanced packaging 강화, interleaved vision-language reasoning traces 연구는 AI가 화면 안의 텍스트 처리에서 로봇 조작과 반도체 공급망으로 더 깊이 내려가고 있음을 보여줍니다. 동시에 visual modality 기반 VLM jailbreak와 medical RAG 챗봇 보안 사례는 멀티모달·의료·브라우저 기반 시스템에서 새로운 공격면이 생긴다는 경고입니다. 에이전트가 더 많은 입력과 권한을 가질수록 평가와 보안은 모델 출시 전 체크리스트가 아니라 상시 운영 레이어가 됩니다.

그래서 오늘의 결론은 꽤 분명합니다. AI/ML의 다음 승부처는 모델 카드의 benchmark 점수보다 **runtime economics, workflow scheduling, model routing, tool-call governance, multimodal security**를 함께 설계하는 능력입니다. 에이전트가 실제 업무와 물리 시스템으로 들어갈수록, 좋은 AI 제품은 단순히 똑똑한 모델을 붙인 제품이 아니라 비용과 지연, 권한과 복구, 안전과 감사 가능성을 한 번에 운영할 수 있는 제품이 될 것입니다.
