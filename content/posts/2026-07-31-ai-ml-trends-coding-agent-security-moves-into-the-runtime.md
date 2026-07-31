---
title: "코딩 에이전트 보안은 프롬프트가 아니라 런타임 통제가 된다"
date: 2026-07-31T21:01:00+09:00
draft: false
categories: ["ai-ml"]
tags: ["AI Agents", "Coding Agents", "Agent Security", "Open Source", "LLM", "MLOps", "DevSecOps"]
comments: true
---

오늘의 AI/ML 트렌드에서 가장 중요한 변화는 코딩 에이전트의 보안 문제가 더 이상 “좋은 프롬프트를 쓰면 해결되는 문제”로 취급되지 않는다는 점이다. 에이전트가 저장소를 읽고, 셸 명령을 실행하고, 패키지를 설치하고, 외부 서비스에 접근하는 순간 보안 경계는 모델의 답변이 아니라 **실행 경로(runtime)**에 생긴다.

최근 Perplexity가 공개한 Numbat 관련 보도는 이 변화를 잘 보여준다. Numbat은 코딩 에이전트 세션에 사전 실행 훅과 규칙 기반 검사를 적용해 시크릿 접근, 권한 상승 같은 위험한 행동을 감시하고 필요하면 차단하는 접근이다. 핵심은 모델이 무엇을 “말했는가”가 아니라, 다음 도구 호출이 실제 시스템에 어떤 변화를 일으키는지를 판단하는 데 있다. 에이전트가 아무리 유창하게 정당화해도 `git push`, 자격증명 파일 읽기, 네트워크 호출은 별도의 정책 이벤트로 기록되어야 한다.

이런 통제 계층이 필요한 이유는 에이전트의 능력이 예측보다 빠르게 커지고 있기 때문이다. 오늘의 트렌드 캐시에는 중국 군 연구진이 미국 AI 모델을 국방 시스템 훈련에 활용했다는 Reuters 보도도 포착됐다. 이 사례의 핵심은 특정 모델의 성능 자체가 아니라, **모델 접근권과 사용 목적을 운영 단계에서 통제해야 한다**는 점이다. 모델 공급망, 에이전트 실행권한, 외부 도구의 허용 범위가 하나의 거버넌스 문제로 묶이고 있다.

실무에서는 다음 네 가지를 최소 기준으로 삼을 필요가 있다.

1. **행동별 권한 분리:** 읽기, 쓰기, 네트워크, 배포 권한을 하나의 토큰에 몰아주지 않는다.
2. **사전 실행 정책:** 위험한 명령은 실행 전에 규칙 엔진과 사람 승인 단계를 통과시킨다.
3. **세션 단위 감사 로그:** 프롬프트뿐 아니라 도구 호출, 변경 파일, 외부 요청, 차단 사유를 함께 남긴다.
4. **실패 시 안전한 기본값:** 정책 엔진이나 로그 수집기가 멈추면 실행을 강행하지 말고 제한 모드로 전환한다.

여기서 중요한 결론은 에이전트 보안 제품의 구매 기준도 바뀐다는 것이다. “모델이 위험한 코드를 얼마나 잘 찾는가”보다 “위험한 행동을 실제로 차단하고, 누가 어떤 근거로 허용했는지 재현할 수 있는가”가 더 중요한 평가 항목이 된다. 코딩 에이전트가 개발자의 손을 대체할수록, 보안은 모델 선택의 부록이 아니라 CI/CD와 운영체제 사이에 놓이는 독립적인 제어면(control plane)이 된다.

**결론:** 코딩 에이전트의 다음 경쟁력은 더 똑똑한 자동완성이 아니라, 실행 가능한 행동을 안전하게 제한하는 런타임 정책이다.

**출처:**
- Forbes: [Perplexity open-sources Numbat to monitor risky AI coding agents](https://www.forbes.com/sites/janakirammsv/2026/07/30/perplexity-open-sources-numbat-to-monitor-risky-ai-coding-agents/) — 에이전트 사전 실행 훅·규칙 기반 감시
- Reuters: [Chinese military researchers tap US AI models to train defence systems](https://www.reuters.com/world/asia-pacific/chinese-military-researchers-tap-us-ai-models-train-defence-systems-2026-07-31/) — 모델 접근권과 사용 목적 통제 이슈
- PNNL / Newswise: [PNNL and Amazon partner to advance AI tools for a reliable grid](https://www.newswise.com/articles/pnnl-and-amazon-partner-to-advance-ai-tools-for-a-more-reliable-secure-and-affordable-grid) — AI 인프라 운영을 시뮬레이션·검증하는 접근
