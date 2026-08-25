---
title: "AI/ML 트렌드: 시범 성공률과 조직 채택률은 다른 숫자다"
date: 2026-08-25T22:15:00+09:00
draft: false
categories: ["ai-ml"]
tags: ["AI", "ML", "에이전트", "로보틱스", "기업채택", "파운데이션모델"]
comments: true
---

같은 날 나온 두 소식을 나란히 놓으면 패턴이 하나 보입니다. 하나는 소프트웨어 에이전트의 조직 내 채택률 데이터, 다른 하나는 로봇 파운데이션 모델의 원샷 학습 성공률 데이터입니다. 둘 다 "모델이 무엇을 할 수 있는가"와 "조직이 그것을 실제로 쓰는가"가 서로 다른 숫자라는 걸 보여줍니다.

## 사내 98% vs 기업 구독자 17%

[TechCrunch가 인용한 OpenAI 후원 연구](https://techcrunch.com/2026/08/24/openai-is-building-an-ai-agent-for-everything-will-everyone-use-them/)에 따르면 지난 6월 기준 OpenAI 내부 직원의 98%가 코딩 에이전트 Codex를 쓰고 있었지만, 조직(기업) 구독자 중에서는 17%, 개인 구독자 중에서는 1% 미만만 실제로 이 에이전트 기능을 사용했습니다. 같은 도구, 같은 회사인데도 "만든 사람들이 매일 쓰는 것"과 "고객이 실제로 쓰는 것" 사이에 80%p 넘는 격차가 있는 셈입니다.

기사는 그 격차의 원인을 한 문장으로 짚습니다. 엔지니어에게는 CLI 하나로 충분했지만, 대부분의 사람은 CLI를 쓰지 않는다는 것입니다. 에이전트가 무엇을 볼 수 있고 어떤 도구를 쓸 수 있는지를 감싸는 "하네스"가 소프트웨어 엔지니어링 바깥에서는 아직 낯설다는 뜻입니다. OpenAI가 비개발자 팀(재무, 커뮤니케이션)에 Codex를 쓰게 만드는 과정에서 "빈 diff입니다" 같은 개발자용 메시지부터 손봐야 했다는 대목이 이를 보여줍니다.

## 원샷 59% vs 파인튜닝 83%

로보틱스에서도 비슷한 이중 숫자가 나왔습니다. [Generalist AI의 GEN-1.5 발표](https://generalistai.com/blog/gen-1.5)는 로봇이 3~12초짜리 시범 하나만 보고 새로운 물리 작업을 배우는 "원샷 학습"을 시연했습니다. 10개 과제에서 원샷 인컨텍스트 학습만으로 평균 59%(±10%p) 성공률을 냈고, 같은 과제를 5분 분량 데모(약 50회)로 10번의 그래디언트 스텝만큼 파인튜닝하면 83%(±9%p)까지 올라갑니다.

이 결과가 흥미로운 건 "제로샷이 안 되던 것이 원샷으로 된다"는 지점이 아니라, 원샷과 소량 파인튜닝 사이의 24%p 차이가 정확히 "시연 가능"과 "실전 배치 가능"의 경계라는 점입니다. 같은 날 [General Intuition이 로보틱스 확장을 위해 60억 달러 밸류로 신규 투자 유치를 추진](https://techcrunch.com/2026/08/24/valor-point72-back-general-intuition-at-6b-valuation-as-ai-startup-pushes-into-robotics/)한다는 소식도 이 흐름과 맞물립니다. 투자자들은 "게임 클립에서 배운 행동 라벨이 실제 로봇 조작으로 일반화되는가"에 베팅하고 있는데, 그 답은 원샷 성공률이 아니라 파인튜닝 이후 성공률, 그리고 그 파인튜닝에 드는 데이터·연산 비용에서 나옵니다.

## 두 숫자를 같은 틀로 읽기

정리하면 두 소식 모두 "모델 능력 곡선"과 "조직 채택 곡선"이 별개라는 같은 메시지를 던집니다.

- 능력 곡선은 빠르게 오릅니다. Codex는 코드를 쓰고, GEN-1.5는 한 번 보여준 동작을 재현합니다.
- 채택 곡선은 하네스, 신뢰, 운영 관행에 의해 결정됩니다. 비개발자용 인터페이스가 없으면 능력은 조직 안에 갇힙니다. 원샷 59%로 배포하면 실패가 잦고, 83%를 확보하려면 파인튜닝 파이프라인과 데이터 수집이라는 별도 투자가 필요합니다.

AI 도입을 검토할 때 "이 모델이 무엇을 할 수 있는가"만 보면 절반만 본 것입니다. 나머지 절반은 "이 능력을 우리 조직의 비개발자·비전문가가 실제로 쓸 수 있게 만드는 하네스에 얼마를 투자할 것인가"라는 질문입니다. 오늘 두 회사가 보여준 숫자는 그 투자가 선택이 아니라 채택률 자체를 결정하는 변수라는 걸 말해줍니다.

## 출처

- [TechCrunch, OpenAI is building AI agents for everything. Will everyone use them?](https://techcrunch.com/2026/08/24/openai-is-building-an-ai-agent-for-everything-will-everyone-use-them/) — Codex 내부 98% vs 조직 구독자 17%, 개인 구독자 1% 미만
- [Generalist AI, GEN-1.5: Embodied Foundation Models are One-Shot Learners](https://generalistai.com/blog/gen-1.5) — 원샷 59%, 파인튜닝 후 83% 성공률
- [TechCrunch, Valor, Point72 back General Intuition at $6B valuation as AI startup pushes into robotics](https://techcrunch.com/2026/08/24/valor-point72-back-general-intuition-at-6b-valuation-as-ai-startup-pushes-into-robotics/) — 로보틱스 확장을 위한 신규 투자 유치
