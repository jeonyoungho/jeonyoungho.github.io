---
title: "[개발자 블로그] 5.18 실행 컨텍스트와 자바스크립트의 동작 원리(PoiemaWeb-Javascript)"
date: 2021-09-08 +0800
categories: [Frontend, PoiemaWeb, Javascript]
tags: [poiemaweb, javascript, es6]
toc: true
comments: true
---

해당 포스팅의 내용은 ES3를 기반으로 하고 있다.

# [1. 실행 컨텍스트](https://poiemaweb.com/js-execution-context#1-%EC%8B%A4%ED%96%89-%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8)
실행 컨텍스트(Execution Context)는 scope, hoisting, this, function, closure 등의 동작원리를 담고 있는 자바스크립트의 핵심원리이다. 실행 컨텍스트를 바로 이해하지 못하면 코드 독해가 어려워지며 디버깅도 매우 곤란해 질 것이다.

ECMAScript 스펙에 따르면 실행 컨텍스트를 실행 가능한 코드를 형상화하고 구분하는 추상적인 개념이라고 정의한다. 좀 더 쉽게 말하자면 실행 컨텍스트는 실행 가능한 코드가 실행되기 위해 필요한 환경 이라고 말할 수 있겠다. 여기서 말하는 실행 가능한 코드는 아래와 같다.

- 전역 코드 : 전역 영역에 존재하는 코드
- Eval 코드 : [eval 함수](https://poiemaweb.com/js-built-in-object#2121-eval)로 실행되는 코드
- 함수 코드 : 함수 내에 존재하는 코드

일반적으로 실행 가능한 코드는 전역 코드와 함수 내 코드이다.

자바스크립트 엔진은 코드를 실행하기 위하여 실행에 필요한 여러가지 정보를 알고 있어야 한다. 실행에 필요한 여러가지 정보란 아래와 같은 것들이 있다.

- 변수 : 전역변수, 지역변수, 매개변수, 객체의 프로퍼티
- 함수 선언
- 변수의 유효범위(Scope)
- this

이와 같이 실행에 필요한 정보를 형상화하고 구분하기 위해 자바스크립트 엔진은 실행 컨텍스트를 물리적 객체의 형태로 관리한다. 아래의 코드를 살펴보자.

~~~
var x = 'xxx';

function foo () {
  var y = 'yyy';

  function bar () {
    var z = 'zzz';
    console.log(x + y + z);
  }
  bar();
}
foo();
~~~

위 코드를 실행하면 아래와 같이 실행 컨텍스트 스택(Stack)이 생성하고 소멸한다. 현재 실행 중인 컨텍스트에서 이 컨텍스트와 관련없는 코드(예를 들어 다른 함수)가 실행되면 새로운 컨텍스트가 생성된다. 이 컨텍스트는 스택에 쌓이게 되고 컨트롤(제어권)이 이동한다.

![image](https://user-images.githubusercontent.com/44339530/132434574-bd8bec7d-1d01-4627-8d4d-e0c0f21020f5.png)

1) 컨트롤이 실행 가능한 코드로 이동하면 논리적 스택 구조를 가지는 새로운 실행 컨텍스트 스택이 생성된다. 스택은 LIFO(Last In First Out, 후입 선출)의 구조를 가지는 나열 구조이다.

2) 전역 코드(Global code)로 컨트롤이 진입하면 전역 실행 컨텍스트가 생성되고 실행 컨텍스트 스택에 쌓인다. 전역 실행 컨텍스트는 애플리케이션이 종료될 때(웹 페이지에서 나가거나 브라우저를 닫을 때)까지 유지된다.

3) 함수를 호출하면 해당 함수의 실행 컨텍스트가 생성되며 직전에 실행된 코드 블록의 실행 컨텍스트 위에 쌓인다.

4) 함수 실행이 끝나면 해당 함수의 실행 컨텍스트를 파기하고 직전의 실행 컨텍스트에 컨트롤을 반환한다.

# [2. 실행 컨텍스트의 3가지 객체](https://poiemaweb.com/js-execution-context#2-%EC%8B%A4%ED%96%89-%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8%EC%9D%98-3%EA%B0%80%EC%A7%80-%EA%B0%9D%EC%B2%B4)
실행 컨텍스트는 실행 가능한 코드를 형상화하고 구분하는 추상적인 개념이지만 물리적으로는 객체의 형태를 가지며 아래의 3가지 프로퍼티를 소유한다.

![image](https://user-images.githubusercontent.com/44339530/132434752-1295e536-aa06-4c67-a2a5-4f7865e56e28.png)

# [2.1 Variable Object (VO / 변수객체)](https://poiemaweb.com/js-execution-context#21-variable-object-vo--%EB%B3%80%EC%88%98%EA%B0%9D%EC%B2%B4)
실행 컨텍스트가 생성되면 자바스크립트 엔진은 실행에 필요한 여러 정보들을 담을 객체를 생성한다. 이를 Variable Object(VO / 변수 객체)라고 한다. Variable Object는 코드가 실행될 때 엔진에 의해 참조되며 코드에서는 접근할 수 없다.

Variable Object는 아래의 정보를 담는 객체이다.
- 변수
- 매개변수(parameter)와 인수 정보(arguments)
- 함수 선언(함수 표현식은 제외)

Variable Object는 실행 컨텍스트의 프로퍼티이기 때문에 값을 갖는데 이 값은 다른 객체를 가리킨다. 그런데 전역 코드 실행시 생성되는 전역 컨텍스트의 경우와 함수를 실행할 때 생성되는 함수 컨텍스트의 경우, 가리키는 객체가 다르다. 이는 전역 코드와 함수의 내용이 다르기 때문이다. 예를 들어 전역 코드에는 매개변수가 없지만 함수에는 매개변수가 있다.

Variable Object가 가리키는 객체는 아래와 같다.

<b>1) 전역 컨텍스트의 경우</b>

~~~
Variable Object는 유일하며 최상위에 위치하고 모든 전역 변수, 전역 함수 등을 포함하는 전역 객체(Global Object / GO)를 가리킨다. 전역 객체는 전역에 선언된 전역 변수와 전역 함수를 프로퍼티로 소유한다.
~~~

![image](https://user-images.githubusercontent.com/44339530/132435114-0113bea3-6d41-4591-8631-8a7315e2d3bd.png)

<b>2) 함수 컨텍스트의 경우</b>

~~~
Variable Object는 Activation Object(AO / 활성 객체)를 가리키며 매개변수와 인수들의 정보를 배열의 형태로 담고 있는 객체인 [arguments object](https://poiemaweb.com/js-function#61-arguments-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0)가 추가된다.
~~~

![image](https://user-images.githubusercontent.com/44339530/132435205-fee91e1b-5c77-4baf-8ad0-d35b0a7f2d09.png)

# [2.2 Scope Chain (SC)](https://poiemaweb.com/js-execution-context#22-scope-chain-sc)




# 프로토타입 체인과 스코프 체인

1) 프로토타입 체인: object의 property를 탐색할 때

2) 스코프 체인: 일반 함수의 변수를 탐색할 때

# 자바스크립트의 실행 방식
- 자바스크립트는 [JIT컴파일 방식](https://ko.wikipedia.org/wiki/JIT_%EC%BB%B4%ED%8C%8C%EC%9D%BC)이다.(자바와 동일)
  - 쉽게 말하면 소스 코드 전체를 컴파일하여 기계어로 번역 후 로직이 실행되는 방식

#### 출처
- [https://poiemaweb.com/js-this](https://poiemaweb.com/js-this)