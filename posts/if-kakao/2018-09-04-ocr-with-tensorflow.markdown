---
layout: post
title:  "[if-kakao] tensorflow로 OCR 개발기"
date:   2018-09-04 11:00:00 +0900
author: Aria
category: if-kakao
---

## 모델 개발을 위한 초석
### Testing is all you need
#### 테스트가 필요한 이유
- 데이터도 모델도 이상이 없는데 결과가 모호하게 나올 때
- 모델을 학습시키는데 데이터의 반정도 돌다가 갑자기 죽음.
- Jupyter를 이용해서 점진적인 모델을 개발하고 테스트하기에 용이, 하지만 서비스하는 입장에서는 취약

#### Module test
- 모델 내부의 모듈은 input/output 결과를 대변하기 힘들다.
- 하지만 dimension test 정도는 가능하다.
{% highlight python %}
tf.test.TestCase
{% endhighlight %}

기본 모델 구현 + test case 작성 ➔ dataset 및 input pipeline 작성 ➔ dataset으로 돌려보고 에러 잡기

#### Testing with small dataset
난이도별 단계별 dataset
1. 적은 vocab, font 하나, 하얀 배경, No transformation
2. 영어 vocab, font 여러개, 단일 배경색, transformation
3. 영어 + 한글 vocab, font 여러개, 배경 추가, transformation

### 정리
- 기본 모델을 만들 때,
  - 모델의 각 모듈을 간단히 테스트할 수 있는 테스트 케이스를 만들자.
  - 데이터셋을 작게 지정해서 실험하자.
  - 런타임 도중 오류가 나는 것은 tf.Print로 디버깅하자.
- 기본 모델이 돌아가기 시작하면,
  - 여러 모델 구조와 dataset으로 실험을 하면서 최적의 set을 찾자.
  - 물론 새로 만드는 모듈들에 대한 테스트케이스도 필요하다.


## 모델 개발 관련 이야기
### 모델 구조
1. Convolutional layer
2. Recurrent layer
3. CTC algorithm

### 모델 구성 요소
#### 1. [Convolutional Neural Network](https://en.wikipedia.org/wiki/Convolutional_neural_network)
**Why CNN?**
- Feature extraction.
- 글자 예측에 유용한 정보를 자동으로 추출 가능.

#### 2. [Recurrent Neural Network](https://en.wikipedia.org/wiki/Recurrent_neural_network)
**Why RNN?**
- Broaden the receptive field.
- CNN의 제한된 수용장을 확장시켜줄 수 있다.
- 각각의 feature cell에서 글자 예측.
- LSTM을 통해 각 feature cell을 조합해 어떤 글자를 의미하는지 알게 됨.
- 이미지의 일부분만을 보고 예측한다면? '아기' ➔ 'orJI'로 디코딩 될 때가 있었음.
- 옆 글자로부터 정보를 얻을 수 있는 상황이라면 내가 예측해야 하는 글자가 어떤 것인지 명확해짐.

#### 3. CTC
**Why CTC?**
- No explicit alignment
- 한 글자에 해당하는 이미지가 어디부터 어디까지 일까?<br/>
  '_대충 10px_' 이런 규칙은 쉽게 망가진다.<br/>
   설명 변수와 예측 변수 사이의 관계 정렬이 되어있지 않을 때 사용한다.
- 그렇다면 어떻게 할까?<br/>
  먼저 매 timestep 마다 결과값을 예측한 후에,
  1. Collapse repeats
  2. Remove blank tokens
  3. Finish
- 확률은 어떻게 계산될까?<br/>
  **CTC Loss**: Dynamic programming으로 all possible combinations을 구해 확률 계산.

### 다양한 구조 실험 & 실패
#### 속도와 성능
##### CTC의 약점
- Conditional independence assumption
- Pooling size 제한: 이미지를 압축하기 위해 pooling을 많이 하는데, 글자를 인식하기 위해서는 사이즈를 많이 줄일 수 없다. 어쩔 수 없이 feature map 사이즈가 커지게 된다.

##### [LSTM](https://en.wikipedia.org/wiki/Long_short-term_memory) 없애기
- 병렬 계산이 불가능한 속도 병목: 다음 셀을 계산하기 위해서는 이전 셀의 누적값을 가지고 있어야 하므로 병렬 계산이 불가하다.

#### 새로운 모형
##### Encoder-Decoder with attention
- 더 많이 pooling할 수 있음.
- 이전 졔측 글자 정보를 이용할 수 있음.<br/>
  ➔ CTC의 약점을 극복하기 위한 시도

##### Dilated Convolution
- vanilla CNN에 비해 더 큰 수용장
- self-attention: feature끼리 더 많은 정보를 공유하게 하면서 병렬적으로 계산을 할 수 있음.<br/>
  ➔ LSTM을 없애보기 위한 시도

속도를 향상시키는건 성공, 하지만 output의 정확도가 많이 떨어지게 되었다.

### 회고
- OCR API 사내 오픈
- 다양한 폰트에 대해 대응 가능
