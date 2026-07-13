---
title: "AI/ML 트렌드: 에이전트의 '완료'가 응답 생성에서 실행 정산으로 이동한다"
date: 2026-07-13T21:00:00+09:00
draft: false
categories: ["ai-ml"]
tags: ["AI", "ML", "Agent", "OpenAI Agents SDK", "Kimi Code", "Reliability", "Sandbox", "Observability"]
comments: true
---

오늘 주목할 AI/ML 트렌드는 새 모델 발표가 아니라 **장기 실행 에이전트의 '완료'를 다시 정의하는 흐름**입니다. 같은 날 공개된 두 릴리스가 같은 곳을 가리킵니다. OpenAI Agents SDK 0.18.2는 hosted multi-agent와 함께 Daytona·Docker·Unix PTY sandbox에서 worker, task, file descriptor의 소유권과 정리(cleanup)를 강화했고, Kimi Code 0.23.6은 print-mode 실행에서 background 작업·active goal·pending cron·장시간 subagent가 남아 있으면 agent turn이 조기에 끝나지 않도록 lifecycle을 보강했습니다.

두 변경의 공통 메시지는 분명합니다. **에이전트가 마지막 문장을 반환한 것과, 그 에이전트가 벌인 모든 일이 실제로 끝난 것은 다른 사건**이라는 점입니다.

## '답을 냈다'와 '일이 끝났다'는 다르다

지금까지 에이전트 신뢰성은 대부분 출력 품질로 측정됐습니다. 답이 정확한가, 형식이 맞는가, 근거가 있는가. 하지만 에이전트가 여러 하위 작업을 돌리고 격리된 실행 환경을 쓰기 시작하면, 화면상 대화가 끝난 뒤에도 다음이 남을 수 있습니다.

- 아직 돌고 있는 background 프로세스와 subagent
- 회수되지 않은 sandbox worker, 열린 file descriptor, 임시 파일
- 마운트된 채 남은 secret, 정리되지 않은 credential 접근
- 예약됐지만 정산되지 않은 cron·continuation 작업

이 잔존물은 오류 메시지를 남기지 않습니다. 그래서 더 위험합니다. 조용히 비용을 새게 하고(유휴 compute), 보안 표면을 넓히며(남은 secret 접근), 다음 실행과 충돌합니다. OpenAI가 자원 ownership과 cleanup을 손보고, Kimi Code가 pending work가 끝날 때까지 turn을 유지하도록 바꾼 것은 이 사각지대를 제품 계약으로 끌어올린 조치입니다.

## 완료의 기준이 '실행 그래프의 종결'로 확장된다

앞으로 에이전트의 완료는 두 축으로 나눠 봐야 합니다. 하나는 **논리적 종결** — 요청한 task가 성공·부분성공·실패·중단 중 무엇으로 끝났는가. 다른 하나는 **자원 정산(settlement)** — 그 과정에서 만든 프로세스·파일·FD·secret·예약 작업이 모두 회수됐는가.

실무에서는 root goal, subagent, background 프로세스, scheduled continuation을 하나의 execution graph로 추적하고, 미정산 노드가 하나라도 있으면 "완료" 통지와 비용 청구를 막는 gate가 필요합니다. 릴리스 테스트에도 정상 완료뿐 아니라 취소, timeout, worker crash, 사용자 중단 시나리오를 넣고, 그 뒤 process·task·FD·임시파일·secret mount의 잔존 여부를 직접 측정해야 합니다.

## 왜 지금 이 신호가 중요한가

이 변화는 최근 몇 달 AI/ML 흐름과 정확히 맞물립니다. 모델 성능 경쟁이 어느 정도 평준화되면서, 실제 도입의 병목은 "얼마나 똑똑한가"가 아니라 "얼마나 오래, 안전하게, 예측 가능하게 굴러가는가"로 옮겨가고 있습니다. 같은 날 나온 다른 신호들 — LiteLLM이 DLP·MCP OAuth를 gateway에 통합한 것, llama.cpp가 protocol 변환 중 이미지 유실을 수정한 것 — 도 결국 **실행면·복구·정산 통제를 강화하는** 같은 방향입니다.

에이전트를 데모에서 상시 운영으로 넘기려는 팀이라면, 성공 지표에 출력 정확도만 두어서는 안 됩니다. **실행 그래프가 완전히 종결됐는지, 자원이 남김없이 정산됐는지, 실패 시 롤백이 가능한지**를 하나의 acceptance scorecard로 검증하는 것 — 그것이 올해 에이전트 신뢰성의 새 기준선이 될 것입니다.

참고:

- OpenAI Agents SDK v0.18.2 — https://github.com/openai/openai-agents-python/releases/tag/v0.18.2
- Kimi Code 0.23.6 — https://github.com/MoonshotAI/kimi-code/releases/tag/%40moonshot-ai/kimi-code%400.23.6
- LiteLLM v1.92.0 (DLP·MCP OAuth) — https://github.com/BerriAI/litellm/releases/tag/v1.92.0
