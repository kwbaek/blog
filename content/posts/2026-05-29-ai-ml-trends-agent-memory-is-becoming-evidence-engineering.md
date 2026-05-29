---
title: "AI/ML 트렌드: 에이전트 메모리는 이제 증거 엔지니어링이 되고 있다"
date: 2026-05-29T21:00:00+09:00
draft: false
categories: ["ai-ml"]
tags: ["AI", "ML", "LLM", "Agent", "Memory", "RAG", "Retrieval", "PyTorch", "AI Safety"]
comments: true
---

오늘 `#ai-ml-trends` 채널에서 가장 흥미로운 흐름은 **AI 에이전트의 다음 병목이 단순한 컨텍스트 길이가 아니라, 어떤 증거를 어떤 구조로 기억하고 다시 꺼내는가**로 이동하고 있다는 점입니다. OpenAI의 Rosalind Biodefense, NVIDIA의 deep research skill, Hugging Face의 `torch.profiler` 가이드, S3Mem 논문, Micro-Macro Retrieval 논문은 서로 다른 층위의 소식처럼 보이지만 한 가지 질문으로 연결됩니다. “AI가 길고 복잡한 작업을 맡으려면 결과를 그럴듯하게 말하는 것보다, 근거를 안정적으로 보존하고 검증하며 재사용하는 체계가 필요하지 않은가?”

OpenAI의 Rosalind Biodefense는 이 질문을 가장 민감한 영역에서 보여줍니다. 생물안보처럼 사회적 리스크가 큰 분야에서 AI를 쓴다는 것은 모델이 정답을 많이 맞히는 문제를 넘어섭니다. 어떤 자료를 근거로 판단했는지, 어느 단계에서 전문가 검토가 필요한지, 위험한 오용 가능성을 어떻게 줄일지까지 함께 설계해야 합니다. 즉 고위험 AI는 “지식이 많다”보다 “증거와 책임의 경로가 남는다”가 더 중요해집니다.

NVIDIA의 “agent harness에 deep research skill을 추가하라”는 글도 같은 방향입니다. Claude Code, Codex, LangChain Deep Agents 같은 harness는 세션을 관리하고 도구를 호출하고 코드를 실행하는 오케스트레이터 역할을 잘합니다. 하지만 깊은 리서치에는 별도의 skill이 필요합니다. 검색어를 고르고, 출처를 비교하고, 중간 결론을 저장하고, 다시 검증하는 절차가 있어야 긴 작업이 단순한 tool-call 나열로 끝나지 않습니다. 에이전트 성능은 모델 크기만이 아니라 skill의 작업 설계에서 갈립니다.

S3Mem 논문은 장기 상호작용 에이전트의 메모리 문제를 더 정면으로 다룹니다. 긴 trajectory를 그냥 텍스트 chunk로 쌓아 두고 RAG로 검색하면, 지역적으로는 관련 있어 보이지만 실제 답변에 필요한 사건의 사슬이 끊기는 경우가 많습니다. 논문은 scene-event 단위의 구조화된 episodic memory, anchor-sensitive retrieval, token budget을 고려한 evidence interface를 제안합니다. 핵심은 “모든 것을 더 길게 넣자”가 아니라, 공간·시간·반복 사건·상태 변화 같은 질문에 맞게 기억을 구조화하자는 것입니다.

Micro-Macro Retrieval도 long-form hallucination을 줄이는 같은 문제의식에서 출발합니다. 긴 답변에서는 검색된 문맥이 많아질수록 오히려 중요한 정보가 출력에서 멀어지고, 그 사이에서 사실 오류가 커질 수 있습니다. 이 논문은 macro 단계에서 큰 증거를 가져오고, micro 단계에서 핵심 정보를 생성 과정 가까이에 재사용하는 retrieve-while-generate 구조를 제안합니다. RAG의 다음 버전은 “검색해서 앞에 붙이기”가 아니라, 답변이 만들어지는 순간마다 필요한 증거를 가까운 위치에 유지하는 방향으로 진화하고 있습니다.

Hugging Face의 `torch.profiler` 입문 글은 조금 다른 층위지만 실무적으로 중요합니다. 에이전트와 RAG 시스템이 복잡해질수록 성능 병목은 모델 호출 비용, retrieval latency, GPU utilization, 데이터 전처리, serving pipeline 곳곳에서 생깁니다. 좋은 메모리와 retrieval 구조를 설계해도 실제 운영에서는 profiling 없이 어디가 느린지 알기 어렵습니다. AI 시스템이 제품이 되려면 알고리즘 아이디어와 함께 계측, 병목 분석, 반복 개선이 붙어야 합니다.

오늘의 결론은 명확합니다. 2026년의 AI/ML 트렌드는 “더 긴 컨텍스트”에서 “더 좋은 증거 인터페이스”로 이동하고 있습니다. 고위험 분야에서는 출처와 검토 경로가 필요하고, 에이전트 harness에는 deep research skill이 필요하며, 장기 메모리에는 scene-event 구조가 필요하고, long-form generation에는 micro-level evidence reuse가 필요합니다. 이제 에이전트의 기억은 저장 공간이 아니라 **증거 엔지니어링 레이어**입니다. 이 레이어를 잘 설계하는 팀이 더 긴 작업, 더 민감한 분야, 더 신뢰 가능한 자동화를 맡게 될 가능성이 큽니다.

참고 링크:

- OpenAI: Strengthening societal resilience with Rosalind Biodefense: <https://openai.com/index/strengthening-societal-resilience-with-rosalind-biodefense>
- NVIDIA Technical Blog: Add a Specialized Deep Research Skill to Agent Harnesses: <https://developer.nvidia.com/blog/add-a-specialized-deep-research-skill-to-agent-harnesses/>
- Hugging Face: Profiling in PyTorch (Part 1): <https://huggingface.co/blog/torch-profiler>
- arXiv: S3Mem: Structured Spatiotemporal Scene-Event Memory for Long-Horizon Interactive Question Answering: <https://arxiv.org/abs/2605.28831>
- arXiv: Micro-Macro Retrieval: Reducing Long-Form Hallucination in Large Language Models: <https://arxiv.org/abs/2605.28828>
