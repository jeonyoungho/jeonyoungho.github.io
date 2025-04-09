---
title: "[개발자 블로그] 5.7 연산자(PoiemaWeb-Javascript)"
date: 2021-09-06 17:50:00 +0800
categories: [Frontend, PoiemaWeb, Javascript]
tags: [poiemaweb, javascript, es6]
toc: true
comments: true
---

# [2. 문과 표현식](https://poiemaweb.com/js-operator#2-%EB%AC%B8%EA%B3%BC-%ED%91%9C%ED%98%84%EC%8B%9D)
자바스크립트 statement 끝에 세미콜론을 안 붙여줘도 되는 이유는 자바스크립트 엔진에는 statement 끝이라고 예측되는 지점에 세미콜론을 자동으로 붙여주는 세미콜론 자동 삽입 기능(ASI, automatic semicolon insertion)이 있기 때문이다.

세미콜론을 반드시 붙여야 한다는 주장이 대다수를 차지하지만 붙이지 말아야 한다 주장도 설득력이 있다. 하지만 ESLint와 같은 정적 분석 도구에서도 세미콜론 사용을 기본으로 설정하고 있고 TC39(ECMAScript 기술 위원회)도 세미콜론 사용을 권장하는 분위기이므로 세미콜론을 붙이는게 좋다.

표현식의 역할은 값을 생성하는 것이다. 문의 역할은 표현식으로 생성한 값을 사용해 컴퓨터에게 명령을 내리는 것이다. 문에는 표현식인 문과 표현식이 아닌 문이 있다. 예를 들어 선언문은 값으로 평가될 수 없다. 따라서 표현식이 아닌 문이다. 하지만 할당문은 그 자체가 표현식인 문이다.

~~~
// 선언문(Declaration statement)
var x = 5 * 10; // 표현식 x = 5 * 10를 포함하는 문이다.

// 할당문(Assignment statement)
x = 100; // 이 자체가 표현식이지만 완전한 문이기도 하다.
~~~

따라서 아래와 같은 코드는 불가능하다.

~~~
var foo = var x = 100;
~~~

# [4.3 문자열 연결 연산자](https://poiemaweb.com/js-operator#43-%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%97%B0%EA%B2%B0-%EC%97%B0%EC%82%B0%EC%9E%90)
자바스크립트 엔진은 개발자의 의도와 상관없이 암묵적으로 타입을 자동 변환시킨다. 
- true -> 1
- false -> 0
- null -> 0
- null -> 0
- undefined -> NaN

~~~
1 + true       // 2 (true → 1)
1 + false      // 1 (false → 0)
~~~

# [6.1 동등 / 일치 비교 연산자](https://poiemaweb.com/js-operator#61-%EB%8F%99%EB%93%B1--%EC%9D%BC%EC%B9%98-%EB%B9%84%EA%B5%90-%EC%97%B0%EC%82%B0%EC%9E%90)
'==(동등 비교)': 두 변수의 값만 일치하는지 비교
'===(일치 비교)': 두 변수의 타입과 값이 일치하는지 비교

동등/일치 비교 연산자와 관련된 더 자세한 내용은 [여기](https://velog.io/@filoscoder/-%EC%99%80-%EC%9D%98-%EC%B0%A8%EC%9D%B4-oak1091tes)를 참고하자.

일치 비교 연산자에서 주의할 것은 NaN이다.

~~~
NaN === NaN // false
~~~

NaN은 자신과 일치하지 않는 유일한 값이다. 따라서 숫자가 NaN인지 조사하려면 비트인 함수 isNaN을 사용한다.
왜 NaN == NaN이 false가 나오는 이유는 아래의 코드를 통해 쉽게 이해할 수 있다.

~~~
function foo(a, b) {
  return a / 5 === b * 20;
}

foo('good', 'test');
~~~

foo함수의 return문을 해석하면 NaN == NaN이 될 것이다. 이 결과는 true라고 볼 수 없다. 둘 다 숫자가 아님을 뜻할 뿐이지 같은 값이라고는 볼 수 없다. 그러기에 false로 나온다고 이해하면 된다. NaN을 체크하려면 아래와 같이 내장함수인 isNaN을 사용하면 된다.

~~~
isNaN(NaN);
~~~

NaN의 number의 type중 하나이다. number로 해석하려다 해석이 불가능한 값을 NaN이라 볼 수 있다. 예를 들어, 아래와 같은 코드를 예시로 볼 수 있다. 'alice'라는 문자열을 number로 변환하려 하지만 해석이 불가능하기에 결과는 NaN이 출력된다. NaN은 직관적으로 잘못된 연산이 발생했다는 정도로 인지하면 된다.

~~~
console.log(parseInt('alice')); 
~~~

# [7. 삼항 조건 연산자](https://poiemaweb.com/js-operator#4-%EC%82%BC%ED%95%AD-%EC%A1%B0%EA%B1%B4-%EC%97%B0%EC%82%B0%EC%9E%90)
삼항연산자는 값을 도출해내서 활용하는 목적(연산, return...)으로 쓸 때 사용하고 그 외의 경우에는 if ~ else로 처리한다.

ex1) 
~~~
function foo(flag) {
  return flag ? 1 : 2;
}
~~~

flag 값에 따라 1 또는 2를 도출해내서 리턴하는 목적으로 사용하기에 삼항 연산자가 적합하다.

ex2) 
~~~

flag = false;
var result = 0;

result += flag ? 1 : 2;
console.log(result);
~~~

flag 값에 따라 1 또는 2를 도출해내서 더하는 목적으로 사용하기에 삼항 연산자가 적합하다.

하지만 다음과 같은 코드는 지양하는게 좋다.

~~~
flag ? console.log('1') : console.log('2');
~~~

# [8. 논리 연산자](https://poiemaweb.com/js-operator#8-%EB%85%BC%EB%A6%AC-%EC%97%B0%EC%82%B0%EC%9E%90)
논리합(||) 연산자와 논리곱(&&) 연산자의 연산 결과는 불리언 값이 아닐 수도 있다. 이 두 연산자는 언제나 피연산자 중 어는 한쪽 값을 반환한다. 이에 대해서는 뒷부분의 '단축 평가'에서 자세히 살펴볼 수 있다.

~~~
// 단축 평가
'Cat' && 'Dog' // “Dog”
~~~

# [11. typeof 연산자](https://poiemaweb.com/js-operator#11-typeof-%EC%97%B0%EC%82%B0%EC%9E%90)
typeof 연산자가 반환하는 7가지 데이터 타입은 아래와 같다.
- string
- number
- boolean
- undefined
- symbol
- object
- function

## typeof 연산자와 관련된 유의사항
1) null을 반환하는 경우는 없다. 아래처럼 null을 할당 후 typeof연산자를 통해 출력하면 object타입이 출력된다.

~~~
var foo = 123;
foo = null;

console.log(typeof foo); // "object"
~~~

따라서 null 타입을 확인할 때는 typeof 연산자를 사용하지 말고 일치 연산자(===)를 사용하도록한다.

~~~
var foo = null;
console.log(typeof foo === null); // false
console.log(foo === null);        // true
~~~

2) NaN도 number타입으로 출력된다.

~~~
typeof NaN             // "number"
~~~

3) 선언하지 않은 식별자를 typeof 연산자로 연산해 보면 ReferenceError가 발생하지 않고 “undefined”를 반환한다.

~~~
typeof undeclared  // "undefined"
~~~

#### 출처
- [https://poiemaweb.com/js-operator](https://poiemaweb.com/js-operator)