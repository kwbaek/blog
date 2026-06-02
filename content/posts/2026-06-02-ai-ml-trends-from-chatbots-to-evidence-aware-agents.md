---
title: "AI/ML 트렌드: 챗봇 다음 단계는 Evidence-aware Agent다"
date: 2026-06-02T21:00:00+09:00
draft: false
categories: ["ai-ml"]
tags: ["AI", "ML", "Agent", "Governance", "Healthcare", "Policy", "Superapp", "Evidence"]
comments: true
---

오늘 `#ai-ml-trends` 흐름에서 가장 흥미로운 신호는 WHO의 “AI와 evidence-informed health policy” 토론문과 Tencent의 WeChat AI agent 출시 준비 보도가 같은 방향을 가리킨다는 점입니다. 하나는 의료·공공정책처럼 책임이 큰 영역이고, 다른 하나는 메신저·커머스·생활 플랫폼이 결합된 슈퍼앱입니다. 겉으로는 전혀 다른 뉴스처럼 보이지만, 둘 다 AI가 단순 대화창을 넘어 **결정 과정 안으로 들어가는 순간 무엇이 필요해지는가**를 보여줍니다.

WHO 논의의 핵심은 AI가 정책 담당자에게 더 빠른 요약을 제공하는 수준에서 멈추지 않는다는 데 있습니다. 근거 기반 정책에서는 어떤 연구가 선택됐는지, 근거의 품질은 어떤지, 편향 가능성은 어디에 있는지, 반대 근거는 무엇인지가 중요합니다. 모델이 그 과정을 검은상자처럼 압축해버리면 속도는 빨라질 수 있지만 책임은 흐려집니다. 의료·공공정책 AI는 답을 잘 내는 도구가 아니라, 판단에 쓰인 근거와 불확실성을 함께 노출하는 **evidence interface**가 되어야 합니다.

Tencent의 WeChat agent 보도도 같은 문제를 소비자 플랫폼 쪽에서 보여줍니다. WeChat 안에 agent가 들어가면 사용자는 별도 AI 앱으로 이동하지 않고 메시지, 결제, 쇼핑, 예약, 업무 커뮤니케이션 흐름 안에서 AI를 만나게 됩니다. 이때 agent는 단순 추천을 넘어 행동을 제안하거나 실행할 수 있습니다. 어떤 상품을 고를지, 어떤 업무를 예약할지, 어떤 문서를 보낼지 결정 과정에 agent가 개입하면, “왜 이 선택지를 추천했는가”와 “어떤 데이터와 권한을 사용했는가”가 사용자 신뢰의 핵심이 됩니다.

그래서 오늘의 큰 트렌드는 agent의 무대가 넓어질수록 **근거·권한·감사 가능성**이 제품 기능이 된다는 것입니다. 2023~2024년의 AI 제품 경쟁이 “대화가 자연스러운가”에 가까웠다면, 2026년의 경쟁은 “이 agent가 내 대신 판단하거나 실행할 때 충분히 검증 가능하고 멈출 수 있는가”로 이동하고 있습니다. 특히 의료, 공공정책, 금융, 커머스, 업무 자동화처럼 실제 결과가 남는 영역에서는 모델 정확도보다 운영 증거가 더 중요한 구매 기준이 될 수 있습니다.

실무적으로는 agent를 설계할 때 UI에 근거 레이어를 기본으로 넣어야 합니다. 출처 링크, 신뢰도, 반대 근거, 사용한 권한, 실행 전 확인 단계, 로그 보관 정책을 처음부터 제품 요구사항에 포함해야 합니다. “AI가 추천했습니다”라는 문구만으로는 부족합니다. 사용자가 이해할 수 있는 설명과 조직이 감사할 수 있는 증거가 함께 있어야 합니다.

오늘의 결론은 간단합니다. 챗봇 다음 단계는 더 말 잘하는 챗봇이 아니라 **evidence-aware agent**입니다. AI가 슈퍼앱과 정책 결정 과정 안으로 들어갈수록, 이기는 제품은 답을 빠르게 내는 제품이 아니라 그 답의 근거와 권한 경계를 끝까지 보여주는 제품이 될 가능성이 큽니다.

참고 링크:

- WHO: New discussion paper on AI in evidence-informed health policy: <https://www.who.int/news/item/02-06-2026-new-who-discussion-paper-sets-out-opportunities-and-risks-of-ai-in-evidence-informed-health-policy>
- Bloomberg: Tencent reportedly preparing WeChat AI agent: <https://www.bloomberg.com/news/articles/2026-06-02/tencent-jumps-after-report-it-s-set-to-launch-wechat-ai-agent>
