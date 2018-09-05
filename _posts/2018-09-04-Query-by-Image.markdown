---
layout: post
title:  "[if kakao] Query by Image"
date:   2018-09-04 12:00:00 +0900
author: Aria
category: if kakao
---

## 텍스트로 검색을 하면 안되나?
- 사진을 잘 표현하는 단어들을 뽑아내서 색인해 볼 수 있다.
- 하지만 문제가 있다.
  - 해당 사진을 설명하는 문장이 필요하다.
  - 사람들이 올리는 글에서는 사진과 글이 매칭된다고 장담할 수 없다! (주목받기 위해 사용하는 노이즈성 단어들)

## 이미지로 검색을 해보자
Undirected graph로 표현, user의 input을 anchor로 취급하여, 관련 있는 노드는 positive, 관련 없는 노드는 negative로 샘플링한다.
![anchor, positive,  negative](https://cdn-images-1.medium.com/max/800/1*MGb0lv45RwKmBYc87tyNzQ.jpeg)
**Distance(anchor, positive) < Distance(anchor, negative) 가 될 수 있도록 학습한다.**

이미지 자체가 크기 때문에, 어떤 함수를 통해 이미지를 줄여보자.

**➔ Distance(F(anchor), F(positive)) < Distance(F(anchor), F(negative))**

학습을 하는 데이터는 어느정도 정제되어 있다. 보지 않은 데이터도 인풋으로 들어올 것으로 예상하고 buffer를 m(margin) 을 둔다.

**➔ Distance(F(anchor), F(positive)) + m < Distance(F(anchor), F(negative)) + m**

### Triplet Loss
이미지 ➔ CNN ➔ Similarity(Triplet Loss)

유사스타일에 대한 평가 방법
서비스 자체에 대한 평가: 로그 파싱. 클릭 수를 measure로 사용 함.
