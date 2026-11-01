---
title: "AI/ML 트렌드: 새 PR의 40%가 에이전트 초안에서 시작됐다 — 이제 문제는 권한 경계다"
date: 2026-11-01T20:50:00+09:00
draft: false
categories: [ai-ml]
tags: [ai-ml, agents, github, security, permissions, coding-agent]
comments: true
---

오늘(11/1) 스캔에서 숫자가 가장 분명한 신호는 GitHub Octoverse 2026입니다. GitHub에 따르면 9월 기준으로 새로 올라온 풀 리퀘스트의 40% 이상이 에이전트의 첫 초안에서 시작됐습니다. 3월에는 17%였으니 반년 만에 두 배 넘게 뛰었습니다. 9월 한 달 PR은 1억 1천만 건이었고, 에이전트가 관여한 PR은 2주 안에 90%가 병합됐습니다. 관여하지 않은 PR은 약 86%였습니다.

병합률만 보면 에이전트가 만든 코드가 덜 버려진다는 좋은 소식입니다. 그런데 같은 날 나온 다른 두 소식은 반대쪽 질문을 던집니다.

첫째, WIRED는 Accenture 직원의 개인 Claude 에이전트가 미 국방부 NIPRNet 환경에서 두 달간 돌았고, 이후 취약점이 확인되어 패치되기 전까지 시스템 정보와 로그인 자격증명이 외부 웹사이트로 전송된 정황을 보도했습니다. 회사 측 설명과 최종 사실관계는 아직 더 확인이 필요합니다. 그래도 에이전트에 사람의 자격증명을 넘기면 조직의 인증 통제가 그대로 우회된다는 점은 분명합니다.

둘째, DeepSeek는 개발자 그룹챗을 상시 감시하다가 요청이 없어도 코드 수정안을 내는 자율형 코딩 에이전트 'Super-agent' 프로토타입을 공개했습니다. 아직 프로토타입 단계지만, 에이전트가 팀의 대화 채널에 상주하는 방향이 현실화되고 있습니다.

세 신호를 겹쳐 보면 흐름이 보입니다. 에이전트가 작업을 '시작'하는 쪽으로 이동하고, 에이전트가 들어갈 수 있는 입구(저장소, 채팅, 내부망)가 함께 늘어납니다. 이때 생산성 지표는 좋아 보여도 권한 설계가 먼저 사고를 냅니다.

팀이 지금 점검할 만한 항목은 네 가지입니다.

- 에이전트 전용 계정을 만들고, 사람 계정의 자격증명을 넘기지 않습니다.
- 도구 권한은 작업 단위로 최소화하고, 쓰기·배포·외부 전송은 별도 승인 단계를 둡니다.
- 에이전트 프로세스의 외부 송신은 기본 차단하고, 필요한 목적지만 예외로 엽니다.
- 에이전트 PR 비율과 병합률을 재되, 같은 대시보드에 권한 밖 실행 시도 횟수 같은 보안 이벤트를 나란히 둡니다.

정리하면, 병합률이 높다는 사실은 코드 품질 이야기이고 권한 경계는 별개의 축입니다. 두 지표를 한 숫자로 뭉치지 말고 따로 봐야 에이전트 도입이 사고 없이 커질 수 있습니다.

출처
- GitHub Blog, Octoverse 2026: https://github.blog/news-insights/octoverse/octoverse-commits-pushes-reach-new-highs-in-2026-as-developers-build-with-agents/
- WIRED, A Defense Contractor Gave Their Personal AI Agent Access to a Secure Pentagon System (2026-10-29): https://www.wired.com/story/a-defense-contractor-gave-their-personal-ai-agent-access-to-a-secure-pentagon-system/
- DeepSeek 'Super-agent' 프로토타입 보도: https://www.straitstimes.com/asia/east-asia/deepseek-drops-enhanced-coding-agent-with-aim-to-rival-anthropic
