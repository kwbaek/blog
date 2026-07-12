---
title: "Hermes/리오 팁: AI 크론에 현장 배치 카드를 추가하자"
date: 2026-07-12T21:00:00+09:00
draft: false
categories: ["openclaw"]
tags: ["Hermes", "리오", "Cron", "AI Automation", "Forward Deployment", "Workflow", "Verification"]
comments: true
---

Hermes/리오로 AI 트렌드나 업무 자동화 크론을 만들 때 가장 흔한 실수는 “정보를 잘 모아 요약하면 일이 끝난다”고 생각하는 것입니다. 실제 현업에서는 좋은 요약 뒤에 데이터 연결, 담당자 확인, 시스템 입력, 예외처리, 결과 검증이 이어집니다. 자동화가 이 마지막 구간을 다루지 못하면 매일 실행돼도 업무를 바꾸지 못하는 알림 봇에 머뭅니다.

이 문제를 줄이는 방법은 각 크론에 **현장 배치 카드(forward deployment card)**를 붙이는 것입니다. 카드는 멋진 에이전트 설계 문서가 아니라, 자동화가 어떤 업무 환경에 들어가 무엇을 읽고 어디까지 행동하며 무엇으로 완료를 증명할지를 한눈에 보여주는 운영 계약입니다.

```yaml
forward_deployment_card:
  business_goal: "의사결정에 필요한 신규 AI 신호를 매일 선별"
  inputs:
    - approved trend sources
    - recent dedupe history
  allowed_actions:
    - read sources
    - write local report artifacts
  prohibited_actions:
    - unapproved external publishing
    - destructive system changes
  completion_evidence:
    - source links verified
    - duplicate check passed
    - output format validated
  fallback:
    - use recent local cache when channel history is unavailable
  escalation:
    - report one blocker and one next action
```

## 모델 지시문보다 업무 경계를 먼저 적기

자동화 품질은 프롬프트 문장만 다듬는다고 안정되지 않습니다. 입력 출처가 불명확하면 오래된 기사를 새 소식으로 오인할 수 있고, 허용 행동이 모호하면 보고만 해야 할 작업이 외부 게시로 이어질 수 있습니다. 완료 증거가 없으면 파일을 작성했지만 빌드되지 않았거나, 빌드됐지만 실제 공개 페이지에 반영되지 않은 상태를 성공으로 착각하게 됩니다.

따라서 카드는 최소 여섯 가지를 포함해야 합니다. 첫째는 사업 목적, 둘째는 허용된 입력, 셋째는 행동 범위, 넷째는 금지된 부작용, 다섯째는 완료 증거, 여섯째는 실패 시 fallback과 중단 조건입니다. 특히 “작성”과 “배포”, “보고”와 “실행”을 서로 다른 권한으로 분리하면 unattended cron의 위험을 크게 낮출 수 있습니다.

## 실행 결과를 증거 묶음으로 남기기

Hermes/리오는 단순히 최종 문장을 만드는 역할보다 **실행 결과를 검증 가능한 묶음으로 만드는 역할**에서 더 큰 가치를 냅니다. 블로그 크론이라면 원문 출처, 생성된 마크다운, 빌드 성공, 새 HTML 존재 여부, 공개 문구 점검, 저장소 반영 상태가 증거가 됩니다. 트렌드 크론이라면 게시 시각, canonical URL, 핵심 수치, 중복 기준, 최신 캐시 반영 여부가 증거가 됩니다.

여기서 중요한 원칙은 모든 도구 로그를 공개하는 것이 아닙니다. 내부에는 충분한 검증 흔적을 남기되, 사용자에게는 필요한 결과와 실패 원인만 전달해야 합니다. 로컬 환경 정보나 운영 식별자가 공개 글에 섞이지 않도록 최종 산출물만 별도로 검사하는 단계도 카드에 포함하는 편이 좋습니다.

기업 AI가 데모에서 현장 배치로 이동하듯, 개인 자동화도 프롬프트에서 운영 계약으로 이동해야 합니다. 현장 배치 카드를 붙이면 Hermes/리오 크론은 “무언가를 생성하는 작업”을 넘어 **업무 경계 안에서 실행하고, 확인하고, 복구할 수 있는 작은 운영 시스템**이 됩니다.
