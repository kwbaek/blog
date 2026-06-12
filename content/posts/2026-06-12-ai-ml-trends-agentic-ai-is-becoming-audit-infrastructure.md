---
title: "AI/ML 트렌드: Agentic AI는 이제 감사 인프라다"
date: 2026-06-12T21:00:00+09:00
draft: false
categories: ["ai-ml"]
tags: ["AI", "ML", "Agent", "Finance", "Security", "Governance", "LangGraph", "OpenAI", "Reuters"]
comments: true
---

오늘 AI/ML 트렌드에서 가장 중요한 신호는 **agentic AI가 실험 기능을 넘어 감사 가능한 운영 인프라로 취급되기 시작했다**는 점입니다. Reuters는 미국 은행 규제기관이 금융사의 AI 사용 감독을 강화하고 있다고 보도했고, Check Point Research는 LangGraph checkpointer 취약점 체인이 self-hosted AI agent의 원격 코드 실행으로 이어질 수 있음을 공개했습니다. 여기에 OpenAI와 Preply의 하이브리드 튜터 사례는 AI가 사람을 완전히 대체하기보다 전문가 워크플로우 안에서 운영되는 형태가 현실적인 제품 패턴이 되고 있음을 보여줍니다.

금융권 감독 강화는 AI 도입의 질문을 바꿉니다. “이 모델이 정확한가?”만으로는 부족하고, 누가 어떤 데이터로 어떤 판단을 실행했는지, 승인권자는 누구였는지, 실패 시 어떤 로그로 재구성할 수 있는지가 중요해집니다. 특히 agentic AI가 리서치 보조를 넘어 고객 응대, 리스크 판단, 주문 전 단계, 내부 운영 자동화에 붙으면 모델 리스크 관리와 업무 권한 관리가 한 묶음이 됩니다. 금융사는 AI를 기능 단위로 사는 것이 아니라 승인, 감사, 보존, 재현성을 포함한 운영 패키지로 평가하게 될 가능성이 큽니다.

LangGraph checkpointer 이슈는 같은 메시지를 보안 관점에서 보여줍니다. 에이전트의 memory와 checkpoint는 단순한 저장소가 아닙니다. 장기 실행 에이전트에서는 다음 행동을 결정하는 상태이며, 외부 입력과 도구 호출 결과가 섞이는 경계면입니다. 이 계층이 오염되거나 취약하면 프롬프트 인젝션을 넘어 실제 실행 권한 탈취로 이어질 수 있습니다. 에이전트 보안은 모델 응답 필터링보다 넓어져서 상태 저장소 패치, 직렬화 형식, 권한 분리, 네트워크 격리, 로그 무결성까지 포함해야 합니다.

Preply 사례도 흥미롭습니다. AI 튜터가 모든 교육을 자동화한다는 이야기보다, 인간 튜터가 AI를 활용해 학습 개인화와 운영 효율을 높이는 구조가 더 설득력 있습니다. 이것은 금융과 보안의 흐름과도 연결됩니다. 고위험 도메인에서 AI는 독립된 마법 상자가 아니라 인간 전문가, 승인 절차, 감사 로그 안에 들어간 운영 구성요소가 됩니다.

결국 2026년의 AI/ML 경쟁력은 더 똑똑한 에이전트만으로 결정되지 않습니다. 에이전트가 어떤 상태를 저장하고, 어떤 권한으로 행동하며, 어떤 증거를 남기고, 언제 사람에게 넘기는지를 설계하는 팀이 더 신뢰받을 것입니다. 오늘의 키워드는 모델 성능이 아니라 **auditability by design**입니다.

참고 링크:

- Reuters: US bank regulators ramp up scrutiny of AI use: <https://www.reuters.com/business/finance/us-bank-regulators-ramp-up-scrutiny-ai-use-financial-companies-2026-06-12/>
- Check Point Research: LangGraph checkpointer exploitation: <https://research.checkpoint.com/2026/from-sqli-to-rce-exploiting-langgraphs-checkpointer/>
- OpenAI/Preply: AI and human tutors workflow: <https://openai.com/index/preply/>
