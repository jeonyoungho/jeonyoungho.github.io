---
title: "[React] dynamic import" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2022-04-27 +0800
categories: [React] # categories는 최대 2개까지 가능
tags: [react] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

하나의 애플리케이션의 번들 사이즈가 엄청 커지게 되면 초기에 웹 페이지 로딩 속도가 엄청 느려질 것이다.
당연히 실무에서는 애플리케이션의 사이즈가 점점 커져갈 수 밖에 없고 당연히 부딪히게 될 수 밖에 없는 문제가 될 것이다.

운 좋게도 팀원을 통해 코드 분할(code spliting)에 대해 먼저 접하게 되었는데, 맨 처음 들었을땐 이해가 부족하여 좀 더 학습하게 되었다.

[벨로그에 잘 정리된 포스팅](https://velog.io/@code-bebop/dynamic-import%EC%99%80-React.lazy)이 있어 이 내용을 바탕으로 정리해보려 한다.
그리고 추후 프로젝트에 적용해보려고 한다.

# Code Spliting
`Code Spliting`은 코드 분할이다. 어떤 코드를 분할하느냐? 바로 번들 코드를 분할하는 것이다.

일반적으로 React 같은 SPA 애플리케이션을 개발하고 나면 webpack 같은 번들러로 번들링 과정을 거치게 된다. 그리고 html 파일에서 번들된 js 파일을 불러와서 웹앱을 브라우저에서 실행한다.

그런데 번들 파일이 다 불러와져야 웹앱이 실행되다 보니, 웹앱의 크기가 커지면 커질수록 성능에 안좋은 영향을 미치게된다. 즉, 초기 웹앱 구동시의 속도가 느려지게 되는 것이다. 서드 파티 라이브러리 같은 경우 그 크기가 굉장히 큰 경우가 많기에 번들 파일의 크기도 금방금방 커져버리게 된다.

그런 때에 해결책이 바로 코드 분할이다. 이는 <b>번들 파일의 코드를 분할하여, 모든 코드를 한 번에 불러오지 않고 사용자가 필요로 할 때에 필요한 코드만 불러오는 개념이다.</b>

우선 코드 분할의 기초 개념인 `dynamic import` 에 대해 먼저 알아보자.

# dynamic import
<b>dynamic import는 동적 불러오기이다.</b>

기존에 코드 파일의 가장 상위에서 import 구문을 사용하여 불러오는 것은 `static import(정적 불러오기)` 라고 한다.

정적 불러오기는 문서의 가장 상위에 위치해야 하고(바닐라js에선 맨 밑에 위치해도 되지만, react-app에선 컴파일 에러가 발생), 블록문 안에 위치할 수 없는 등의 제약 사항이 있다.

`[JAVASCRIPT.INFO의 동적으로 모듈 가져오기 문서](https://ko.javascript.info/modules-dynamic-imports)` 에서는 정적 불러오기에 이런 제약사항이 발생한 이유에 대해 아래와 같이 설명하고 있다.

> 이런 제약사항이 만들어진 이유는 import/export 는 코드 구조의 중심을 잡아주는 역할을 하기 때문입니다. 코드 구조를 분석해 모듈을 한데 모아 번들링하고, 사용하지 않는 모듈은 제거(가지치기)해야하는데, 코드 구조가 간단하고 고정되어 있을때만 이런 작업이 가능합니다.

이런 장점들을 내려놓고서라도 동적 불러오기를 사용해야하는 이유가 바로 코드 분할이다.

## 사용법

동적 불러오기는 다음과 같이 사용한다.

```javascript
import('./sum').then(sum => {
  console.log(sum(1 + 2));
});
```

동적 불러오기는 `import()` 구문을 사용하는데, <b>프로미스 객체를 반환</b>한다. 프로미스 객체의 반환 값은 불러온 모듈이다, 함수를 호출하는 문법을 취하고 있으나, import는 함수가 아니다.

동적 불러오기는 코드의 위치에 관계없이 사용이 가능하기 때문에, 모듈들을 사용자가 필요로 할 때에 불러오게끔 할 수 있다.

## React.lazy
리액트에선 컴포넌트 파일을 정의하고 동적 불러오기를 사용하면 에러가 발생한다.
<b>컴포넌트를 동적으로 불러오기 위해선 React.lazy를 사용해야 한다.</b>

## 사용법
React.lazy를 사용한 예시 코드이다.

```javascript
import { Suspense } from 'react';

const SomeComponent = React.lazy(() => import('./SomeComponent'));

const MyComponent = () => {
  return (
    <Suspense fallback={<div>로딩 중. . .</div>}>
      <SomeComponent />
    </Suspense>
  );
}
```

`React.lazy()`는 `import()` 구문을 반환하는 콜백함수를 인자로 받는다. <b>동적 불러오기로 불러와지는 모듈은 리액트 컴포넌트를 포함하며 default export를 가진 모듈이어야 한다.</b> 그리고 불러온 컴포넌트를 반환한다.

<b>React.lazy로 불러온 컴포넌트는 단독으로 쓰일 수 없고, React.Suspense 컴포넌트로 하위에서 렌더링되어야 한다.</b>

Suspense 컴포넌트는 fallback을 prop을 필수로 가진다. fallback prop 은 로딩 표시기로 사용할 컴포넌트를 받는다.

> **Note**: Suspense 컴포넌트와 관련해선 [리액트 공식 문서](https://ko.reactjs.org/docs/concurrent-mode-suspense.html)를 참고하자.

## with React Router
그렇다면 React.lazy를 어디에 적용하는 것이 좋을까?

[React 공식문서의 코드 분할 항목](https://ko.reactjs.org/docs/code-splitting.html)에 의하면, Router 바로 아래에 Suspense를 위치시키고, Route로 보여줄 컴포넌트들을 React.lazy로 불러올 것을 권장하고 있다.
아래가 그 예시 코드이다.

```javascript
import React, { Suspense, lazy } from 'react';
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';

const Home = lazy(() => import('./routes/Home'));
const About = lazy(() => import('./routes/About'));

const App = () => (
  <Router>
    <Suspense fallback={<div>Loading...</div>}>
      <Switch>
        <Route exact path="/" component={Home}/>
        <Route path="/about" component={About}/>
      </Switch>
    </Suspense>
  </Router>
);
```

> 이를 시작하기 좋은 장소는 라우트입니다. 웹 페이지를 불러오는 시간은 페이지 전환에 어느 정도 발생하며 대부분 페이지를 한 번에 렌더링하기 때문에 사용자가 페이지를 렌더링하는 동안 다른 요소와 상호작용하지 않습니다.

# SSR에서의 코드 분할

## React.lazy는 SSR에서 작동하지 않는다.
SSR 웹 앱에서 코드 분할을 하고 싶다면 `@loadable/component` 라이브러리를 사용해야한다. <b>이는 React 공식문서에서도 권장하는 바이다.</b>

> React.lazy와 Suspense는 아직 서버 사이드 렌더링을 할 수 없습니다. 서버에서 렌더링 된 앱에서 코드 분할을 하기 원한다면 Loadable Components를 추천합니다. 이는 서버 사이드 렌더링과 번들 스플리팅에 대한 좋은 가이드입니다.

loadable component에 대해 자세히 알고 싶다면 [loadable component 공식문서](https://loadable-components.com/)를 참고하자.

# webpack에서의 코드 분할 설정
dynamic import에 대해 맨 처음 접했을때 들었던 생각이 웹팩에서 따로 이에 대해 설정이 필요할 것 같다는 생각이 들었다. 결과는 예상대로 일치하였다.

webpack은 코드 분할을 적극적으로 지원하고 있으며, 코드 분할 설정 또한 상세히 할 수 있다.

## entry에 의한 코드 분할
`webpack.config`의 property 중에는 entry 객체가 있다. webpack은 번들링 할때에 entry 에 명시된 시작점을 기반으로 의존성이 있는 모든 모듈들을 함께 번들링한다.

그렇기 때문에 어떤 React 웹앱을 개발한 뒤 최상위 컴포넌트인 App.js 파일을 App.bundle.js 파일로 번들링하면, html에서 bundle.js를 불러오기만 하면 React 웹앱이 실행되는 것이다.

이 entry 객체는 여러개의 파일을 value로 가질 수도 있는데, `A.js 파일`과 `B.js 파일`을 entry로 설정하고 번들링하면 A가 번들링 된 파일, B가 번들링 된 파일 두 가지가 나온다. 당연히 시작점이 두 개이기에 번들 결과가 두 개가 생성되는 것이다.

프론트 개발자들 사이에서 유명한 [캡틴 판교님의 웹팩 핸드북](https://joshua1988.github.io/webpack-guide/concepts/entry.html#entry-%ED%8C%8C%EC%9D%BC%EC%97%90%EB%8A%94-%EC%96%B4%EB%96%A4-%EB%82%B4%EC%9A%A9%EC%9D%B4-%EB%93%A4%EC%96%B4%EA%B0%80%EC%95%BC-%ED%95%98%EB%82%98) 에서도 아래와 같이 설명하고 있다.

> 위와 같이 엔트리 포인트를 분리하는 경우는 싱글 페이지 애플리케이션이 아닌 특정 페이지로 진입했을 때 서버에서 해당 정보를 내려주는 형태의 멀티 페이지 애플리케이션에 적합합니다.

## SplitChunkPlugin
SplitChunkPlugin은 webpack에서 지원하는 플러그인 중 하나이다.
이 플러그인을 사용하는 주 목적은 `chunk 코드의 중복된 의존성 제거`이다.

예를 들어, A.js 파일과 B.js가 있다고 가정하자.

각각의 파일은 API를 요청하는 비즈니스 로직이 담겨 있어서, 둘 다 axios라이브러리를 불러와서 사용하는데, 동적 불러오기 구문을 사용해서 axios를 불러온다.

```javascript
// A.js
import(axios)

... // API를 요청하는 로직

```

```javascript
// B.js
import(axios)

... // API를 요청하는 또 다른 로직
```

그럼 A.chunk.js 와 B.chunk.js가 만들어지고, 각 청크에는 axios를 불러오는 코드가 담겨있을 것이다.

어짜피 하나의 html 파일에서 사용될 것인데, 중복된 라이브러리 코드를 여러개 요청하는 것은 로딩 시간만 늘릴 뿐이다.

이런 중복된 청크 코드를 또 다른 청크 코드로 추출하는 일을 하는 것이 바로 `SplitChunkPlugin`이다.

[웹팩 공식문서의 SplitChunkPlugin 항목](https://webpack.js.org/plugins/split-chunks-plugin/)에 의한 사용법은 아래와 같다.

```javascript
// webpack.config

module.exports = {
  //...
  optimization: {
    splitChunks: {
      // include all types of chunks
      chunks: 'all', // 'initial' | 'async' | 'all' 
    },
  },
};
```

chunks 옵션은 `'initial' | 'async' | 'all'` 중 하나를 값으로 가진다.

- initial: 정적 불러오기에만 splitChunks를 적용한다.
- async: 동적 불러오기에만 splitChunks를 적용한다.
- all: 모든 불러오기에 splitChunks를 적용한다.

이외에도 정말 다양한 옵션이 있지만, 그것은 공식문서에서 확인하면 된다.

<b>CRA로 만든 웹앱에는 splitChunks가 기본적으로 적용되어 있다.</b>

#### 출처
- [https://velog.io/@code-bebop/dynamic-import%EC%99%80-React.lazy](https://velog.io/@code-bebop/dynamic-import%EC%99%80-React.lazy)
- [https://ko.reactjs.org/docs/code-splitting.html](https://ko.reactjs.org/docs/code-splitting.html)
- [https://pks2974.medium.com/dynamic-import-%EB%A1%9C%EC%9B%B9%ED%8E%98%EC%9D%B4%EC%A7%80-%EC%84%B1%EB%8A%A5-%EC%98%AC%EB%A6%AC%EA%B8%B0-caf62cc8c375](https://pks2974.medium.com/dynamic-import-%EB%A1%9C%EC%9B%B9%ED%8E%98%EC%9D%B4%EC%A7%80-%EC%84%B1%EB%8A%A5-%EC%98%AC%EB%A6%AC%EA%B8%B0-caf62cc8c375)