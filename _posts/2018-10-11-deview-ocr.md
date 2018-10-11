---
layout: post
title:  "Reading AI: From the Bottom to the Learning of a Foreign Lang"
author: Aria
category: "Deview 2018"
---

[발표자료](https://www.slideshare.net/deview/111-ai)
# Background
## 글자를 읽는 단계
- 사람이 읽는 단계: DEVIEW -> (D,E,V,I,E,W) -> 소리내어 읽기
- AI가 읽는 단계: DEVIEW -> (44, 45, 56, 49, 45, 57) -> TTS
  - Text Localization -> Text Recognition -> TTS

## 글자를 읽는다 = 글자 찾기 + 글자 인식 = OCR

# OCR 정복하기
## 1단계: 기술 트렌드 파악
- 논문 스터디
  - 빠른 요약: 핵심 기여 논문 결과 위주로 논문당 최대 2 슬라이드로 정리
  - 세부 리뷰: 결과에 대한 타당성 검증 위주로 논문당 최대 10 슬라이드로 정리
  - 기술 분류

### 기술 분류: 글자 찾기
#### 사전지식
![글자 영역 표현법들]("/img/blog/ocr-1.png")
- POLY(다각형): N 개의 점으로 표현.

#### Regression-based
- 이미지 -> CNN -> 글자 영역 표현 값들
- SSD(Single Shot Detector)로 Textboxes 검출. 종횡비와 밀도를 토대로 검출.
- 최근 들어 논문 수도 감소 중, 성능 재현 어려움. RECT 표현법이 다수

#### Segmentation-based
- 이미지 -> CNN -> 화소 단위 정보 -> Binarization(이진화) -> 연결된 성분 분석 -> RBOX 정합 -> 글자 영역 표현 값들
- 최근 들어 논문 수 급증
- 학습이 안정적
- RBOX 표현법이 절대 다수이나 POLY 표현법이 서서히 증가 중

#### End-to-end
- 기존 글자 찾는 방법과 인식 하는 방법을 한 번에 학습시켜 상호 도움이 되도록 한다.
- 최근 1년 사이 논문 등장
- 성능 재현 어려움
- 안정적인 학습법 연구 필요

### 기술 분류: 글자 인식
#### Text spotting
- N개 단어 분류
- End-to-End에서는 간혹 사용
- 글자 인식 문제에서는 잘 안쓰이는 추세

#### CTC-based
- 이미지 -> CNN(특징 추출) -> RNN (시퀀스 정보)-> CTC
- 규칙 기반 후처리
- 고정 길이 시퀀스 길이에 대해 후처리

```
input이 trip이 적혀있는 이미지라면,

ttrrriipppp
ttrr--i--pp
t-r----i--p
이 CTC를 통해 trip으로 후변환
```
- 최근 논문 수 감소
- 하지만 속도가 빠르고 적은 메모리를 요구하므로 대용량 이미지 프로세싱에는 적합

#### Attention-based
- 가변 길이 시퀀스 생성
- 최근 논문 대부분의 방식
- CTC에 비해 속도는 느리지만 높은 정확도를 자fkd

## 2단계: 논문 구현의 시작!
- 논문 구현 개상 선택: 기술 분류 별로 2개의 논문 선택
- 구현 플랫폼: 각 논문을 pytorch와 tensorflow에 모두 구현.
  - 의도1. 연구 효율성 극대화
  - 의도2. 코드 리뷰 및 각 플랫폼에 대한 이해 증대
  - 의도3. 한 논문에 대해 논의할 사람 확보

## 3단계: 일어 적용
### 일어 특징
- 띄어쓰기가 없어 글자 찾기가 어려움.
- 세로쓰기가 많아 글자 인식이 어려움.
- 한자가 많아 글자 인식이 어려움.
  - 일어의 글자수 대략 3천 개. 3천 개의 클래스가 필요한 것.

### 일본어 띄어쓰기 대응
- 글자 검출은 단어단위가 아니라 글자 단위로 검출

### 일본어 세로쓰기 대응
- 가로쓰기와 세로쓰기에 대한 인식용 네트워크를 따로 둔다.
- pipeline: 이미지 -> 글자 찾기 -> RBOX -> Box별 각도 보정 -> 인식기 선택 -> 가로인식 or 세로인식

---
영어/일어 OCR 데모 페이지: [https://demo.ocrt.clova.ai/](https://demo.ocrt.clova.ai/)
