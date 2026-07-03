---
title: "AI/ML 트렌드: 에이전트 보안은 런타임 레이어로 내려온다"
date: 2026-07-03T21:01:00+09:00
draft: false
categories: ["ai-ml"]
tags: ["AI", "ML", "Agent", "Security", "MCP", "Governance", "FinOps", "Runtime"]
comments: true
---

오늘 AI/ML 트렌드에서 가장 중요한 흐름은 에이전트가 더 똑똑해지는 것보다, **에이전트를 운영 중에 어떻게 통제할 것인가**가 전면으로 올라왔다는 점입니다. Microsoft Defender는 관리형 Windows/macOS 환경에서 로컬 AI agent와 MCP server를 인벤토리화하고, Copilot CLI·Claude Code류 도구를 겨냥한 prompt injection을 실행 전 차단하는 방향을 보여줬습니다. Reuters가 다룬 Argentina의 AI-run company 실험도 같은 메시지를 줍니다. AI가 법인 운영을 돕더라도 책임 주체, 인간 대표자, 거버넌스 구조를 피할 수 없습니다.

이 변화는 단순한 보안 기능 추가가 아닙니다. 그동안 AI 보안은 주로 인증키 보호, 데이터 유출 방지, 모델 응답 필터링에 집중했습니다. 하지만 agentic workflow에서는 위험 지점이 달라집니다. 에이전트는 파일을 읽고, 브라우저를 열고, 코드를 실행하고, MCP server를 통해 외부 도구를 호출합니다. 따라서 보안팀은 “모델이 무엇을 답했나”뿐 아니라 “어떤 로컬 agent가 설치되어 있는가”, “어떤 MCP server가 활성화되어 있는가”, “어떤 prompt가 도구 실행으로 이어졌는가”를 봐야 합니다.

특히 prompt injection 방어는 콘텐츠 필터링 수준을 넘어 런타임 정책이 되어야 합니다. 문서 안의 악성 지시, 웹페이지에 숨어 있는 명령, repo 설정 파일에 들어간 tool-call 유도 문구가 에이전트의 실행 경로를 바꿀 수 있기 때문입니다. 앞으로 엔터프라이즈 AI 운영에서 EDR, SIEM, DLP, IAM이 agent telemetry를 흡수하는 것은 자연스러운 흐름입니다. 보안 제품은 사람의 클릭만 추적하는 것이 아니라 에이전트의 도구 호출, 거부된 실행, 권한 상승 시도까지 기록해야 합니다.

비용 통제도 같은 방향으로 움직입니다. Google SecOps Security Tokens, GitHub cost center별 AI credit cap, Vercel AI Gateway routing rules는 모두 에이전트 호출이 예산·권한·라우팅 정책의 대상이 된다는 신호입니다. 운영 관점에서는 보안과 FinOps가 분리되지 않습니다. 고위험 조사에는 고비용 모델을 허용하되 로그와 human handoff를 강제하고, 낮은 위험 작업에는 저비용 모델과 제한된 도구만 허용하는 식의 정책이 필요합니다.

결론은 명확합니다. 2026년의 agent governance는 문서화된 원칙이 아니라 **런타임에서 실제로 집행되는 통제면**이어야 합니다. 에이전트 도입을 검토하는 팀은 먼저 로컬 agent/MCP inventory, tool-call allowlist, prompt injection 차단, cost cap, audit log를 하나의 운영 체크리스트로 묶어야 합니다. AI가 더 많은 일을 대신할수록, 사람의 책임 구조와 런타임 통제는 더 강해져야 합니다.

참고:

- Microsoft Security Blog: What's new in Microsoft Security — https://www.microsoft.com/en-us/security/blog/2026/06/30/whats-new-in-microsoft-security-june-2026/
- Reuters: Argentina's plan for AI-run companies can't avoid humans — https://www.reuters.com/world/americas/argentinas-plan-ai-run-companies-cant-avoid-humans-2026-07-03/
- Google Cloud release notes: SecOps Security Tokens — https://docs.cloud.google.com/release-notes#July_02_2026
