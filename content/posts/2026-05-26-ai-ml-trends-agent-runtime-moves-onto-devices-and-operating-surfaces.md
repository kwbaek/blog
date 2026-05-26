---
title: "AI/ML 트렌드: 에이전트 런타임은 디바이스와 운영 표면으로 내려온다"
date: 2026-05-26T21:00:00+09:00
draft: false
categories: ["ai-ml"]
tags: ["AI", "ML", "Agents", "On-device AI", "Multimodal", "Developer Tools", "Edge AI", "RAG"]
comments: true
---

오늘 Discord `#ai-ml-trends` 채널에서 가장 눈에 띈 흐름은 **에이전트가 클라우드 챗봇 안에 머무르지 않고, 모바일 디바이스, 개발자 터미널, 운영 UI, 연구 워크플로로 퍼지고 있다**는 점입니다. Google의 ADK for Kotlin/Android, LiteRT-LM, Gemini CLI의 Antigravity CLI 전환, Hugging Face의 generative UI 모델과 personal assistant benchmark, GitHub Copilot의 semantic issue search, 그리고 여러 multimodal agent 연구는 서로 다른 소식처럼 보입니다. 하지만 한 문장으로 묶으면 “모델 성능 다음의 전쟁터는 에이전트가 실제 환경에서 어디에 붙고 어떻게 움직이는가”입니다.

가장 현실적인 변화는 on-device agent 쪽입니다. Google이 ADK를 Kotlin/Android로 확장한 것은 agent framework가 Python 서버나 notebook에 갇혀 있지 않다는 신호입니다. Android 앱 안에서 tool use, workflow, 사용자 확인 단계를 직접 구성할 수 있다면, 개인화된 모바일 agent는 단순 알림 요약을 넘어 앱 내부 작업 흐름으로 들어갑니다. 동시에 LiteRT-LM은 on-device Gemma 4 실행을 빠르게 만들기 위해 동적 로딩, multi-token prediction, constrained decoding 같은 최적화를 묶고 있습니다. agent가 매번 클라우드 왕복을 해야 한다면 latency와 비용, 프라이버시가 병목이 됩니다. edge inference가 개선될수록 “작은 작업은 디바이스에서 처리하고, 큰 판단만 클라우드로 넘기는” 하이브리드 설계가 더 자연스러워집니다.

개발자 도구도 같은 방향으로 움직입니다. GitHub Copilot Chat의 semantic issue search는 레포 운영에서 agent가 맥락을 찾는 시간을 줄입니다. 키워드 검색으로 이슈를 뒤지는 대신 자연어 의도에 맞는 이슈를 찾을 수 있으면, bug triage, regression 조사, release note 작성 같은 작업의 첫 단계가 짧아집니다. Gemini CLI가 Antigravity CLI로 전환되는 흐름도 중요합니다. CLI는 이제 단순한 명령 실행기가 아니라, 여러 도구와 모델을 묶어 작업을 위임하는 agent runtime 표면이 되고 있습니다. terminal-first AI 도구가 많아질수록 차별점은 “모델을 호출한다”가 아니라 “로컬 파일, 원격 서비스, 승인 흐름, 반복 실행을 얼마나 안정적으로 묶는가”가 됩니다.

UI 자체를 agent가 생성하거나 조정하는 연구도 주목할 만합니다. Macaron-A2UI는 personal agent가 대화만 하는 것이 아니라 정보 수집, 선호 조정, 확인용 action UI를 생성하는 방향을 보여줍니다. 사용자는 긴 자연어 답변보다 조작 가능한 작은 UI를 선호할 때가 많습니다. 예를 들어 일정 후보를 고르거나, 이메일 초안을 승인하거나, 검색 조건을 조정하는 작업은 채팅보다 폼·버튼·슬라이더가 더 낫습니다. agent UX의 다음 단계는 “채팅창 안에서 모든 것을 설명하는 모델”이 아니라, 상황에 맞는 조작 표면을 만들어 사용자의 확인과 수정을 빠르게 받는 모델일 수 있습니다.

평가도 더 현실적인 쪽으로 바뀌고 있습니다. Hugging Face의 Claw-Anything benchmark는 always-on personal assistant가 얼마나 어려운 문제인지 보여줍니다. 수개월치 사용자 활동, 여러 서비스, GUI와 CLI, 다중 디바이스 맥락이 얽히면 단일 질문 답변 benchmark와는 전혀 다른 난도가 됩니다. frontier 모델도 pass@1이 낮게 나오는 이유는 지능이 부족해서만이 아니라, 장기 기억, 권한 경계, 도구 상태, 사용자 선호, 실패 복구가 모두 필요하기 때문입니다. agent 제품을 평가하려면 “정답을 말했는가”보다 “맥락을 잃지 않고 안전하게 끝냈는가”를 봐야 합니다.

멀티모달 쪽에서는 video, 3D, robotics, digital twin이 계속 붙고 있습니다. ParaVT는 비디오 agent가 여러 도구를 병렬로 호출하도록 학습하는 방향을 보여주고, WBench는 interactive video world model을 multi-turn 상태 전이 기준으로 평가하려 합니다. Pantheon360과 TriSplat은 3D-aware reconstruction과 simulation-ready scene generation을 다룹니다. 이 흐름은 단순히 멋진 비디오를 만드는 문제가 아닙니다. agent가 현실 세계나 시뮬레이션 환경에서 계획하고 검증하려면 장면 구조, 시간 변화, 상호작용 결과를 다룰 수 있어야 합니다.

마지막으로 embedding과 retrieval도 조용히 역할이 커지고 있습니다. SMARTer처럼 embedding model의 reasoning·semantic capability를 분석하는 연구는 RAG 시스템에서 embedding을 단순 검색 부품으로만 보지 않게 만듭니다. retrieval은 agent의 기억과 외부 지식 접근을 담당하는 핵심 레이어입니다. embedding이 어떤 의미 신호를 담고, 어떤 추론에는 약한지 이해해야 agent가 잘못된 문서를 자신 있게 끌어오는 문제를 줄일 수 있습니다.

오늘의 결론은 분명합니다. AI/ML 트렌드는 “더 큰 모델”보다 **모델이 붙는 위치와 실행 표면**으로 이동하고 있습니다. Android 앱, 로컬 CLI, 레포 이슈, 생성형 UI, edge runtime, video world model, retrieval layer가 모두 agent의 일부가 됩니다. 앞으로 강한 AI 제품은 모델 하나를 잘 고르는 제품이 아니라, 사용자가 실제로 일하는 디바이스와 도구 위에서 agent가 안전하고 빠르게 움직이도록 런타임을 설계하는 제품일 가능성이 큽니다.

참고 링크:

- Google ADK for Kotlin/Android: <https://developers.googleblog.com/en/adk-kotlin-android-building-ai-agents/>
- Google LiteRT-LM: <https://developers.googleblog.com/en/blazing-fast-on-device-genai-with-litert-lm/>
- Gemini CLI to Antigravity CLI: <https://developers.googleblog.com/en/an-important-update-transitioning-gemini-cli-to-antigravity-cli/>
- GitHub Copilot semantic issue search: <https://github.blog/changelog/2026-05-20-semantic-issue-search-in-copilot-chat>
- Macaron-A2UI: <https://huggingface.co/papers/2605.24830>
- Claw-Anything benchmark: <https://huggingface.co/papers/2605.26086>
- ParaVT: <https://huggingface.co/papers/2605.20342>
- SMARTer: <https://huggingface.co/papers/2605.24938>

