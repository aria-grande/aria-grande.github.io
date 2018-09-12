---
layout: post
title:  "Face Recognition with deep learning"
author: Aria
category: if-kakao
---

# 얼굴 인식 과정
1. Detection
2. Alignment: Facial Landmark Detection
3. Normalization
특정 스케일로 변환하거나 영상/이미지를 회전시켜 정규화시킴.
4. Recognition

# 얼굴 인식 분류
## Identification
- 입력 얼굴과 등록된 얼굴들을 비교해서 누구인지 알려줌.
- Gallery DB 내에서 Probe(입력 영상)을 조회하는 것
- Gallery DB를 구축하면서 문제가 많이 발생한다. 엔지니어링이 필요. (DB 상에서 top1, top2를 뽑을 때 기본적으로는 kNN(k-Nearest Neighbor)을 사용. 다양한 표정? 다양한 포즈?)

## Verification
- 주어진 두 얼굴이 같은지 판단.
- LFW Database(Labeled Faces in the Wild, 2008)를 이용

# 학습 방법
## Feature Embedding for Face Recognition
Train ➔ conv, bn, act, pooling ➔ Dimension reduction ➔ Softmax, Metric learning ➔ classification

### Test
Test ➔ cosine similarity, Euclidean distance

## Metric learning
- Triplet loss(CVPR 2015)
- Center loss(ECCV 2016)
  같은 class의 feature를 한 곳으로 모아 various를 줄이기 위한 방법
- Contrastive loss(NIPS 2014)
- Ring loss(CVPR 2018)
  feature의 크기를 r만큼 준 뒤, r space 내에서만 보이도록 한 방법

## Angular classification
- NormFace(ACMMM 2017)
  Weight Normalization
- Sphere Faces(CVPR 2017)
- CosFace(CVPR 2018)
- ArcFace(arxiv 2017)

margin을 어디에 주느냐에 따라 sphere, arc, cos가 나뉘게 됨. 성능은 큰 차이가 없다.


# 최근 얼굴 인식 연구 방향
## Recent Databases
### MSCeleb-1M(ECCV 2016)
- 10만명
- 약 1000만장
- 학습하는데도 오래 걸림.

### VGGFace2(FG 2018)
- 9131명
- 331만장
- 테스트해보니 성능 99%! 우리 서비스에 이용할 수 있을까? 대부분은 서양인이다. 동양인으로 실험해봤더니 90%의 Accuracy. 반은 틀리고 반은 맞추고. 표정이 다르거나 하면 틀림.

# 카카오의 얼굴 인식
- kakao dataset을 만듬.
  (사람수는 현재 1천명 정도. 계속 데이터를 쌓고 있다. 모드고 GT 찍고, 성능 높이고 ...)
- Evaluation: LFW protocol 사용
- Softmax: 92%! , Softmax + Center loss: 99%!

# 참고할 사이트
1. [Training using the VGGFace2 dataset](https://github.com/davidsandberg/facenet/wiki/Training-using-the-VGGFace2-dataset)
- tensorflow
- VGGFace2 학습
- LFW: 99.6%

2. [Insightface](https://github.com/deepinsight/insightface)
- mxnet
- Angular classification
- MSCeleb-1M 학습
- LFW: 99.8%
