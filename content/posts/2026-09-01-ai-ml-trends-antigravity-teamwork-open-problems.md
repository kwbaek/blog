---
title: "AI/ML 트렌드: Google Antigravity + Gemini 3.7 Flash, 멀티에이전트 팀워크가 FOCS·JMLR급 미해결 문제 7건을 풀다"
date: 2026-09-01T21:10:00+09:00
draft: false
categories: ["ai-ml"]
tags: ["AI", "ML", "Google", "Antigravity", "Gemini", "멀티에이전트", "AI연구", "OpenAI", "ChatGPTAds", "Nvidia", "MediaTek"]
comments: true
---

2026년 9월 첫날 AI/ML 업계에서 가장 눈에 띄는 소식은, 단일 모델의 벤치마크 점수가 아니라 **여러 에이전트가 협업해 수 시간~수일 동안 스스로 문제를 파고드는 '팀워크' 구조**가 실제 연구 성과로 이어졌다는 점입니다.

## 1. Google Antigravity Teamwork — 에이전트 팀이 학계 미해결 문제를 풀다

Google이 공식 블로그를 통해 공개한 내용에 따르면, 자율 에이전트 팀이 협업·비평·반복(iterate)하며 장시간에 걸쳐 복잡한 과제를 해결하는 프레임워크인 **Antigravity의 'Teamwork'**를 Gemini 3.7 Flash와 결합해 다음과 같은 성과를 냈습니다.

- **수학·이론컴퓨터과학**: FOCS, JMLR급 최상위 학술지 미해결 문제 7건 해결. 여기에는 **Knuth의 사이클 추측**(Lean 정리 증명 언어로 40페이지 이상 분량의 증명 검증)을 포함해 희소 볼록 최적화(sparse convex optimization), 검증 가능한 LLM 양자화(provable quantization), prefix-matrix factorization 등이 포함됩니다. TCSBench에서는 71% 정확도를 기록했습니다.
- **시스템 엔지니어링**: 처음부터(from scratch) 사이클 단위로 정확한 out-of-order RISC-V CPU 시뮬레이터를 만들어 xv6 운영체제를 셸까지 부팅시켰으며, 실제 하드웨어 대비 사이클 정합 오차는 0.71%에 불과했습니다.
- **오픈소스 기여**: Eigen(SIMD 최적화)과 ParlayHash(삽입 처리량 2배 향상, 메모리 25% 절감) 같은 핵심 라이브러리에 성능 개선을 실제로 upstream 반영했습니다.

이 사례가 중요한 이유는, 단일 모델 호출로는 도달하기 어려운 **장시간·다단계 추론이 필요한 실제 연구 문제**를, 여러 에이전트가 서로의 결과물을 비평하고 재작업하는 구조를 통해 실질적으로 완결시켰다는 점입니다. 벤치마크 성능이 아니라 "실제로 논문 수준 증명이 통과했다"는 검증 가능한 결과물이 나왔다는 게 핵심입니다.

(출처: [Google 공식 블로그](https://blog.google/innovation-and-ai/technology/developers-tools/antigravity-teamwork-multi-agent/))

## 2. OpenAI ChatGPT Ads — 200일 만에 연매출 10억 달러

OpenAI는 ChatGPT Ads가 출시 200일이 채 되지 않아 연환산 매출(ARR) 10억 달러를 넘어섰다고 발표했습니다. 기존 40개국 이상에서 운영되던 광고를 인도·유럽·중동·북아프리카까지 셀프서비스 Ads Manager로 확장했습니다. 무료·Go 요금제 이용자에게만 광고가 노출되며, 광고가 ChatGPT의 답변 내용 자체에는 영향을 주지 않는다는 점을 명시적으로 강조했습니다. AI 어시스턴트가 검색엔진을 대체하는 광고 매체로 빠르게 자리 잡고 있다는 신호입니다.

## 3. Nvidia-MediaTek — 35억 달러 전환사채 투자 구조 확정

이전에 보도됐던 Nvidia-MediaTek NVLink Fusion 파트너십 확대 소식에 이어, Nvidia가 MediaTek이 발행하는 39억 달러 규모 해외 전환사채 중 35억 달러를 직접 인수하는 구체적 금액·구조가 확정 공개됐습니다. MediaTek은 이를 통해 내년 약 800억 달러 규모로 예상되는 데이터센터 시장에서 최대 15%까지 점유율을 확보하겠다는 목표를 밝혔습니다. AI 칩 생태계에서 지분 투자와 장기 공급 계약이 결합되는 흐름이 계속 강화되고 있습니다.

## 4. Vercel Labs agent-browser v0.36 — WebMCP 실험 지원

Vercel Labs의 오픈소스 브라우저 에이전트 도구 `agent-browser`가 v0.36.0에서 실험적 **WebMCP** 지원을 추가했습니다. 에이전트가 현재 열려있는 웹페이지의 기능을 프레임 인식(frame-aware), 취소(cancellation), 안전 제약(safety constraints)이 포함된 MCP 도구로 자동 노출할 수 있게 됐으며, 로컬 Chrome 환경에서는 기본 활성화됩니다. 웹페이지 자체가 에이전트에게 "이런 도구를 쓸 수 있다"고 알려주는 방향으로 브라우저 자동화 생태계가 진화하고 있음을 보여줍니다.

---

## 종합 인사이트

오늘의 트렌드를 관통하는 키워드는 **"단일 호출에서 팀·생태계 단위로"**입니다. 연구는 개별 모델 응답이 아니라 에이전트 팀의 장시간 협업으로, 광고 수익은 검색 대체재로서의 AI 어시스턴트로, 칩 공급은 지분 투자가 결합된 장기 계약으로, 브라우저 자동화는 웹페이지 스스로 도구를 노출하는 구조로 각각 확장되고 있습니다. 개별 기술 발표보다 이런 구조적 이동을 추적하는 것이 실무에 더 유용한 신호가 될 것입니다.
