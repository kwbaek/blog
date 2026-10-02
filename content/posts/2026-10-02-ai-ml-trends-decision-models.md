---
title: "에이전트의 '다음 한 걸음'은 작은 모델이 고른다 — 결정 모델(Decision Model)의 부상"
date: 2026-10-02T21:05:00+09:00
draft: false
categories: ["ai-ml"]
tags: ["Decision-Model", "Cloudflare", "AWS", "Strands", "OpenAI", "Agents", "Inference-Cost", "Ramp-AI-Index"]
comments: true
---

이번 주 #ai-ml-trends 채널에서 가장 눈에 띈 흐름은 새로운 프런티어 모델이 아니라 **작은 모델의 새로운 역할**입니다. 이틀 사이에 AWS와 Cloudflare가 각각 오픈소스 "결정 모델(decision model)"을 내놓았고, TechCrunch에 따르면 OpenAI도 같은 주에 비슷한 Decisions API를 프리뷰로 공개했습니다. 글을 쓰는 모델이 아니라 **고르는 모델**이 하나의 제품군으로 자리 잡는 중입니다.

## 결정 모델이란 무엇인가

Cloudflare의 설명을 빌리면 결정 모델은 "확률을 바탕으로 에이전트가 어떻게 행동할지 정하도록 분류를 수행하는 모델"입니다. 고객 문의를 입력으로 주고 "긴급한가? 어느 팀이 맡아야 하나?"를 물으면, 자유 서술이 아니라 **정해진 형식의 답과 확률**이 돌아옵니다. 코드는 이 값으로 티켓을 라우팅하거나, 에스컬레이션하거나, 사람에게 넘길지 정합니다.

TechCrunch가 소개한 AWS Strands Decider 2B도 같은 발상입니다. 미리 정해 둔 선택지 중 하나를 고르고, 그 선택에 얼마나 확신하는지를 함께 내놓습니다. Qwen3.5-2B의 몸통 위에 텍스트 생성 대신 보정된(calibrated) 선택을 출력하도록 만든 모델이고, 로컬에서 돌릴 수 있을 만큼 작습니다. AWS의 Marc Brooker는 "워크플로 단계에서 '지금 위치에서 다음에 할 일이 무엇인가'를 정하는 완벽한 결정자"라고 설명했습니다.

## 세 회사가 같은 방향을 가리킨다

- **AWS Strands Decider 2B** — 닫힌 선택지, 확신도 출력, 로컬 실행 가능. 에이전트 비용과 지연을 줄이는 쪽에 초점이 있습니다.
- **Cloudflare Clef / Clef-flash** — Apache 2.0으로 Hugging Face에 공개되고 Workers AI에서도 쓸 수 있습니다. 이미지를 받는 비전 인코더와 64k 컨텍스트를 갖췄고, Jev API와 호환됩니다. 여기에 고객이 자기 용도에 맞게 Clef를 파인튜닝하는 강화학습(RL) 제품도 함께 나왔습니다.
- **OpenAI Decisions API(프리뷰)** — TechCrunch 보도 기준으로 같은 주에 비슷한 기능이 나왔습니다.

세 사례를 묶는 기준 모델은 TypeSafe의 Jev입니다. 이 기준이 생기면서 Cloudflare가 "Jev Decision Index"로 순위를 비교하는 단계까지 왔습니다. TechCrunch는 Jev 이후 비슷한 모델이 수십 개 나왔다고 전합니다.

## 숫자로 보면 무엇이 달라지나

Cloudflare가 직접 밝힌 사례가 이 흐름을 잘 보여 줍니다. 위협 인텔리전스 팀이 웹사이트 도메인을 분류하는 작업에서, Clef는 페이지를 가져와 렌더링하고 분류하기까지 **2.2초**가 걸렸습니다. 같은 워크플로에서 가장 빠른 범용 LLM인 gpt-oss-120b는 **4.7초**가 걸렸고 분류 결과도 **2개**만 돌려줬습니다. 반면 Clef는 "패션 사이트 95%, 이커머스 85%, 피싱 1% 미만"처럼 여러 라벨을 확률과 함께 냈습니다.

벤치마크 표에서도 지연 시간 차이가 드러납니다. 중앙값 지연이 Clef는 약 209ms, Clef-flash는 약 39ms였고, 비교 대상인 Jev는 약 524ms였습니다. 다만 이 수치는 Cloudflare가 자사 모델을 중심으로 공개한 결과입니다. 품질 항목에서도 When2Call처럼 Jev가 앞선 항목이 있어서, "항상 더 낫다"가 아니라 **작업 유형에 따라 다르다**고 읽어야 합니다.

## 왜 지금인가 — Ramp AI Index가 보여 준 비용 압력

같은 날 The Decoder가 소개한 Ramp AI Index는 이 흐름의 배경을 보여 줍니다. 미국 기업의 AI 사용량은 7월 지출 정점 이후 약 50% 늘어 9월 말 최고치를 기록했는데, 지출은 오히려 줄었습니다. 상위 모델의 가격 인하와 효율적인 경량 모델이 원인으로 꼽혔습니다. 9월 마지막 주 토큰 지출 점유율은 Anthropic 51%, OpenAI 44.5%였고, 오픈소스 모델은 5% 미만이었습니다. 다만 API 지출 기준이고 대형 고객에 치우친 표본이라는 한계가 있습니다.

사용량이 늘수록 모든 단계에 풀사이즈 LLM을 쓰는 방식은 부담이 커집니다. 분기 판단처럼 **답이 닫혀 있고 자주 반복되는 단계**를 작은 모델로 빼내는 것은 비용 곡선을 꺾는 가장 직접적인 방법입니다.

## 실무 관점의 해석

1. **LLM 호출을 '생각하는 단계'와 '고르는 단계'로 나눠 보세요.** 모든 단계가 추론을 필요로 하지는 않습니다. 분류, 라우팅, 승인 여부 판단은 후자입니다.
2. **확신도는 기능입니다.** 결정 모델의 가치는 답 자체보다 확률을 함께 준다는 데 있습니다. 임곗값 아래면 사람에게 넘기거나 큰 모델로 재판단하게 만들 수 있습니다.
3. **닫힌 선택지는 검증하기 쉽습니다.** 자유 서술 출력은 평가가 어렵지만, 선택지가 정해져 있으면 정확도와 보정 상태를 숫자로 추적할 수 있습니다.
4. **아직은 초기 시장입니다.** 벤치마크가 사실상 한두 곳의 기준에 모여 있고, 업체가 자기 기준에서 1위를 주장하는 구조입니다. 도입하기 전에 자기 데이터로 직접 재 보는 단계가 필요합니다.

## 한 줄 정리

에이전트 경쟁의 한 축이 "더 똑똑한 모델"에서 **"어느 단계에 얼마나 작은 모델을 쓸 수 있는가"**로 옮겨 가고 있습니다.

## 출처

- Cloudflare Blog, "Introducing Clef: our open-source decision models, and new RL fine-tuning platform" — https://blog.cloudflare.com/clef-decision-models/
- TechCrunch, "Amazon releases its own Jev clone as decision models flood the web" (2026-10-01) — https://techcrunch.com/2026/10/01/amazon-releases-its-own-jev-clone-as-decision-models-flood-the-web/
- The Decoder, "Businesses are using more AI and paying less for it, Ramp AI Index shows" — https://the-decoder.com/businesses-are-using-more-ai-and-paying-less-for-it-ramp-ai-index-shows/
