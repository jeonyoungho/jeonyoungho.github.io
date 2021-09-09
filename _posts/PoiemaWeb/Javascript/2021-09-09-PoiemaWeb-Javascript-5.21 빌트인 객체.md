---
title: "[개발자 블로그] 5.21 빌트인 객체(PoiemaWeb-Javascript)" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-09-09 +0800
categories: [PoiemaWeb, Javascript] # categories는 최대 2개까지 가능
tags: [javascript, ES6] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

자바스크립트의 객체는 아래와 같이 크게 3개의 객체로 분류할 수 있다.

![image](https://user-images.githubusercontent.com/44339530/132601950-6b4667b5-72d4-47b4-bbfb-e0aa914d2c08.png)

# [1. 네이티브 객체](https://poiemaweb.com/js-built-in-object#11-object)
네이티브 객체(Native objects or Built-in objects or Global Objects)는 ECMAScript 명세에 정의된 객체를 말하며 애플리케이션 전역의 공통 기능을 제공한다. 네이티브 객체는 애플리케이션의 환경과 관계없이 언제나 사용할 수 있다.

Object, String, Number, Function, Array, RegExp, Date, Math와 같은 객체 생성에 관계가 있는 함수 객체와 메소드로 구성된다.

네이티브 객체를 <b>Global Objects</b>라고 부르기도 하는데 이것은 전역 객체(Global Object)와 다른 의미로 사용되므로 혼동에 주의하여야 한다.

전역 객체(Global Object)는 모든 객체의 최상위 객체를 의미하며 일반적으로 Browser-side에서는 `window`, Server-side(Node.js)에서는 `global` 객체를 의미한다.

# [1.3 Boolean](https://poiemaweb.com/js-built-in-object#13-boolean)
Boolean 객체와 원시 타입 boolean을 혼동하기 쉽다. Boolean 객체는 true/false를 포함하고 있는 객체이다.

~~~
var x = new Boolean(false);
if (x) { // x는 객체로서 존재한다. 따라서 참으로 간주된다.
  // . . . 이 코드는 실행된다.
}
~~~

# [1.10 Error](https://poiemaweb.com/js-built-in-object#110-error)
Error 생성자는 error 객체를 생성한다. error 객체의 인스턴스는 런타임 에러가 발생하였을 때 throw된다.

~~~
try {
  // foo();
  throw new Error('Whoops!');
} catch (e) {
  console.log(e.name + ': ' + e.message);
}
~~~

Error 이외에 Error에 관련한 객체는 아래와 같다.
- EvalError
- InternalError
- RangeError
- ReferenceError
- SyntaxError
- TypeError
- URIError

# [1.11 Symbol](https://poiemaweb.com/js-built-in-object#111-symbol)
Symbol은 ECMAScript 6(Javascript 2015) 에서 추가된 유일하고 변경 불가능한(immutable) 원시 타입으로 Symbol 객체는 원시 타입 Symbol 값을 생성한다.

# [1.12 원시 타입과 래퍼객체(Wrapper Object)](https://poiemaweb.com/js-built-in-object#112-%EC%9B%90%EC%8B%9C-%ED%83%80%EC%9E%85%EA%B3%BC-%EB%9E%98%ED%8D%BC%EA%B0%9D%EC%B2%B4wrapper-object)
네이티브 객체는 각자의 프로퍼티와 메소드를 가진다. 정적(static) 프로퍼티, 메소드는 해당 인스턴스를 생성하지 않아도 사용할 수 있고 prototype에 속해있는 메소드는 해당 prototype을 상속받은 인스턴스가 있어야만 사용할 수 있다.

<b>그런데 원시 타입 값에 대해 표준 빌트인 객체의 메소드를 호출하면 정상적으로 작동한다.</b>

~~~
var str = 'Hello world!';
var res = str.toUpperCase();
console.log(res); // 'HELLO WORLD!'

var num = 1.5;
console.log(num.toFixed()); // 2
~~~

<b>이는 원시 타입 값에 대해 표준 빌트인 객체의 메소드를 호출할 때, 원시 타입 값은 연관된 객체(Wrapper 객체)로 일시 변환 되기 때문에 가능한 것이다. 그리고 메소드 호출이 종료되면 객체로 변환된 원시 타입 값은 다시 원시 타입 값으로 복귀한다.</b>

자세한 내용은 [Prototype: 6.원시 타입(Primitive data type)](https://poiemaweb.com/js-prototype#6-%EC%9B%90%EC%8B%9C-%ED%83%80%EC%9E%85primitive-data-type%EC%9D%98-%ED%99%95%EC%9E%A5)의 확장을 참조 바란다.

Wrapper 객체는 String, Number, Boolean이 있다.

# [2. 호스트 객체](https://poiemaweb.com/js-built-in-object#2-%ED%98%B8%EC%8A%A4%ED%8A%B8-%EA%B0%9D%EC%B2%B4)
호스트 객체(Host object)는 <b>브라우저 환경에서 제공하는 window, XmlHttpRequest, HTMLElement 등의 DOM 노드 객체와 같이 호스트 환경에 정의된 객체를 말한다.</b> 예를 들어 브라우저에서 동작하는 환경과 브라우저 외부에서 동작하는 환경의 자바스크립트(Node.js)는 다른 호스트 객체를 사용할 수 있다.

브라우저에서 동작하는 환경의 호스트 객체는 전역 객체인 window, BOM(Browser Object Model)과 DOM(Document Object Model) 및 XMLHttpRequest 객체 등을 제공한다.

# [2.1 전역 객체(Global Object)](https://poiemaweb.com/js-built-in-object#21-%EC%A0%84%EC%97%AD-%EA%B0%9D%EC%B2%B4global-object)
[전역 객체](https://poiemaweb.com/js-global-object)는 모든 객체의 유일한 최상위 객체를 의미하며 일반적으로 Browser-side에서는 `window`, Server-side(Node.js)에서는 `global` 객체를 의미한다.

# [2.2 BOM (Browser Object Model)](https://poiemaweb.com/js-built-in-object#22-bom-browser-object-model)
브라우저 객체 모델은 브라우저 탭 또는 브라우저 창의 모델을 생성한다. 최상위 객체는 `window` 객체로 현재 브라우저 창 또는 탭을 표현하는 객체이다. 또한 이 객체의 자식 객체들은 브라우저의 다른 기능들을 표현한다.애 객체들은 Standard Built-in Object가 구성된 후에 구성된다.

![image](https://user-images.githubusercontent.com/44339530/132603135-3658cea7-1a7d-40a8-9177-d9c5e0ede50b.png)

자세한 내용은 [MDN Web APIs: Window](https://developer.mozilla.org/en-US/docs/Web/API/Window)를 참조하기 바란다.

# [2.2 DOM (Document Object Model)](https://poiemaweb.com/js-built-in-object#22-dom-document-object-model)
문서 객체 모델은 현재 웹페이지의 모델을 생성한다. 최상위 객체는 `document` 객체로 전체 문서를 표현한다. 또한 이 객체의 자식 객체들은 문서의 다른 요소들을 표현한다. 이 객체들은 Standard Built-in Objects가 구성된 후에 구성된다.

![image](https://user-images.githubusercontent.com/44339530/132603277-38b61835-df03-422f-b3f1-e67e70cc1ef2.png)

자세한 내용은 [Javascript DOM](https://poiemaweb.com/js-dom)를 참조하기 바란다.

#### 출처
- [https://poiemaweb.com/js-built-in-object](https://poiemaweb.com/js-built-in-object)