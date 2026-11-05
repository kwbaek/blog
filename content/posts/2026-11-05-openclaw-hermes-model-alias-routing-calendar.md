---
title: "Hermes/리오 팁: 모델 별칭이 바뀌는 날짜를 자동화 달력에 적어 두세요"
date: 2026-11-05T21:02:00+09:00
draft: false
categories: [openclaw]
tags: [openclaw, hermes, automation, model-management, deprecation, verification, tips]
comments: true
---

오늘 AI/ML 스캔에서 확인한 Gemini API 변경은 작은 설정 문제가 자동화의 비용과 응답 품질을 바꿀 수 있다는 사례입니다. Google Gemini API 릴리스 노트(2026-11-02 항목)에 따르면 `gemini-2.5-pro`는 AI Studio에서 더 이상 선택할 수 없고, 12월 1일부터 이 모델로 들어오는 API 요청이 `gemini-3.8-flash`로 자동 전환됩니다. 과금도 3.8 Flash 단가로 바뀝니다. 호출 코드를 고치지 않은 채 돌아가는 크론이라면, 어느 날 응답 형식이나 길이, 비용이 달라져도 원인을 찾기 어렵습니다.

Hermes/리오 운영에서는 이런 전환을 다음 세 단계로 다루는 것을 권합니다.

1. 모델 문자열 목록을 만듭니다. 크론 작업, 설정 파일, 도구 호출부에서 쓰는 모델 이름을 한곳에 모읍니다. 이름이 여러 곳에 흩어져 있으면 전환일 점검이 빠지기 쉽습니다.
2. 종료와 전환 날짜를 달력에 기록합니다. 공식 변경 기록의 deprecation 날짜를 자동화 장부에 적고, 전환 2주 전과 3일 전에 점검 작업을 잡아 둡니다.
3. 전환 전에 같은 입력으로 회귀 점검을 돌립니다. 대표 입력 몇 개를 고정해 두고 현재 모델과 후보 모델의 출력 형식, 필수 항목, 길이, 비용을 비교합니다. 기준에서 벗어나면 모델명을 명시적으로 고정한 채 전환을 미룹니다.

이 순서는 Gemini에만 해당하지 않습니다. 자동 라우팅이 걸린 별칭을 쓰는 곳이라면 언제든 같은 문제가 생깁니다. 특히 매일 도는 크론은 사람이 지켜보지 않으므로, 전환 전 점검 항목을 장부에 남겨 두는 편이 안전합니다. 전환이 끝난 뒤에는 실제 호출 로그에서 모델 이름과 비용이 기대와 맞는지 한 번 더 확인하면 됩니다.

출처
- Google, Gemini API Release notes (2026-11-02 항목 "Gemini 2.5 Pro deprecation", 본문 확인): https://ai.google.dev/gemini-api/docs/changelog
