---
title: "칩은 빌려 쓰고 소프트웨어는 직접 짠다 — AI 인프라 경쟁의 두 축"
date: 2026-10-01T21:04:00+09:00
draft: false
categories: ["ai-ml"]
tags: ["AI-Infrastructure", "Broadcom", "Anthropic", "DeepSeek", "Huawei", "TileLang", "Robinhood", "Agentic-Finance"]
comments: true
---

오늘 #ai-ml-trends에 올라온 소식들을 묶어 보면 한 가지 흐름이 보입니다. AI 경쟁의 무게중심이 "어떤 모델이 더 똑똑한가"에서 **"연산 자원을 누가, 어떤 방식으로 확보하고 어떤 소프트웨어로 굴리는가"**로 옮겨 가고 있다는 점입니다. 오늘은 그중 세 가지 신호를 정리합니다.

## 1. Broadcom이 Anthropic에 칩 리스 자금을 빌려준다

로이터는 Broadcom이 IPO 관련 서류를 통해 Anthropic에 최대 420억 달러 규모의 대출을 제공할 수 있다고 밝혔다고 전했습니다. 핵심은 돈의 용도입니다. 자금은 Broadcom 칩을 리스하는 데 쓰입니다. 공급업체가 고객의 구매력까지 금융으로 뒷받침하는 구조입니다.

이 구조가 의미하는 바는 다음과 같습니다.

- **칩 공급자가 곧 금융 제공자가 됩니다.** 대형 모델 기업의 연산 수요는 현금흐름만으로 감당하기 어려운 수준입니다. 공급망 안에서 금융이 함께 돌아가기 시작했습니다.
- **고객 집중도가 커집니다.** 대출과 리스로 묶인 관계는 공급자의 매출 안정성을 높여 주지만, 한 고객의 성패에 공급자가 더 깊이 노출됩니다.
- **모델 기업의 비용 구조가 달라집니다.** 칩을 자산으로 사는 대신 리스로 쓰면 초기 부담은 줄지만 고정비 성격의 상환 의무가 쌓입니다.

다만 이 소식은 IPO 서류에 기반한 보도이고 조건의 세부는 확정되지 않았습니다. 최대 금액과 실제 집행액은 다른 개념이라는 점을 구분해서 봐야 합니다.

## 2. DeepSeek와 Huawei, Ascend용 오픈소스 개발 도구를 내놓다

The Decoder 보도에 따르면 DeepSeek는 Huawei와 함께 Ascend 칩용 프로그래밍 도구를 만들고 전부 오픈소스로 공개합니다. 중심에는 TileLang이 있습니다. 베이징대 연구진이 만든 AI 칩용 프로그래밍 언어로, DeepSeek는 약 1년 전부터 사용해 왔고 CUDA보다 단순한 프로그래밍 모델을 제공한다고 주장합니다. 연산·칩 간 데이터 이동 라이브러리가 함께 공개되고, Ascend 950 칩 128개로 구성한 슈퍼노드도 같이 최적화했다고 합니다.

Nvidia의 진짜 해자는 칩 설계만이 아니라 CUDA를 쓰는 개발자 생태계입니다. 국산 칩이 성능을 제대로 내려면 하드웨어보다 먼저 **소프트웨어 계층**이 필요합니다. 그래서 이번 공개는 칩 성능 경쟁보다 한 단계 아래, 즉 "개발자가 이 칩을 쉽게 쓸 수 있는가"를 겨냥한 움직임으로 읽힙니다.

1번과 2번은 같은 이야기의 양면입니다. 한쪽은 미국에서 칩을 **금융으로 확보하는** 방식이고, 다른 쪽은 중국에서 칩을 **소프트웨어로 쓸 만하게 만드는** 방식입니다. 둘 다 "칩 한 장의 성능"이 아니라 칩을 둘러싼 체계가 경쟁력이라는 결론으로 이어집니다.

## 3. Robinhood Agents — 사용자가 직접 만드는 트레이딩 에이전트

Robinhood는 HOOD Summit 2026(9월 29일)에서 사용자가 직접 AI 에이전트를 만들어 전략 수립, 리서치, 자동 거래에 쓸 수 있는 Robinhood Agents를 공개했습니다. 24시간 주말 거래 소식과 함께 나왔습니다. PYMNTS는 "안전과 보안을 핵심에 두겠다"는 회사 측 발언을 전했습니다.

여기서 눈여겨볼 지점은 **에이전트의 제작 주체가 사용자**라는 점입니다. 금융 플랫폼이 에이전트를 직접 제공하는 것과 달리, 사용자가 만든 에이전트가 계좌에서 주문까지 낸다면 책임 경계가 훨씬 복잡해집니다. 어떤 주문이 허용되는지, 한도는 얼마인지, 실패했을 때 누가 멈출 수 있는지가 제품 설계의 핵심이 됩니다.

## 실무 관점의 체크포인트

세 소식을 운영 관점에서 한 줄씩 옮기면 다음과 같습니다.

1. **연산 비용은 계약 구조까지 봐야 합니다.** 단가만이 아니라 리스·대출·장기 약정이 얽힌 총비용을 비교해야 합니다.
2. **벤더 락인은 칩뿐 아니라 소프트웨어 계층에서 생깁니다.** 특정 컴파일러나 커널 언어에 묶이면 이식 비용이 커집니다.
3. **자동 실행 에이전트는 권한과 한도를 먼저 설계해야 합니다.** 모델 성능보다 허용 범위, 승인 흐름, 중단 수단이 먼저입니다.

## 정리

AI 경쟁의 승부처는 모델 벤치마크에서 **자원 확보 방식과 실행 환경의 설계**로 넓어지고 있습니다. 칩을 어떻게 조달하고, 그 위에서 어떤 소프트웨어로 돌리며, 그 결과물이 어디까지 자동으로 행동하게 할지를 함께 봐야 하는 시기입니다.

## Sources

- Reuters — Broadcom to lend Anthropic up to $42 billion to lease its chips, filing says: <https://www.reuters.com/business/broadcom-lend-anthropic-up-42-billion-lease-its-chips-filing-says-2026-10-01/>
- The Decoder — China's AI industry closes ranks as Deepseek ships open-source software for Huawei's Ascend chips: <https://the-decoder.com/chinas-ai-industry-closes-ranks-as-deepseek-ships-open-source-software-for-huaweis-ascend-chips/>
- PYMNTS — Robinhood Unveils New AI Agents and 24/7 Weekend Trading: <https://www.pymnts.com/news/artificial-intelligence/2026/robinhood-unveils-new-ai-agents-and-24-7-weekend-trading/>
