---
title: "AI/ML 트렌드: 에이전트 Skill은 이제 공급망이다"
date: 2026-06-11T21:00:00+09:00
draft: false
categories: ["ai-ml"]
tags: ["AI", "ML", "Agent", "Security", "Supply Chain", "Storage", "Infrastructure", "Unit 42", "TrendForce"]
comments: true
---

오늘 AI/ML 트렌드에서 가장 흥미로운 신호는 **에이전트의 확장 단위가 프롬프트가 아니라 공급망으로 취급되기 시작했다**는 점입니다. Unit 42는 AI agent skill supply chain 리스크를 다루며 Behavioral Integrity Verification(BIV)을 제안했고, TrendForce는 AI agent boom이 enterprise SSD 공급 압박으로 이어지고 있다고 분석했습니다. 하나는 에이전트가 가져다 쓰는 skill·manifest·코드의 신뢰 문제이고, 다른 하나는 에이전트 실행이 실제 저장장치 수요까지 밀어 올리는 인프라 문제입니다.

첫 번째 변화는 에이전트 보안의 초점이 “모델이 나쁜 답을 하지 않는가”에서 “에이전트가 설치한 능력이 실제로 무엇을 하는가”로 이동한다는 것입니다. 지금까지 많은 팀은 skill, MCP tool, plugin, workflow 문서를 편의 기능처럼 다뤘습니다. 하지만 에이전트가 파일을 읽고, 브라우저를 조작하고, 저장소를 수정하고, 배포까지 할 수 있다면 skill은 사실상 작은 앱입니다. 설명 문서에는 읽기 전용이라고 되어 있지만 실제 코드가 쓰기 권한을 요구한다면, 그것은 prompt 품질 문제가 아니라 공급망 무결성 문제입니다.

BIV가 흥미로운 이유는 이 문제를 설치 전 검증 게이트로 바꾸기 때문입니다. skill의 설명, manifest, 코드, 호출 권한, 실제 행동이 서로 맞는지 비교하고, 의심스러운 drift를 배포 전에 잡자는 접근입니다. 이는 패키지 매니저가 의존성 해시와 권한을 보는 것과 비슷합니다. AI 에이전트 생태계에서도 “좋은 프롬프트 모음”이라는 느슨한 표현만으로는 부족해지고, 누가 만들었고 어떤 권한을 쓰며 어떤 로그를 남기는지까지 관리해야 합니다.

두 번째 변화는 agent boom이 GPU만의 이야기가 아니라는 점입니다. TrendForce는 enterprise SSD 시장에서 AI agent 수요가 공급 압박을 키우고 있다고 봅니다. 에이전트 서비스는 단발성 채팅보다 더 많은 상태를 남깁니다. 작업 로그, 파일 스냅샷, 벡터 인덱스, 장기 메모리, 감사 로그, 재시도 기록이 쌓입니다. 장기 실행 에이전트가 많아질수록 병목은 GPU inference뿐 아니라 빠른 storage, 로그 보존 비용, 데이터 수명주기 관리로 확장됩니다.

이 두 신호를 함께 보면 AI/ML의 다음 실무 과제는 명확합니다. 에이전트를 도입하는 조직은 “어떤 모델을 쓸까”보다 먼저 “어떤 skill을 신뢰할 수 있고, 어떤 데이터를 얼마나 오래 남길 것인가”를 정해야 합니다. skill registry, 설치 전 검증, 권한 최소화, 로그 보존 정책, storage 비용 한도를 하나의 운영 설계로 묶어야 합니다. 에이전트가 더 유능해질수록 가장 비싼 실패는 답변 오류가 아니라, 신뢰할 수 없는 능력을 설치하고 그 흔적을 재구성하지 못하는 일입니다.

참고 링크:

- Unit 42: AI agent supply chain risks: <https://unit42.paloaltonetworks.com/ai-agent-supply-chain-risks/>
- TrendForce: AI agent boom and enterprise SSD pressure: <https://www.trendforce.com/presscenter/news/20260611-13092.html>
