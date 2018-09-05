---
layout: post
title:  "[if-kakao] 딥러닝을 활용한 뉴스 메타 태깅"
date:   2018-09-04 16:00:00 +0900
author: Aria
category: if-kakao
---

## 배경
- 메타데이터: 컨텐츠의 특성
- 메타데이터 생성과 활용이 잘 되려면 데이터의 유통이 간단해야 한다.
- 메타데이터의 흐름을 시스템화 하자.

## 1. 기사 형태 분류하기(#텍스트분류, #딥려닝모델)
### 기사 형태 분류
- 사실 전달형과 해설/묘사형을 나누는 문제로 정의

#### 사실 전달형
- 육하원칙 위주로 기술
- Straight articles

#### 해설/묘사형 기사
- 사건, 인물에 대한 심층 취재 기사
- 르포, 스케치, 인터뷰 등

### Convolution Neural Network로 기사 형태 분류하기
#### 논문
- **Character based convolution neural Network**
- Text understanding from scratch

#### 모델
- 6 Convolution Layer

#### 모델 선택 이유
- 형태소 분석, word embedding 보다는 character 단위 처리
- 한글, 한자 Romanization: alphabet space 내에서 처리
- 문자와 단어의 순서를 보존하여 기사의 문투를 학습

### 학습 데이터셋 구성 ~ 최종 모델 결과
#### 학습 데이터 구성
정답을 가지고 있지 않은 문제의 정답셋을 만들어 내기
1. 근사 기준
- 기사 형태별 샘플을 구할 수 있는 기준을 찾음.
  - 예: 기사의 길이!
- 완벽한 패턴은 아니므로, 오분류된 데이터가 있을 수 있음.
- 오분류 패턴이 일관되지 않다면 패턴 학습 대세에 영향 적음.

2. 보완책
- 평가 자체가 어렵다.
- 뉴스 도메인 전문가의 도움을 받자.
- 학습 데이터, 테스트 데이터 검수 및 라벨링

#### 시험을 보자
- 학습 데이터
  - 총 뉴스 44.2만건
  - 사실전달: 해설/묘사 : 기타 = 10 : 1 : 10
  - 제목+본문 concat ➔ 로마자 변환 후 600 char만 사용
- 테스트 결과
  - Validation Accuracy: 79.3%
  - Test Accuracy: 79.8% 하지만 해설/묘사 Recall: 42.5%
  - 특징: 해설/묘사형 기사의 수가 너무 적어서 분류가 잘 되지 않았다.
- 해당 카테고리의 기사들을 더 모으자.

#### 2차 시험
- 학습 데이터
  - 총 뉴스 55만건
  - 사실전달: 해설/묘사 : 기타 = 2 : 1 : 2
  - 제목+본문 concat ➔ 로마자 변환 후 600 char만 사용
- 테스트 결과
  - Validation Accuracy: 83.3%
  - Test Accuracy: 82.2% 하지만 해설/묘사 Recall: 62%
  - 특징
    - 사실 전달 기사의 표현을 잘 학습(언제 누가 뭐했다, 보도했다, OO에 따르면)
    - 해설/묘사 기사 중에서도 해설 기사나 인터뷰 기사를 높은 확률 값으로 분류, but 재현율이 낮음
- 기사 본문을 더 길게 학습 해보자!

#### 3차 시험
- 학습 데이터
  - 총 뉴스 55만건
  - 사실전달: 해설/묘사 : 기타 = 2 : 1 : 2
  - 제목+본문 concat ➔ 로마자 변환 후 1014 char 사용 (논문에서 1014 char을 사용했을 때 가장 좋다고 나와서)
- 테스트 결과
  - Validation Accuracy: 82%
  - Test Accuracy: 81% 하지만 해설/묘사 Recall: 74%
  - 특징
    - 해설/묘사 기사 재현율 상승, 연재, 기획코너 기사를 높은 확률 값으로 분류
    - 600char 학습시 '기타'기사로 잘 분류하던 기사들을 헷갈려하는 경우도 있음.

### 모델 앙상블
학습 모델마다 특성이 다르므로 이를 결합하여 결과 변동성을 줄임.
모델이 서로 놓치는 부분을 보완해줌.

## 2. 뉴스 메타 태깅 시스템(#딥러닝개발, #메타데이터플랫폼)
### 목표
- 실시간 프로세싱: 기사가 들어오는 대로 태그 생성
- 메타 태그 생성의 비동기성: 다양한 조직과 플랫폼이 메타 데이터 생성에 참여하므로 생성이 비동기적으로 이루어져야 한다.(예외: 메타데이터간 의존성이 있는 경우)
- 메타 태그 생성 기술의 다양성: ML, DL 플랫폼의 다양성 수용

### 흐름
- 메타 데이터의 생성과 소비가 함께 이루어지는 흐름
- news topic에서 콘텐츠와 meta data 흐름을 모두 tracking
- contents + meta data ➔ new meta data

### System Architecture
- Meta data 생성 = Ensemble - Inferrer API Services
- used kafka, DC/OS
#### Inferrer API
- ML/DL 모델을 서비스하는 API
- 모델 단위로 API 개발, Dockerizing 하여 학습 모델 + 코드 버전 관리
- 모델 추론 이외의 기능은 최소화
  - API개발 공수 최소화/모델에 집중

#### Ensemble API
- Inferrer API를 호출해서 최종 모델 추론 결과를 리턴하는 API
  - 각 모델에 필요한 입력 값(feature) 생성
- 메타 데이터 서비스의 gateway
- 구현
  - spring-webflux로 개발: 병렬-비동기 처리 구조의 이점

#### Message Flow Control
- Filtering - Invoke Ensemble - Post process - Produce
- 메타데이터 태그 토픽에 태그 발행
- 메타데이터가  늘어날 수록 반복되는 비슷한 작업들

##### Apache Nifi를 사용
- Data Flow 개발이 간단
- 각 처리 단계별로 in/out 처리량과 데이터 모니터링
- nifi-registry와 연동시 data-flow 형상 관리 가능

## 향후 과제
- 딥러닝 모델 품질 관리: 오분류 사례 수집 및 모델 재학습
- 다양한 메타데이터 추가. 기사 내 사진, 차트, 인포그래픽 분석
