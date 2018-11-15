---
layout: post
title:  "Javascript 배틀그라운드로부터 살아남기"
author: Aria
category: "Deview 2018"
comments: true
---
[발표자료](https://www.slideshare.net/deview/123javascript)

## The birth
- 1995년 Netscape에서 개발
- 당시 웹은 정적, DOM 인터렉션을 통해 다이나믹한 웹을 만들고자 하는 비전.

## 새롭게 개발된 이유
- 기존 언어를 채택할 수도 있었지만, java스럽게 보여야 함과 java를 돋보이게 하는 마케팅적 측면 압력
- Mocha -> Livescript -> javascript

## 파생버전
- JScript: MS의 JS 구현 엔진, java와의 상표권 문제 충돌을 회피하기 위해 개발
- ActionScript: Flash에서 사용, ES4 기반.

## Server-side Javascript
- node, npm, ...

## 프레임워크의 등장
- jQuery
- React, Angular, Vue.js

## Browser wars
- Evergreen Browser
```
The term "evergreen browser" refers to browsers that are automatically upgraded to future versions, rather than being updated by distribution of new versions from the manufacturer, as was the case with older browsers
```

## Vanilla is not common
- Vanilla 개발은 일반적이지 않음
- Framework + toolchains(transpiler, bundling)
- 대다수 기능들은 라이브러리 '조립'

## 개발 방식의 진화
### Library -> Framework
- 직접적인 DOM 핸들링 필요성 감소
- DOM API native 지원향상(CSS selector 엔진 불필요)
- 컴포넌트 단위의 기능에 집중, DOM은 프레임워크에 맡김
ㄴ Virtual DOM을 통해 제어

### Transpiling Era
- From non/pure javascript code compiles/generates to javascript
- Coffeescript, Dart, Typescript, ...

#### Babel
- 새로운 syntax를 즉시 사용할 수 있게 됨.
- ES6+ 코드를 이전 브라우저 호환 코드로 컴파일
지금도 IE에서는 error function을 사용할 수 없지만, babel의 도움으로 가능

## 2018년 동향
### 언어적 관점
#### 버전 구분 중요성 감소
- Annual release로 인해 해마다 릴리즈.
  - 새로운 명세의 릴리즈까지 너무 오래 소요.
  - 단일 릴리즈 모델의 한계
- Transpiler(Babel)는 최신 스펙 사용 이슈 해결
- 과거와 같이 버저닝에 큰 변화가 아닌 feature 단위
- Evergreen browser는 스펙을 빠르게 구현

#### What's coming?
- ES2017/ES8
  - async/await
  - SHARED

### WEB ASSEMBLY
- 프로그래밍 언어를 컴파일 해, 어느 브라우저에서나 빠르게 실행되는 바이너리 파일로 변환
- 주요 브라우저 및 node.js 모두 지원

### 정적 타입 시스템
- js의 부족한 영역(loose typing)을 채워줌
- 버그 감소
- as Languages: Typescript, ReasonML, PureScript
- as tools: Flow

### Web components
#### Polymer
- [lit-html](https://github.com/Polymer/lit-html): 커스텀 엘리먼트 생성하는 기본 클래스에서 활용되는 라이브러리. DOM 렌더링 집중 like jsx -> 표준 기술이므로 빌드 과정 필요없음.

### NPM
- 'Do not use Bower' policy.

### Yarn
- yarn을 통한 패키지 다운로드는 전체 npm 다운로드의 0.03%.
- 초기 yarn의 장점(npm보다 빠르다)은 점점 퇴색.

### Bundler/Build
#### Webpack v4
- 향상된 빌드 시간: 60~98% 감소
- mode property -> development | production | none
- split cli: webpack-cli

### OCJS(Zero Configuration JS)
- webpack은 configpack 같다. Configuration headache.
- [PARCEL](https://parceljs.org/)의 등장(무설정 번들링 지향)

### 모바일 애플리케이션
- webview를 래핑하는 hybrid-app은 죽어감.

### React Native
- React 인기에 더불어 인기 상승.
- TV 플랫폼 지원.

### 데스크톱 어플리케이션
- 모두 Chromium 기반
- Electron: 세밀한 제어 기능 제공.
- [NW.js](https://nwjs.io/): 웹앱을 빠르게 데스크톱앱으로 전환시 장점.

## PWA(Progressive Web Applications)
- Safari와 Edge 지원 추가로 기본 사용 환경을 갖춤.
- 다양한 도구의 등장: Workbox, HNPWA, PWA Starter Kit, pwa-helpers

## Javascript는 어디로 가고 있나?
### 흐릿해지는 경계
- FE/BE 구분 없이 하나의 코드셋으로 모든 곳에서 실행.
- Universal Javascript.
- Node.js + JS Foundation을 merge하려는 움직임의 시작(2018.10.4)

## Javascript로부터 생존하기
1) Trends
- Github Trending: 새로운/떠오르는 프로젝트 확인
- FE Tech Mailings: 최신 소식 업데이트

2) Browsers Update: Chrome / WebKit / MS Edge / Firefox

3) 다른 사람들은 어떻게 생각하나?
- State of JS
- Stack overflow annual developer survey
- Medium's js posts.

4) Conferences
- JSConf
- Fluent
- dotJS
- JS Kongress
- Netflix JavaScript Talks
