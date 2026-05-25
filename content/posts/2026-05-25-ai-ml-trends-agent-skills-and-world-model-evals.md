---
title: "AI/ML 트렌드: 에이전트 스킬과 월드 모델 평가는 운영 문제로 이동한다"
date: 2026-05-25T21:00:00+09:00
draft: false
categories: ["ai-ml"]
tags: ["AI", "ML", "Agents", "VLM", "World Models", "Evaluation", "Infrastructure", "Skills"]
comments: true
---

오늘 Discord `#ai-ml-trends` 채널에서 가장 흥미로운 흐름은 **AI 연구의 초점이 단일 모델 성능에서, 에이전트가 지식을 저장하고 멀티모달 세계를 검증하는 운영 방식으로 옮겨가고 있다**는 점입니다. Andrej Karpathy가 Anthropic pre-training team에 합류했다는 소식, SkillOpt와 agent skill negative transfer 연구, Google AI Edge Gallery의 MCP 연동, SciAtlas 과학 지식그래프, VLM과 world model 평가 논문들은 서로 다른 분야처럼 보입니다. 하지만 모두 “모델이 더 똑똑해지는 것” 이후의 질문을 다룹니다. 지식은 어디에 저장할 것인가, 어떤 스킬이 실제로 도움이 되는가, 시각·공간·시간 이해는 어떻게 검증할 것인가, 그리고 이 모든 것을 어떤 런타임에서 반복 가능하게 운영할 것인가의 문제입니다.

가장 직접적인 신호는 에이전트 스킬 연구입니다. SkillOpt는 스킬 문서를 외부 agent state처럼 add, delete, replace하며 검증 점수로 최적화하는 접근을 제안합니다. 뒤이어 올라온 Microsoft 계열 연구는 자동 생성된 스킬이 항상 좋은 결과를 내지 않고, extractor로 좋은 모델과 consumer로 좋은 모델이 다를 수 있음을 지적합니다. 이는 스킬 라이브러리를 “많이 쌓을수록 좋다”는 관점에서 벗어나게 만듭니다. 앞으로 중요한 것은 스킬의 개수보다 특정 작업군에서 실제 utility가 검증되었는지, 낡은 스킬이 negative transfer를 만들고 있지는 않은지, 스킬 업데이트가 agent behavior를 예측 가능하게 개선하는지입니다.

Google AI Edge Gallery의 MCP 연동도 같은 맥락입니다. 온디바이스 Gemma 앱이 MCP 서버를 통해 외부 도구를 호출하고, 알림과 세션 연속성을 붙이면 모바일 앱은 단순 챗봇이 아니라 작은 agent runtime이 됩니다. 이 변화는 edge AI를 모델 파일 배포 문제가 아니라 tool boundary, local state, notification, session handoff 문제로 확장합니다. 사용자가 휴대폰에서 실행하는 에이전트가 로컬 도구를 호출하고 이전 맥락을 이어갈 수 있다면, 성능 평가는 답변 품질만이 아니라 실패 시 복구, 권한 경계, 지연 시간, 배터리, 개인정보 흐름까지 포함해야 합니다.

멀티모달 연구 쪽에서는 평가 축이 더 현실적으로 변하고 있습니다. VLM post-training에서 지각과 추론을 분리하려는 연구는 긴 chain-of-thought가 부족해서가 아니라, 애초에 시각 지각 단계에서 오류가 생기는 경우가 많다고 봅니다. ETCHR은 이미지 편집기를 reasoning 보조기로 쓰며 세밀한 시각 초점과 공간 변환 병목을 줄이려 합니다. PhotoFlow는 3D 장면 안에서 카메라 위치와 구도를 에이전트가 닫힌 루프로 탐색하게 만들고, SCOPE는 FPS playable environments로 world model 능력을 평가합니다. 이는 “이미지를 보고 설명한다”를 넘어, 에이전트가 공간 안에서 조작하고 관찰하고 다시 판단하는 능력을 측정하려는 흐름입니다.

비디오 생성과 latent decoder 연구도 평가와 운영의 문제로 이어집니다. Geo-Align은 텍스트 일치만이 아니라 장면 기하와 시간 일관성 보상을 통해 비디오 생성 모델을 정렬하려 합니다. PiD는 고해상도 latent decoder 병목을 줄이고, RankE는 discrete text-to-image post-training에서 generator와 decoder를 함께 최적화해 보상 점수와 픽셀 품질 사이의 trade-off를 줄이려 합니다. 생성 모델 경쟁은 더 이상 “예쁜 샘플 몇 장”만으로 설명되지 않습니다. 물리적 일관성, decoder 비용, alignment 후 fidelity 유지, interactive evaluation이 함께 봐야 할 지표가 됩니다.

SciAtlas도 중요합니다. 43M 논문, 157M 엔티티, 3B triplet 규모의 과학 지식그래프는 deep research agent가 키워드 검색과 단순 RAG를 넘어 topology-aware retrieval과 graph reranking을 쓸 수 있게 만드는 인프라입니다. 과학 문헌 탐색에서 중요한 것은 답변 한 번이 아니라, 개념과 실험과 주장 사이의 연결을 따라가며 근거를 좁히는 과정입니다. 에이전트가 연구 보조자로 쓰이려면 검색 결과를 많이 가져오는 능력보다, 연결 구조를 보존하고 잘못된 경로를 줄이는 능력이 더 중요해집니다.

오늘의 결론은 분명합니다. AI/ML의 다음 경쟁은 모델 하나의 benchmark 점수가 아니라, 모델 주변의 운영 체계입니다. 스킬은 검증 가능한 상태가 되어야 하고, edge agent는 도구와 권한 경계를 가져야 하며, VLM과 world model은 상호작용 가능한 환경에서 평가되어야 합니다. 생성 모델은 기하·시간·decoder 비용까지 포함해 측정되어야 하고, research agent는 문헌 지식의 구조를 다룰 수 있어야 합니다. 강한 AI 시스템은 점점 “좋은 모델”보다 “좋은 모델이 계속 올바르게 작동하도록 만드는 구조”에 가까워지고 있습니다.

참고 링크:

- Karpathy joins Anthropic pre-training team: <https://techcrunch.com/2026/05/19/openai-co-founder-andrej-karpathy-joins-anthropics-pre-training-team/>
- Google AI Edge Gallery MCP integration: <https://developers.googleblog.com/a-smarter-google-ai-edge-gallery-mcp-integration-notifications-and-session-continuity/>
- SciAtlas: <https://huggingface.co/papers/2605.22878>
- SkillOpt: <https://huggingface.co/papers/2605.23904>
- Agent skill negative transfer: <https://huggingface.co/papers/2605.23899>
- From Seeing to Thinking: <https://huggingface.co/papers/2605.20177>
- Geo-Align: <https://arxiv.org/abs/2605.23903>
- SCOPE: <https://huggingface.co/papers/2605.23345>

