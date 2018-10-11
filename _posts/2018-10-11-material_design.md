---
layout: post
title:  "Learn how Material Design makes it easier and faster to build apps without compromising quality"
author: Aria
category: "Deview 2018"
---

[발표자료](https://www.slideshare.net/deview/142-learn-how-material-design-makes-it-easier-and-faster-to-build-apps-without-compromising-quality)

# What is material design?
- Material is an adaptable design system - backed by open source code - that helps teams easily build high quality digital experiences.

# Why material design?
- Speed is very important. Time is money.
- Easier, faster to customize
- Organize the world's information and make it universally accessible.

## Components
### FAB(Floating Action button)
- FAB를 사용하는 partners가 많아지고 있다. 조금 더 fancy함
- submit button, add/plus button, ...

![fab]("/img/blog/fab.png")

### Backdrop
- filter, 메타태그 처럼 나타내고 있음.

![backdrop]("/img/blog/backdrop.png")

### Motion
- password keypad에서도 모션이 들어간다.
- loading scene에도 motionable한 이미지를 넣는다.

![motion](https://cdn-images-1.medium.com/max/1200/1*xrH020ybWUPw503tg4IyGQ.gif)

## Tips on Adopting Material Design
1. Audit your product
2. Start with Material Foundations
ㄴ color
ㄴ layout interactions...
3. Choose appropriate Material component and *customize!*
4. Implement, test, and iterate.

## Q&A
Q: Design System이 디자이너를 대체할 것이라고 하는데 어떻게 생각하는지?<br/>
A: IKEA 매뉴얼처럼 starting point를 제공하는 것이지 디자이너의 역할을 대체할 수는 없을 것 같다.

Q: 디자인은 매우 주관적인데, 표준을 만들때, 어떤걸 기준으로 결정하나?<br/>
A: UX expert의 의견. 전에는 상단에 햄버거 메뉴를 놓도록 강조했는데, 데이터/피드백을 받고 나서 bottom navigation도 추가했다.

Q: design guide를 따르니 많은 products들이 비슷해보인다.<br/>
A: 구글 내에서도 이런 얘기가 많이 있었다. 2018년도 5월에 출시한 것은 thinning, emphasis만 들어 있다. Interactive tools, color tools

Q: 앱 내 새로운 component를 삽입할 때 어떤걸 기준으로 추가하나?<br/>
A: 우리가 기존에 가지고 있던 컴포넌트를 정말 재사용할 수 없는건지 충분한 논의 후 결정

Q: partner와 협업할 경우, 예를 들면 lyft, customization을 하다보면 guide line을 해칠 수도 있는데, 어떻게 진행했나?<br/>
A: lyft가 새로운 것을 suggest를 했을 때, variation 정도라 하면 support를 한다.

Q: bootstrap 대신 material을 사용해야 할 이유?<br/>
A: speed와 효율성을 높여야 하는 것은 백번 맞는 말. 하나 둘 컴포넌트를 조합해서 만들어나가는 것보다, basement look이 구축되어 있는 상황에서 customize 하는게 더 빠르게 디벨롭할 수 있다. motion, loading 등.

---
PPT를 보며 느낀 점. 모든 화면을 캡쳐해서 넣지 않고, 강조하고 싶은 부분만 잘라서 gif로 자료를 삽입함. 집중하기 좋았음.
