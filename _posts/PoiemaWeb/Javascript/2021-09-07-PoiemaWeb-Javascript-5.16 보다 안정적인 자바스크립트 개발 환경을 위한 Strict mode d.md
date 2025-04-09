---
title: "[개발자 블로그] 5.16 보다 안정적인 자바스크립트 개발 환경을 위한 Strict mode(PoiemaWeb-Javascript)"
date: 2021-09-07 +0800
categories: [Frontend, PoiemaWeb, Javascript]
tags: [poiemaweb, javascript, es6]
toc: true
comments: true
---

# [1. strict mode란?](https://poiemaweb.com/js-strict-mode#1-strict-mode%EB%9E%80)
~~~
function foo() {
  x = 10;
}

console.log(x); // ?
~~~

foo 함수 내에서 선언하지 않은 변수 x에 값 10을 할당하였다. 이때 변수 x를 찾아야 x에 값을 할당할 수 있기 때문에 자바스크립트 엔진은 변수 x가 어디에서 선언되었는지 스코프 체인을 통해 검색하기 시작한다.

자바스크립트 엔진은 먼저 foo 함수의 스코프에서 변수 x의 선언을 검색한다. foo 함수의 스코프에는 변수 x의 선언이 없으므로 검색에 실패할 것이고, 자바스크립트 엔진은 변수 x를 검색하기 위해 foo 함수 컨텍스트의 상위 스코프(위 예제의 경우, 전역 스코프)에서 변수 x의 선언을 검색한다.

전역 스코프에도 변수 x의 선언이 존재하지 않기 때문에 ReferenceError를 throw할 것 같지만 자바스크립트 엔진은 암묵적으로 전역 객체에 프로퍼티 x를 동적 생성한다. 결국 식별자 x는 전역 변수가 된다. 이렇게 전역 변수가 된 변수를 `암묵적 전역 변수(implicit global)`라 한다.

개발자의 의도와는 상관없이 자바스크립트 엔진이 생성한 암묵적 전역 변수는 오류를 발생시키는 원인이 될 가능성이 크다. 따라서 반드시 var, let, const 키워드를 사용하여 변수를 선언한 다음 변수를 사용해야 한다.

하지만, 오타나 문법 지식의 미비로 인한 실수는 언제나 발생하는 것이다. 따라서 오류를 줄여 안정적인 코드를 생산하기 위해서는 보다 근본적인 접근이 필요하다. 다시 말해, 잠재적인 오류를 발생시키기 어려운 개발 환경을 만들고 그 환경에서 개발을 하는 것이 보다 근본적인 해결책이라고 할 수 있다.

이를 지원하기 위해 ES5부터 `strict mode`가 추가되었다. strict mode는 자바스크립트 언어의 문법을 보다 엄격히 적용하여 기존에는 무시되던 오류를 발생시킬 가능성이 높거나 자바스크립트 엔진의 최적화 작업에 문제를 일으킬 수 있는 코드에 대해 명시적인 에러를 발생시킨다.

