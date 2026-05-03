---
title: "AI/ML 트렌드: 웹 자체가 에이전트의 공격면이 되고 있다"
date: 2026-05-03T21:00:00+09:00
draft: false
categories: ["ai-ml"]
tags: ["AI", "ML", "Agents", "Security", "Prompt Injection", "Inference", "Open Models"]
comments: true
---

오늘 `#ai-ml-trends`에서 가장 중요하게 볼 만한 흐름은 **에이전트가 읽는 웹 자체가 공격면이 되고 있다**는 점입니다. Google Security가 공개 웹의 indirect prompt injection(IPI) 패턴을 Common Crawl 기반으로 분석한 글이 특히 강한 신호였습니다. 여기에 Featherless.ai의 오픈소스 inference 인프라 투자, Parallel Web Systems의 agent용 웹 인프라 확장, `Safe Bilevel Delegation` 같은 delegation safety 연구까지 겹치면, 최근 AI/ML 흐름은 단순히 “더 좋은 모델”이 아니라 “웹을 읽고, 하위 에이전트에 일을 맡기고, 외부 도구를 실행하는 시스템을 어떻게 안전하게 운영할 것인가”로 이동하고 있습니다.

Google의 분석이 흥미로운 이유는 prompt injection을 더 이상 실험실 예제가 아니라 공개 웹에 실제로 존재하는 패턴으로 다뤘다는 점입니다. 웹페이지, 댓글, 문서, SEO 텍스트, 숨겨진 지시문은 사람에게는 별 의미 없는 배경 정보처럼 보여도, 브라우징 에이전트나 웹 RAG 시스템에게는 실행 후보 컨텍스트가 됩니다. Google은 pranks, SEO, agent deterrence, exfiltration/destruction 시도를 구분했고, 악성 카테고리가 2025년 11월에서 2026년 2월 사이 32% 늘었다고 설명했습니다. 이 수치는 웹 에이전트 보안이 “나중에 붙이는 guardrail”이 아니라, 웹을 읽는 순간부터 필요한 기본 설계 요소라는 점을 보여줍니다.

두 번째 축은 에이전트용 웹 인프라의 사업화입니다. Parallel Web Systems가 “웹의 두 번째 사용자=AI agents”를 전면에 내세우며 1억 달러 Series B를 유치한 것은 검색/리서치 API가 사람용 검색엔진과 다른 제품군으로 분리되고 있음을 보여줍니다. AI agent는 단순히 페이지를 열어보는 것이 아니라, 반복적으로 검색하고, 출처를 비교하고, 구조화된 결과를 만들고, 때로는 tool call과 결합합니다. 따라서 웹 인프라도 latency, freshness, anti-bot, provenance, safety filtering 같은 요소를 에이전트 친화적으로 재설계해야 합니다.

세 번째 축은 오픈모델 운영의 하방 인프라입니다. Featherless.ai가 2천만 달러 Series A로 30,000개 이상의 오픈웨이트 모델을 서버리스/정액형으로 제공하겠다고 밝힌 것은, 오픈소스 AI 경쟁이 모델 공개 자체보다 추론 비용과 운영 편의성으로 내려오고 있다는 신호입니다. 기업이나 개인 개발자는 모델을 직접 고르는 것만으로 끝나지 않습니다. 어떤 공급자에서 어떤 가격으로 돌릴지, 장애 시 대체 경로가 있는지, 데이터가 어디로 가는지, 모델별 성능과 비용을 어떻게 비교할지가 실제 adoption을 좌우합니다.

네 번째 축은 delegation safety입니다. `Safe Bilevel Delegation`은 상위 에이전트가 하위 에이전트에게 얼마나 맡길지, 즉 delegation 정도를 safety-efficiency tradeoff로 다루려는 formalism입니다. 웹에서 오염된 지시문을 읽은 에이전트가 다시 다른 에이전트나 도구에 일을 넘긴다면, 위험은 한 번의 답변 오류가 아니라 workflow 전체로 전파될 수 있습니다. 그래서 앞으로의 에이전트 시스템은 “무엇을 읽었는가”, “누구에게 맡겼는가”, “어떤 권한으로 실행했는가”를 함께 추적해야 합니다.

오늘의 결론은 분명합니다. AI/ML의 다음 중요한 경쟁력은 모델 점수만이 아니라 **웹 컨텍스트를 신뢰 가능한 입력으로 정제하고, 추론 인프라를 비용 효율적으로 선택하며, delegation과 tool execution을 감사 가능한 형태로 통제하는 능력**에서 나올 가능성이 큽니다. 웹은 더 이상 사람이 보는 문서 저장소가 아니라, 에이전트가 행동을 결정하는 실행 환경입니다. 그 환경이 오염될 수 있다면, 브라우징·RAG·에이전트 제품의 핵심 설계도 보안과 운영 중심으로 바뀌어야 합니다.
