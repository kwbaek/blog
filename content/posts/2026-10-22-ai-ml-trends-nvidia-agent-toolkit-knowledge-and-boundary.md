---
title: "AI/ML 트렌드: 엔터프라이즈 에이전트는 데이터 지식층과 실행 경계를 같이 가져야 한다 — NVIDIA Agent Toolkit 확장"
date: 2026-10-22T20:50:00+09:00
draft: false
categories: [ai-ml]
tags: [ai-ml, agents, nvidia, enterprise, governance, data-platform, runtime]
comments: true
---

NVIDIA가 GTC Berlin에서 오픈소스 Agent Toolkit을 확장했습니다. 눈에 띄는 점은 모델 성능이 아니라 구성입니다. 기업 데이터를 에이전트가 쓸 수 있는 형태로 만드는 지식 계층(Agent Knowledge Foundation)과, 에이전트가 무엇에 접근하고 무엇을 실행할 수 있는지 정하는 런타임 경계(OpenShell)를 한 묶음으로 내놓았습니다.

## 무엇이 발표됐나

NVIDIA 블로그(2026-10-21) 기준 구성은 다음과 같습니다.

- **Agent Knowledge Foundation**: CUDA-X 데이터 라이브러리(cuDF, cuGraph, cuML, cuOpt, cuVS)와 원시 데이터를 고객·제품·주문 같은 업무 개념으로 바꾸는 Auto-Ontology 라이브러리를 포함합니다.
- **오픈 모델**: Nemotron Retriever(검색·재정렬)와 Kumo 계열 구조화 데이터 모델(Tabular, Relational, Time Series, Forecast)을 제공합니다.
- **OpenShell**: 에이전트가 접근하고 실행할 수 있는 범위를 통제하는 보안 런타임입니다.
- **NemoClaw 블루프린트**: 보안 설계가 들어간 전문 에이전트 참조 설계입니다.

발표문은 Palantir Foundry·AIP, Databricks Genie, Snowflake Notebook 연동 사례를 함께 제시합니다. 검색 쪽에서는 SAP 리랭커가 약 3배 빨라졌고 ServiceNow는 지연 시간을 약 4분의 1 줄였다는 고객 수치가 있습니다.

## 왜 중요한가

에이전트 도입에서 자주 생기는 실패는 두 가지입니다. 하나는 회사 데이터를 모른 채 그럴듯하게 답하는 문제이고, 다른 하나는 데이터를 알게 된 에이전트가 권한 밖의 시스템에 손을 대는 문제입니다. 이번 발표는 이 둘을 각각 지식 계층과 런타임 경계라는 별도 레이어로 나눠 다룬다는 점에서 구조가 분명합니다.

특히 "데이터를 옮기지 않고, 기존 거버넌스 통제를 바꾸지 않고 쓸 수 있다"는 설명은 실무 관점에서 의미가 큽니다. 데이터 이동이 어려운 금융·제조 조직에서는 에이전트 도입 설계를 모델 선택보다 데이터 접근 권한 설계로 시작하게 만들기 때문입니다.

## 과장하지 말아야 할 부분

- 발표는 NVIDIA의 제품 구성 발표이고, 위 성능 수치는 참여 기업이 밝힌 결과입니다. 독립 벤치마크가 아닙니다.
- Auto-Ontology가 실제 기업 데이터에서 업무 개념을 얼마나 정확히 매핑하는지는 공개 자료만으로 확인할 수 없습니다. 온톨로지 품질은 도메인마다 다르므로 파일럿에서 직접 검증해야 합니다.
- 런타임 경계가 있다고 해서 권한 설계가 끝나는 것은 아닙니다. 어떤 도구를 허용할지, 쓰기 작업에 승인 단계를 둘지는 여전히 조직이 정해야 합니다.

## 정리

- 에이전트의 가치는 데이터를 얼마나 잘 아느냐에 달려 있고, 신뢰는 실행 경계가 얼마나 명확하냐에 달려 있습니다.
- 도입 검토 시에는 "어떤 데이터를 읽을 수 있는가"와 "어떤 행동을 실행할 수 있는가"를 별도 체크리스트로 나누는 것이 좋습니다.
- 출처: https://blogs.nvidia.com/blog/enterprise-platforms-agent-toolkit/