[ESLint](https://poiemaweb.com/eslint)와 같은 린트 도구를 사용하여도 strict mode와 유사한 효과를 얻을 수 있다. 린트 도구는 정적 분석(static analysis) 기능을 통해 소스 코드를 실행하기 전에 소스 코드를 스캔하여 문법적 오류만이 아니라 잠재적 오류까지 찾아내고 오류의 이유를 리포팅해주는 유용한 도구이다.

![image](https://user-images.githubusercontent.com/44339530/132313268-f6c4ce20-aec7-494a-b304-dea7a1894bd7.png)

또한 strict mode가 제한하는 오류는 물론 코딩 컨벤션을 설정 파일 형태로 정의하고 강제할 수 있기 때문에 보다 강력한 효과를 얻을 수 있다. 필자는 개인적으로 strict mode보다 린트 도구의 사용을 선호한다.

> **Note**: ESLint의 설치 및 비주얼 스튜디오 코드에서 ESLint 사용에 대해서는 [이곳](https://poiemaweb.com/eslint)을 참고하기 바란다.

# [2. strict mode의 적용](https://poiemaweb.com/js-strict-mode#2-strict-mode%EC%9D%98-%EC%A0%81%EC%9A%A9)
strict mode를 적용하려면 전역의 선두 또는 함수 몸체의 선두에 'use strict';를 추가한다.
전역의 선두에 추가하면 스크립트 전체에 strict mode가 적용된다.

~~~
// 전역에 strict mode의 적용하는 것은 바람직하지 않다!
'use strict';

function foo() {
  x = 10; // ReferenceError: x is not defined
}
foo();
~~~

<b>함수 몸체의 선두에 추가하면 해당 함수와 중첩된 내부 함수에 strict mode가 적용된다.</b>

~~~
// 함수 단위로 strict mode 적용
function foo() {
  'use strict';

  x = 10; // ReferenceError: x is not defined
}
foo();
~~~

코드의 선두에 strict mode를 위치시키지 않으면 제대로 동작하지 않는다.

~~~
function foo() {
  x = 10; // 에러를 발생시키지 않는다.
  'use strict';
}
foo();
~~~

# [3. 전역에 strict mode를 적용하는 것은 피하자.](https://poiemaweb.com/js-strict-mode#3-%EC%A0%84%EC%97%AD%EC%97%90-strict-mode%EB%A5%BC-%EC%A0%81%EC%9A%A9%ED%95%98%EB%8A%94-%EA%B2%83%EC%9D%80-%ED%94%BC%ED%95%98%EC%9E%90)
전역에 적용한 strict mode는 스크립트 단위로 적용된다.

~~~
<!DOCTYPE html>
<html>
<body>
  <script>
    'use strict';
  </script>
  <script>
    x = 1; // 에러가 발생하지 않는다.
    console.log(x); // 1
  </script>
  <script>
    'use strict';

    y = 1; // ReferenceError: y is not defined
    console.log(y);
  </script>
</body>
</html>
~~~

위 예제와 같이 스크립트 단위로 적용된 strict mode는 다른 스크립트에 영향을 주지 않고 자신의 스크립트에 한정되어 적용된다.

하지만 strict mode 스크립트와 non-strict mode 스크립트를 혼용하는 것은 오류를 발생시킬 수 있다. <b>특히 외부 서드 파티 라이브러리를 사용하는 경우, 라이브러리가 non-strict mode일 경우도 있기 때문에 전역에 strict mode를 적용하는 것은 바람직하지 않다.</b> 이러한 경우, 즉시 실행 함수로 스크립트 전체를 감싸서 스코프를 구분하고 즉시 실행 함수의 선두에 strict mode를 적용한다.

~~~
// 즉시실행 함수에 strict mode 적용
(function () {
  'use strict';

  // Do something...
}());
~~~

# [4. 함수 단위로 strict mode를 적용하는 것도 피하자.](https://poiemaweb.com/js-strict-mode#4-%ED%95%A8%EC%88%98-%EB%8B%A8%EC%9C%84%EB%A1%9C-strict-mode%EB%A5%BC-%EC%A0%81%EC%9A%A9%ED%95%98%EB%8A%94-%EA%B2%83%EB%8F%84-%ED%94%BC%ED%95%98%EC%9E%90)
앞서 말한 바와 같이 함수 단위로 strict mode를 적용할 수도 있다. 그러나 어떤 함수는 strict mode를 적용하고 어떤 함수는 strict mode를 적용하지 않는 것은 바람직하지 않으며 모든 함수에 일일이 strict mode를 적용하는 것은 번거로운 일이다. 그리고 strict mode가 적용된 함수가 참조할 함수 외부의 컨텍스트에 strict mode를 적용하지 않는다면 이 또한 문제가 발생할 수 있다.

~~~
(function () {
  // non-strict mode
  var lеt = 10; // 에러가 발생하지 않는다.

  function foo() {
    'use strict';

    let = 20; // SyntaxError: Unexpected strict mode reserved word
  }
  foo();
}());
~~~

따라서 strict mode는 즉시실행함수로 감싼 스크립트 단위로 적용하는 것이 바람직하다.

# 5. strict mode가 발생시키는 에러

## [5.1 암묵적 전역 변수](https://poiemaweb.com/js-strict-mode#51-%EC%95%94%EB%AC%B5%EC%A0%81-%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98)
선언하지 않은 변수를 참조하면 ReferenceError가 발생한다.

~~~
(function () {
  'use strict';

  x = 1;
  console.log(x); // ReferenceError: x is not defined
}());
~~~

## [5.2 변수, 함수, 매개변수의 삭제](https://poiemaweb.com/js-strict-mode#52-%EB%B3%80%EC%88%98-%ED%95%A8%EC%88%98-%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98%EC%9D%98-%EC%82%AD%EC%A0%9C)

~~~
(function () {
  'use strict';

  var x = 1;
  delete x;
  // SyntaxError: Delete of an unqualified identifier in strict mode.

  function foo(a) {
    delete a;
    // SyntaxError: Delete of an unqualified identifier in strict mode.
  }
  delete foo;
  // SyntaxError: Delete of an unqualified identifier in strict mode.
}());
~~~

## [5.3 매개변수 이름의 중복](https://poiemaweb.com/js-strict-mode#53-%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-%EC%9D%B4%EB%A6%84%EC%9D%98-%EC%A4%91%EB%B3%B5)
중복된 함수 파라미터 이름을 사용하면 SyntaxError가 발생한다.

~~~
(function () {
  'use strict';

  //SyntaxError: Duplicate parameter name not allowed in this context
  function foo(x, x) {
    return x + x;
  }
  console.log(foo(1, 2));
}());
~~~

## [5.3 with 문의 사용](https://poiemaweb.com/js-strict-mode#53-with-%EB%AC%B8%EC%9D%98-%EC%82%AC%EC%9A%A9)
with 문을 사용하면 SyntaxError가 발생한다.

~~~
(function () {
  'use strict';

  // SyntaxError: Strict mode code may not include a with statement
  with({ x: 1 }) {
    console.log(x);
  }
}());
~~~

## [5.4 일반 함수의 this](https://poiemaweb.com/js-strict-mode#54-%EC%9D%BC%EB%B0%98-%ED%95%A8%EC%88%98%EC%9D%98-this)
strict mode 에서 함수를 일반 함수로서 호출하면 this에 undefined가 바인딩된다. 생성자 함수가 아닌 일반 함수 내부에서는 this를 사용할 필요가 없기 때문이다. 이때 에러는 발생하지 않는다.

~~~
(function () {
  'use strict';

  function foo() {
    console.log(this); // undefined
  }
  foo();

  function Foo() {
    console.log(this); // Foo
  }
  new Foo();
}());
~~~

## strict mode가 발생시키는 에러 정리
1) 암묵적 전역 변수를 사용하는 경우(선언하지 않은 변수를 참조하는 경우) -> ReferenceError 발생

2) 변수, 함수, 매개변수의 삭제하는 경우

3) 중복된 함수 파라미터 이름을 사용하는 경우 -> SyntaxError 발생

4) with 문을 사용하는 경우 -> SyntaxError 발생

5) 일반 함수에서 this를 사용하는 경우 -> 에러 발생 안함
- 함수를 일반 함수로서 호출하면 this에 undefined가 바인딩된다. 생성자 함수가 아닌 일반 함수 내부에서는 this를 사용할 필요가 없기 때문이다. 이때 에러는 발생하지 않는다.

# [6. 브라우저 호환성](https://poiemaweb.com/js-strict-mode#6-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80-%ED%98%B8%ED%99%98%EC%84%B1)
IE 9 이하는 지원하지 않는다.

#### 출처
- [https://poiemaweb.com/js-strict-mode](https://poiemaweb.com/js-strict-mode)