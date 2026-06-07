---
title: "AI/ML 트렌드: 코드리뷰 에이전트에는 더 많은 코멘트가 아니라 보정이 필요하다"
date: 2026-06-07T21:00:00+09:00
draft: false
categories: ["ai-ml"]
tags: ["AI", "ML", "Agent", "Code Review", "Developer Tools", "Security", "Evaluation", "Open Source"]
comments: true
---

오늘 AI/ML 트렌드에서 눈에 띈 신호는 **코딩 에이전트가 코드 생성 도구에서 리뷰 품질 관리 도구로 확장되고 있다**는 점입니다. Alibaba가 공개한 Open Code Review는 AI code review agent를 단순한 “PR에 LLM 붙이기”가 아니라 rule matching, sub-agent 분할, line-level comment pipeline으로 설계합니다. Hugging Face의 Her는 Claude Code 세션 JSONL을 읽어 도구 호출, 토큰 사용, 위험한 운영 변경, 민감 문자열 관련 행동을 추적하는 감사 도구입니다. 둘을 함께 보면, 앞으로 중요한 질문은 “에이전트가 리뷰를 할 수 있는가”가 아니라 **그 리뷰가 얼마나 일관되고, 덜 시끄럽고, 나중에 감사 가능한가**입니다.

AI 코드리뷰의 첫 번째 병목은 정확도보다 노이즈입니다. 사람 리뷰어는 적은 수의 중요한 코멘트를 원하지만, LLM은 자신감 있게 많은 말을 만들기 쉽습니다. 사소한 스타일 지적, 이미 테스트가 막고 있는 문제, 프로젝트 컨벤션과 맞지 않는 일반론이 늘어나면 개발자는 에이전트 코멘트를 무시하기 시작합니다. 그래서 Open Code Review처럼 rule과 sub-agent를 나누는 접근이 중요합니다. 보안, 성능, API 호환성, 문서화, 테스트 누락을 한 모델의 자유응답에 맡기기보다, 각 축을 별도 관점으로 쪼개고 최종 코멘트는 PR 맥락에 맞게 걸러야 합니다.

두 번째 병목은 리뷰 근거입니다. AI가 “이 코드는 위험하다”고 말했을 때, 어떤 diff line, 어떤 규칙, 어떤 과거 incident, 어떤 도구 실행 결과에 근거했는지가 남아야 합니다. Her 같은 세션 감사 도구가 흥미로운 이유도 여기에 있습니다. 코딩 에이전트가 파일을 읽고, 명령을 실행하고, 수정안을 만들었다면 그 과정은 나중에 재생 가능해야 합니다. 특히 CI/CD나 production-adjacent 자동화에 붙는 에이전트는 결과물만 보면 부족합니다. 어떤 입력을 신뢰했고, 어떤 도구를 호출했고, 어디서 권한 경계를 넘지 않았는지를 확인할 수 있어야 조직이 안심하고 확장할 수 있습니다.

세 번째 병목은 보정(calibration)입니다. 좋은 리뷰 에이전트는 단순히 더 강한 모델을 쓰는 것이 아니라, 팀의 실제 리뷰 기준에 맞춰 코멘트 임계값을 조절합니다. 예를 들어 보안 취약점 의심은 민감하게 잡되, 스타일 코멘트는 formatter가 처리하게 넘기고, architecture 변경은 사람에게 escalation하는 식입니다. PR 크기, 작성자 숙련도, repo 위험도, 배포 빈도에 따라 코멘트의 깊이도 달라져야 합니다. AI 리뷰 품질은 benchmark 점수보다 “이 팀에서 merge decision을 실제로 더 좋게 만들었는가”로 평가해야 합니다.

오늘의 결론은 간단합니다. 코드리뷰 에이전트의 경쟁력은 말 잘하는 모델이 아니라 **코멘트를 줄이고, 근거를 남기고, 팀 기준에 맞게 보정되는 운영 시스템**에서 나옵니다. 개발 조직이 AI 리뷰를 도입한다면, 먼저 “어떤 코멘트가 가치 있는가”, “어떤 증거가 남아야 하는가”, “언제 사람에게 넘길 것인가”를 정의해야 합니다. 그다음에 모델과 도구를 붙이는 편이 훨씬 안전합니다.

참고 링크:

- Alibaba Open Code Review: <https://github.com/alibaba/open-code-review>
- Hugging Face: Her — Claude Code session audit: <https://huggingface.co/blog/build-small-hackathon/her-blog>
