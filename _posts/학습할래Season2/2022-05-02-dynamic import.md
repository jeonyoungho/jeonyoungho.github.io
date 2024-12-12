---
title: "[학습할래] Dynamic Import"
date: 2022-05-02 +0800
categories: [학습할래]
tags: [mobx]
toc: true
comments: true
---

일반적으로 브라우저에 접속하면 해당 브라우저에서 필요한 html 파일을 네트워크를 통해 가져오고, 해당 html 파일에 명시된 자바스크립트 파일, 스타일 시트 파일들을 함께 가져오게 됩니다.

<b>여기서!!!</b>

하나의 애플리케이션의 번들(javascript 파일) 사이즈가 엄청 커지게 되면 초기에 웹 페이지 로딩 속도가 엄청 느려지게 될 것입니다.  

당연히 실무에서는 애플리케이션의 사이즈가 점점 커져갈 수 밖에 없고 당연히 부딪히게 될 수 밖에 없는 문제가 될 것입니다.

운 좋게도 저는 코드 분할(code spliting)에 대해 먼저 접하게 되었는데, 맨 처음 들었을땐 이해가 부족하여 좀 더 추가적인 학습을 진행하게 되었습니다.

[벨로그에 잘 정리된 포스팅](https://velog.io/@code-bebop/dynamic-import%EC%99%80-React.lazy)이 있어서 이 내용을 바탕으로 정리해보려 합니다. 


## Code Spliting
`Code Spliting`은 코드 분할이라고도 표현합니다. **어떤 코드를 분할하느냐?** 바로 번들 코드를 분할하는 것입니다.

일반적으로 React 같은 SPA 애플리케이션을 개발하고 나면 webpack 같은 번들러로 번들링 과정을 거치게 됩니다. 그리고 html 파일에서 번들된 js 파일을 불러와서 웹앱을 브라우저에서 실행합니다.

그런데 번들 파일이 다 불러와져야 웹앱이 실행되다 보니, 웹앱의 크기가 커지면 커질수록 성능에 안 좋은 영향을 미치게 됩니다. 즉, 초기 웹앱 구동 시의 속도가 느려지게 되는 것입니;다. 서드 파티 라이브러리 같은 경우 그 크기가 굉장히 큰 경우가 많기에 번들 파일의 크기도 금방금방 커져버리게 됩니다.

그럴 때에 해결책이 바로 코드 분할입니다. 이는 **번들 파일의 코드를 분할하여, 모든 코드를 한 번에 불러오지 않고 사용자가 필요로 할 때에 필요한 코드만 불러오는 개념입니다.**

## dynamic import
우선 코드 분할의 기초 개념인 `dynamic import` 는 동적 불러오기라고도 많이 표현됩니다. 

기존에 코드 파일의 가장 상위에서 import 구문을 사용하여 불러오는 것은 `static import(정적 불러오기)` 라고 합니다.

정적 불러오기는 문서의 가장 상위에 위치해야 하고(바닐라js에선 맨 밑에 위치해도 되지만, react-app에선 컴파일 에러가 발생), 블록문 안에 위치할 수 없는 등의 제약 사항이 있습니다.

사용법은 다음과 같습니다.

```javascript
import('./sum').then(sum => {
  console.log(sum(1 + 2));
});

```

동적 불러오기는 `import()` 구문을 사용하는데, **프로미스 객체를 반환**합니다. 프로미스 객체의 반환 값은 불러온 모듈이게 됩니다.

동적 불러오기는 코드의 위치에 관계없이 사용이 가능하기 때문에, 모듈들을 사용자가 필요로 할 때에 불러오게끔 할 수 있습니다.

## React.lazy
리액트에선 컴포넌트 파일을 정의하고 동적 불러오기를 사용하면 에러가 발생합니다. **컴포넌트를 동적으로 불러오기 위해선 React.lazy를 사용해야 합니다.**

React.lazy를 사용한 예시 코드입니다.

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

`React.lazy()`는 `import()` 구문을 반환하는 콜백함수를 인자로 받습는다. **동적 불러오기로 불러와지는 모듈은 리액트 컴포넌트를 포함하며 default export를 가진 모듈이어야 합니다.** 결과적으로 불러온 컴포넌트를 반환합니다.

**React.lazy로 불러온 컴포넌트는 단독으로 쓰일 수 없고, React.Suspense 컴포넌트로 하위에서 렌더링되어야 합니다.**


> 리액트에서 제공하는 Suspense 컴포넌트와 관련해서는 [해당 공식 문서](https://17.reactjs.org/docs/concurrent-mode-suspense.html)를 참고 부탁드립니다.

## webpack에서의 코드 분할 설정
dynamic import에 대해 맨 처음 접했을때 들었던 생각이 웹팩에서 따로 이에 대해 설정이 필요할 것 같다는 생각이 들었고 결과는 예상대로 일치하였습니다!

webpack은 코드 분할을 적극적으로 지원하고 있으며, 코드 분할 설정 또한 상세히 할 수 있습니다.

## entry에 의한 코드 분할
`webpack.config`의 property 중에는 entry 객체가 있습니다. webpack은 번들링 할때에 entry 에 명시된 시작점을 기반으로 의존성이 있는 모든 모듈들을 함께 번들링합니다.

그렇기 때문에 어떤 React 웹앱을 개발한 뒤 최상위 컴포넌트인 App.js 파일을 App.bundle.js 파일로 번들링하면, html에서 bundle.js를 불러오기만 하면 React 웹앱이 실행되는 것입니다.

이 entry 객체는 여러 개의 파일을 value로 가질 수도 있는데, `A.js 파일`과`B.js 파일`을 entry로 설정하고 번들링하면 A가 번들링 된 파일, B가 번들링 된 파일 두 가지가 나옵니다. 당연히 시작점이 두 개이기에 번들 결과가 두 개가 생성되는 것입니다.

프론트 개발자분들 사이에서 유명한 [캡틴 판교님의 웹팩 핸드북](https://joshua1988.github.io/webpack-guide/concepts/entry.html#entry-%ED%8C%8C%EC%9D%BC%EC%97%90%EB%8A%94-%EC%96%B4%EB%96%A4-%EB%82%B4%EC%9A%A9%EC%9D%B4-%EB%93%A4%EC%96%B4%EA%B0%80%EC%95%BC-%ED%95%98%EB%82%98) 에서도 아래와 같이 설명하고 있습니다.

> 위와 같이 엔트리 포인트를 분리하는 경우는 싱글 페이지 애플리케이션이 아닌 특정 페이지로 진입했을 때 서버에서 해당 정보를 내려주는 형태의 멀티 페이지 애플리케이션에 적합합니다.

하지만 INHR+ 프론트엔드는 다른 이유로 하나 이상의 entry 포인트를 가집니다!

간략히 설명 드리면, 

```javascript
<script A>
<script B>
```

이런 식으로 여러 개의 script 파일을 추가하게 되어, A 스크립트에선 environment와 auth token 정보를 초기화하는 자바 스크립트 코드가 들어가게되고, B 스크립트에선 실제 번들 코드가 들어가게 되는 구조로 되어 있습니다. (제가 알고 있는 내용은 그러한데 혹여나 아니라면 피드백 부탁드립니다…!!)


## SplitChunkPlugin

SplitChunkPlugin은 webpack에서 지원하는 플러그인 중 하나 입니다. 이 플러그인을 사용하는 **주 목적은 chunk 코드의 중복된 의존성 제거입니다.**

예를 들어, A.js 파일과 B.js가 있다고 가정 했을때.

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

그럼 A.chunk.js 와 B.chunk.js가 만들어지고, 각 청크에는 axios를 불러오는 코드가 담겨있을 것입니다.

어차피 하나의 html 파일에서 사용될 것인데, 중복된 라이브러리 코드를 여러 개 요청하는 것은 로딩 시간만 늘릴 뿐이고, 이런 중복된 청크 코드를 또 다른 청크 코드로 추출하는 일을 하는 것이 바로 `SplitChunkPlugin`입니다.

[웹팩 공식문서의 SplitChunkPlugin 항목에 의한 사용법](https://webpack.js.org/plugins/split-chunks-plugin/)은 아래와 같습니다.

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

chunks 옵션은 `'initial'` | `'async'` | `'all'` 중 하나를 값으로 가집니다.

initial: 정적 불러오기에만 splitChunks를 적용한다.

async: 동적 불러오기에만 splitChunks를 적용한다.

all: 모든 불러오기에 splitChunks를 적용한다.

이외에도 정말 다양한 옵션이 있지만, 그것은 공식문서에서 확인할 수 있습니다.

**(추가적으로 CRA로 만든 웹앱에는 splitChunks가 기본적으로 적용되어 있습니다.)**

## Reference
- [https://velog.io/@code-bebop/dynamic-import%EC%99%80-React.lazy](https://velog.io/@code-bebop/dynamic-import%EC%99%80-React.lazy)
- [https://ko.legacy.reactjs.org/docs/code-splitting.html](https://ko.legacy.reactjs.org/docs/code-splitting.html)
- [https://pks2974.medium.com/dynamic-import-%EB%A1%9C%EC%9B%B9%ED%8E%98%EC%9D%B4%EC%A7%80-%EC%84%B1%EB%8A%A5-%EC%98%AC%EB%A6%AC%EA%B8%B0-caf62cc8c375](https://pks2974.medium.com/dynamic-import-%EB%A1%9C%EC%9B%B9%ED%8E%98%EC%9D%B4%EC%A7%80-%EC%84%B1%EB%8A%A5-%EC%98%AC%EB%A6%AC%EA%B8%B0-caf62cc8c375)