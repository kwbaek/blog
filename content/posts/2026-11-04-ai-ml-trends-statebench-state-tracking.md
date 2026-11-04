---
title: "AI/ML 트렌드: 긴 문서 속 '최종 상태'를 모델이 끝까지 따라가지 못한다"
date: 2026-11-04T21:00:00+09:00
draft: false
categories: [ai-ml]
tags: [ai-ml, benchmark, long-context, state-tracking, evaluation, agents]
comments: true
---

오늘(11/4) 스캔에서 가장 구체적인 신호는 Ai2가 공개한 벤치마크 StateBench입니다. 긴 문서 안에서 개체의 상태가 여러 번 바뀔 때 언어 모델이 그 상태를 얼마나 정확히 따라가는지 측정합니다. 공식 Hugging Face 블로그 본문을 직접 확인했고, 게시일은 2026-11-03입니다.

StateBench는 상태 추적을 네 단계로 나눕니다. 값이 한 번 정해지고 바뀌지 않는 R-trivial, 가장 최근 값만 찾으면 되는 aperiodic, 이전 값에 계속 더해 가는 soluble, 변수끼리 참조하며 순서가 얽히는 general finite입니다. 블로그는 이를 생일(고정), 온도조절기(최신 값), 게임 점수(누적), 컵 속 공 찾기(교환 추적)에 비유합니다. 뒤로 갈수록 한 번의 놓침이 최종 답 전체를 틀리게 만듭니다.

결과는 분명합니다. Gemini 3.8 Flash, Claude Opus 5.5, GPT-6 Sol과 Luna는 R-trivial과 aperiodic에서는 대체로 잘 풀었습니다. 그러나 soluble과 general finite에서는 입력이 길어질수록 정확도가 떨어졌고, 가장 긴 general finite 문제에서는 13% 이하로 내려갔습니다. 블로그는 이유 가운데 하나로 민감도를 듭니다. 어려운 단계에서는 많은 갱신이 최종 답에 영향을 주므로, 한 번 빠뜨리면 결과가 바로 틀어집니다.

아키텍처 차이도 흥미롭습니다. 275M 규모 모델을 처음부터 이 과제로만 학습시킨 통제 실험에서, Gated DeltaNet과 하이브리드 모델은 수만 개 할당이 들어간 긴 시퀀스까지 거의 완벽하게 일반화했습니다. 트랜스포머는 쉬운 단계는 배웠지만 긴 시퀀스에서 성능이 무너졌고, general finite는 끝내 학습하지 못했습니다. 다만 이 결과는 소형 모델의 실험이므로 프런티어 모델에 그대로 옮겨 해석하면 안 됩니다. 블로그도 변수 수가 고정되어 있다는 전제가 실제 긴 대화와 맞지 않는다고 한계를 밝힙니다.

실무적 함의는 간단합니다. 긴 로그나 문서에서 여러 번 바뀐 값의 최종 결과를 모델에게 맡길 때는 최종 답만 믿지 말고, 상태 변화 단계를 명시적으로 적게 하거나 최종 값을 원자료에서 다시 계산하는 단계를 붙여야 합니다. 재고, 잔고, 설정값처럼 누적과 참조가 얽힌 값일수록 이 검증이 중요합니다.

정리하면, 긴 문맥을 읽는 능력과 긴 문맥 속 상태를 끝까지 따라가는 능력은 다릅니다. 평균 점수가 높아도 상태 추적 난이도를 나눠 보지 않으면 실패가 어디서 시작되는지 보이지 않습니다.

출처
- Ai2, StateBench: Testing how language models track state changes over long documents (Hugging Face 블로그, 2026-11-03, 본문 확인): https://huggingface.co/blog/allenai/statebench
