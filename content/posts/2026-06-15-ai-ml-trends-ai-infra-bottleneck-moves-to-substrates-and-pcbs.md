---
title: "AI/ML 트렌드: AI 인프라 병목은 기판과 PCB까지 내려갔다"
date: 2026-06-15T21:02:00+09:00
draft: false
categories: ["ai-ml"]
tags: ["AI", "ML", "Infrastructure", "Supply Chain", "Data Center", "Hardware", "Procurement"]
comments: true
---

오늘 AI/ML 트렌드에서 가장 중요한 신호는 AT&S가 말레이시아 Kulim 생산 거점에 **15억~20억 유로 규모의 AI substrate·advanced PCB capacity 확장**을 발표한 것입니다. 겉으로 보면 반도체 부품 회사의 설비 투자 뉴스처럼 보이지만, AI/ML 운영 관점에서는 훨씬 더 큰 의미가 있습니다. AI 인프라 병목이 더 이상 GPU와 HBM만의 문제가 아니라, 서버 보드와 패키징을 떠받치는 기판·PCB·소재·지역 생산능력까지 내려갔다는 뜻이기 때문입니다.

지난 몇 달 동안 AI 인프라 논의는 주로 “어떤 GPU를 얼마나 확보하느냐”에 집중됐습니다. 하지만 실제 데이터센터는 GPU만으로 돌아가지 않습니다. 고성능 accelerator를 묶는 substrate, advanced PCB, optical interconnect, power delivery, cooling, rack density가 같이 맞아야 합니다. AT&S의 Kulim 투자는 이 하위 레이어가 AI 수요를 따라잡기 위해 얼마나 큰 자본지출을 필요로 하는지 보여줍니다. 모델 API 가격이나 cloud instance 단가만 보고 AI rollout 일정을 잡는 팀은 이제 중요한 리스크를 놓치게 됩니다.

이 변화는 기업 AI 도입에도 직접 영향을 줍니다. 예를 들어 사내 agent, 검색 증강, 개인화 추천, 멀티모달 검수 같은 서비스를 대규모로 운영하려면 단순히 “좋은 모델을 고른다”로 끝나지 않습니다. 특정 region에서 capacity가 열리는 시점, AI server vendor의 부품 allocation, PCB·substrate lead time, 대체 공급선 유무가 서비스 출시 일정과 SLA에 영향을 줍니다. AI가 소프트웨어처럼 보일수록, 실제 병목은 점점 더 물리적인 공급망으로 이동합니다.

흥미로운 점은 이 흐름이 구매·재무·영업 조직의 문서화 기준도 바꾼다는 것입니다. AI 프로젝트 승인 자료에는 모델 성능표뿐 아니라 capacity stack이 들어가야 합니다. GPU, memory, networking, substrate, PCB, power, cooling을 하나의 표로 묶고, 각 항목의 공급 지역과 대체 경로를 표시해야 합니다. 고객에게 AI 기능을 팔 때도 “우리는 최신 모델을 씁니다”보다 “부품·지역·벤더 병목이 생겨도 납기와 성능 영향을 설명할 수 있습니다”가 더 강한 신뢰 신호가 됩니다.

결국 오늘의 핵심은 AI/ML이 다시 하드웨어 산업이라는 사실입니다. 모델은 클라우드 콘솔에서 몇 줄로 호출되지만, 그 뒤에는 유로 단위의 대규모 설비 투자와 다층 공급망이 있습니다. 앞으로 좋은 AI 팀은 prompt와 benchmark만 보는 팀이 아니라, substrate와 PCB 같은 보이지 않는 병목까지 제품 로드맵의 일부로 관리하는 팀이 될 것입니다.

참고 링크:

- AT&S Kulim capacity expansion: <https://ats.net/en/ir-news/ats-expands-kulim-site-to-support-long-term-customer-demand-and-deepen-strategic-technology-partnerships/>
