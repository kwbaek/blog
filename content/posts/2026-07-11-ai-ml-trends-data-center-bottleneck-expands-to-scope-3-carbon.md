---
title: "AI/ML 트렌드: AI 데이터센터의 병목이 전력에서 공급망 탄소로 넓어진다"
date: 2026-07-11T21:01:00+09:00
draft: false
categories: ["ai-ml"]
tags: ["AI", "ML", "Data Center", "Carbon Emissions", "Scope 3", "Cloud", "Sustainability"]
comments: true
---

오늘 주목할 AI/ML 트렌드는 새로운 모델이 아니라 **AI 인프라의 비용표가 바뀌고 있다는 신호**입니다. 가디언이 Microsoft·Amazon·Google의 지속가능성 보고서를 종합한 결과, 세 회사의 2026 회계연도 탄소배출량은 총 1억1,900만tCO₂e로 전년 약 1억100만tCO₂e보다 거의 18% 늘었습니다. 기사에 따르면 Microsoft는 25%, Google은 18%, Amazon은 16% 증가했으며, 데이터센터 건설과 이를 뒷받침하는 공급망 활동이 핵심 요인으로 지목됐습니다.

AI 인프라 논의는 보통 GPU 수급, 전력 확보, 냉각, 네트워크 대역폭에 집중됩니다. 하지만 이번 수치는 서버가 켜진 뒤 사용하는 전기만으로는 전체 비용과 환경 영향을 설명할 수 없다는 점을 보여줍니다. 건물의 철강·콘크리트, 반도체와 서버 제조, 냉각 설비, 물류처럼 데이터센터를 짓기 전에 발생하는 **embodied carbon**과 Scope 3 배출이 AI 확장의 중요한 제약으로 부상하고 있습니다.

## 클라우드 이전이 배출을 없애지는 않는다

기업이 자체 서버를 클라우드로 옮기면 내부 전력 사용량과 직접 배출은 줄어든 것처럼 보일 수 있습니다. 그러나 실제 연산은 hyperscaler의 데이터센터에서 계속 일어납니다. 즉, 배출이 사라지는 것이 아니라 공급업체 쪽으로 이동할 수 있습니다. 앞으로 기업 고객은 클라우드 비용표뿐 아니라 워크로드별 탄소 데이터, 지역별 전력원, 장비 수명, 증설에 따른 공급망 배출까지 요구하게 될 가능성이 큽니다.

이 변화는 AI 제품의 아키텍처 결정에도 영향을 줍니다. 더 큰 모델을 기본값으로 사용하는 설계, 캐시 없이 같은 긴 문맥을 반복 전송하는 에이전트, 사용률이 낮은 GPU를 상시 점유하는 전용 배포는 비용뿐 아니라 탄소 효율에서도 불리합니다. 반대로 작은 모델 라우팅, prompt caching, batch inference, quantization, 사용률 기반 capacity planning은 FinOps와 탄소 감축을 동시에 개선할 수 있습니다.

## 기업이 준비해야 할 세 가지

첫째, AI 워크로드의 품질·지연·비용 지표에 에너지와 탄소 지표를 함께 붙여야 합니다. 둘째, 클라우드·GPU 공급업체를 평가할 때 운영 전력뿐 아니라 장비와 건설을 포함한 Scope 3 산정 범위를 확인해야 합니다. 셋째, “AI가 다른 산업의 배출을 줄인다”는 편익과 “AI 인프라 자체가 만드는 배출”을 서로 상쇄해 뭉뚱그리지 말고 별도의 장부로 관리해야 합니다.

AI 데이터센터 투자는 계속 확대될 가능성이 높습니다. 따라서 중요한 질문은 AI를 멈출 것인가가 아니라, **같은 사업 성과를 더 적은 연산·전력·자재로 달성할 수 있는가**입니다. 모델 성능 경쟁 다음에는 inference efficiency와 공급망 탄소를 함께 증명하는 운영 능력이 AI 인프라의 경쟁력이 될 것입니다.

참고:

- The Guardian, Datacentres drive up big tech’s carbon emissions to a third of those of France — https://www.theguardian.com/us-news/2026/jul/11/microsoft-amazon-google-datacentre-carbon-emissions-france
