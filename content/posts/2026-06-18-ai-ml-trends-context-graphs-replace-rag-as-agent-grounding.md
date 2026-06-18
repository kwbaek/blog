---
title: "AI/ML 트렌드: RAG 다음 경쟁은 권한 있는 컨텍스트 그래프다"
date: 2026-06-18T21:00:00+09:00
draft: false
categories: ["ai-ml"]
tags: ["AI", "ML", "Agents", "Enterprise AI", "RAG", "AWS", "Governance"]
comments: true
---

오늘 AI/ML 트렌드에서 가장 흥미로운 신호는 AWS가 agent의 지식 연결을 단순 검색 문제가 아니라 **권한 있는 컨텍스트 그래프** 문제로 다루기 시작했다는 점입니다. AWS Context는 조직 데이터, 관계, 권한 맥락을 agent가 MCP/API로 질의하되 IAM과 Lake Formation의 권한 경계를 상속하게 만드는 방향을 보여줍니다. 이는 enterprise RAG의 다음 단계가 “문서를 더 잘 찾기”가 아니라 “이 사용자가 지금 이 맥락을 볼 수 있는지까지 포함해 근거를 구성하기”로 이동한다는 뜻입니다.

기존 RAG는 대체로 검색 품질, chunking, embedding, reranking을 중심으로 발전했습니다. 하지만 기업 agent가 실제 업무에 들어가면 더 어려운 질문이 생깁니다. 같은 문서라도 영업팀, 법무팀, 협력사, 임원에게 보여줘도 되는 범위가 다릅니다. 프로젝트 관계, 고객 등급, 데이터 소유자, 접근 만료일, 내부 정책이 답변 가능성을 바꿉니다. 그래서 agent grounding은 점점 “문서 검색”에서 “identity-aware context resolution”으로 확장될 수밖에 없습니다.

Bedrock AgentCore의 확장도 같은 흐름입니다. 조직 지식, 웹 지식, 유료 지식 연결과 continuous learning, 운영 디버깅이 한 플랫폼 안에 묶이면 agent platform 경쟁은 모델 호출 API가 아니라 운영 레이어 경쟁이 됩니다. 장기 실행 agent는 최신 웹 정보, 내부 문서, 고객 데이터, 과거 작업 로그를 동시에 참고하려 합니다. 이때 플랫폼의 핵심 가치는 더 많은 소스를 붙이는 것이 아니라, 출처·권한·신뢰도·학습 반영 여부를 추적할 수 있게 만드는 데 있습니다.

이 변화는 AI 제품을 도입하는 기업의 평가 기준도 바꿉니다. “우리 문서를 검색해 답한다”는 데모만으로는 부족합니다. 누가 어떤 데이터에 접근했는지, 답변에 어떤 source가 쓰였는지, stale permission이 남아 있지 않은지, continuous learning으로 정책이 drift하지 않았는지 증명해야 합니다. 특히 HR, 영업, 재무, 법무처럼 권한 경계가 촘촘한 영역에서는 agent의 답변 품질보다 먼저 과권한 노출 여부가 구매 심사의 핵심이 될 가능성이 큽니다.

결국 오늘의 메시지는 명확합니다. enterprise agent의 다음 병목은 모델 성능이나 embedding 성능만이 아닙니다. 조직의 데이터 권한, 관계 맥락, 외부 지식 연결, 학습 루프를 하나의 검증 가능한 컨텍스트 레이어로 묶는 능력입니다. RAG가 “무엇을 찾았는가”의 기술이었다면, 컨텍스트 그래프는 “누가, 왜, 어떤 권한으로, 어떤 근거를 보았는가”의 기술입니다. AI/ML 시장의 경쟁축은 점점 이 운영 가능한 신뢰 레이어로 이동하고 있습니다.

참고 링크:

- AWS — Context intelligence for your data and AI agents at scale: <https://aws.amazon.com/blogs/machine-learning/context-intelligence-for-your-data-and-ai-agents-at-scale/>
- AWS — New in Amazon Bedrock AgentCore: <https://aws.amazon.com/blogs/machine-learning/new-in-amazon-bedrock-agentcore-build-agents-with-broader-knowledge-and-continuous-learning/>
