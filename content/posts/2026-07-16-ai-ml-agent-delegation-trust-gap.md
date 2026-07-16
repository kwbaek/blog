---
title: "에이전트에게 위임할수록 '보이지 않는 지점'이 늘어난다 — 델리게이션과 신뢰 인프라"
date: 2026-07-16T21:10:00+09:00
draft: false
categories: ["ai-ml"]
tags: ["AI Agents", "Delegation", "Enterprise AI", "IAM", "Codex", "Anthropic", "VentureBeat", "Oak"]
comments: true
---

오늘 AI/ML 트렌드 채널에 올라온 소식들을 겹쳐 놓고 보면 하나의 공통된 그림이 드러납니다. 에이전트가 더 많은 일을 자율적으로 처리할수록, 사람이 그 과정을 들여다볼 수 있는 창구는 오히려 줄어들고 있다는 점입니다. 오늘의 세 가지 소식 — OpenAI Codex의 서브에이전트 지시문 암호화, 이스라엘 스타트업 Oak의 AI 전용 신원관리(IAM) 6000만달러 시드 펀딩, 그리고 VentureBeat의 엔터프라이즈 에이전트 설문 — 은 각각 다른 각도에서 같은 문제를 말합니다.

첫 번째 소식은 OpenAI Codex가 GPT-5.6 기반 서브에이전트("Sol", "Terra" 등으로 알려진) 사이의 위임 지시문을 암호화하기 시작했다는 것입니다. The Decoder 보도에 따르면, 이는 모델 증류(distillation)나 무단 복제를 막기 위한 보호 조치이지만, 부작용으로 개발자가 로컬에서 서브에이전트 간에 실제로 어떤 지시가 오갔는지 감사(audit)할 수 없게 됩니다. 즉, 에이전트 시스템의 성능을 지키기 위한 선택이 투명성을 희생시키는 트레이드오프가 실제로 프로덕션 코딩 도구에서 벌어지고 있는 것입니다.

두 번째 소식은 정반대 방향에서 문제를 풀려는 시도입니다. TechCrunch에 따르면 Oak는 Accel, CRV, Greylock 등으로부터 6000만달러 시드 펀딩을 유치하며 스텔스에서 나왔습니다. Oak가 풀려는 문제는 "AI 에이전트가 무엇을 할 수 있고, 무엇을 대신 인증받았는지"를 관리하는 것 — 다시 말해 사람 중심으로 설계된 전통적 IAM(신원 및 접근관리)이 자율적으로 파생되고 실행되는 에이전트 계정 앞에서 무너지고 있다는 진단입니다. 서브에이전트가 암호화된 지시로 움직이는 세상에서는, 최소한 "누가 무슨 권한으로 실행했는가"를 추적하는 계층이 따로 필요해지는 셈입니다.

세 번째 소식은 이 간극이 실제로 얼마나 넓은지를 숫자로 보여줍니다. VentureBeat이 소개한 Pulse Research의 기업 101곳 설문에서, 엔터프라이즈 AI 에이전트의 약 40%가 Anthropic Claude 기반으로 구축되고 있다는 결과가 나왔습니다. 그런데 같은 설문의 더 중요한 발견은 따로 있습니다 — 응답 기업 대다수가 "에이전트"라고 부르는 것이 실제로는 단일 턴 챗봇을 감싼 래퍼(wrapper) 수준에 머물러 있다는 점입니다. 진짜 문제는 플랫폼 선택이 아니라 배포(deployment) 성숙도라는 것이 VentureBeat의 결론입니다.

이 세 소식을 묶으면 하나의 실무 시사점이 나옵니다. 지금 시점의 에이전트 도입에서 병목은 "어떤 모델을 쓸 것인가"가 아니라 "위임한 작업을 누가, 어떤 권한으로, 어디까지 감사 가능하게 실행했는가"입니다. 서브에이전트 지시가 암호화되고, 신원 관리가 별도 스타트업으로 분리되고, 대다수 기업의 "에이전트"가 사실은 챗봇 래퍼라는 세 가지 사실은 결국 같은 결론을 가리킵니다: 실행 능력은 이미 앞서 있고, 그 실행을 둘러싼 신뢰·감사·권한 인프라가 아직 따라잡지 못하고 있다는 것.

개인이나 소규모 팀에서 에이전트 자동화를 설계할 때도 같은 원칙이 적용됩니다. 에이전트가 무엇을 대신 실행했는지 로그로 남기고, 위임 단계마다 "이 작업은 왜 실행됐는가"를 재구성할 수 있는 최소한의 감사 흔적을 남기는 것이 모델 선택보다 먼저 챙겨야 할 기본기입니다. 자율성이 늘어날수록, 그 자율성을 사후에 설명할 수 있는 능력이 함께 늘어나야 안전합니다.

**출처:**
- The Decoder: [OpenAI's Codex now encrypts instructions between AI agents](https://the-decoder.com/openais-codex-now-encrypts-instructions-between-ai-agents-leaving-developers-blind-to-internal-delegation/)
- TechCrunch: [Backed by $60M in funding, Oak steps out of stealth](https://techcrunch.com/2026/07/15/backed-by-60m-in-funding-oak-steps-out-of-stealth-to-fix-the-identity-mess-that-ai-agents-are-making-worse/)
- VentureBeat: [Agentic orchestration: enterprise AI organizations have a deployment problem, not a platform problem](https://venturebeat.com/ai/agentic-orchestration-enterprise-ai-organizations-have-a-deployment-problem-not-a-platform-problem-and-most-are-calling-chatbots-agents/)
