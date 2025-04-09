---
title: "[개발자 블로그] 5.6 데이터 타입과 변수(PoiemaWeb-Javascript)"
date: 2021-09-06 17:40:00 +0800
categories: [Frontend, PoiemaWeb, Javascript]
tags: [poiemaweb, javascript, es6]
toc: true
comments: true
---

# [1.1.1 number](https://poiemaweb.com/js-data-type-variable#111-number)
Javascript는 모든 수를 실수(일반적으로 소수를 가리킴)로 처리한다.

~~~
console.log(3/2);
=> 결과: 1.5
~~~

# [1.1.2 string](https://poiemaweb.com/js-data-type-variable#112-string)
문자열은 배열처럼 인덱스를 통해 접근할 수 있으며 immutable특징을 가진다.

~~~
var str = "string"
str[0] = 'S'
~~~

위의 코드 처럼 이미 생성된 문자열의 일부 문자를 변경해도 반영되지 않는다(이때 에러가 발생하지 않는다). 한 번 생성된 문자열은 read only로서 변경할 수 없다.(immutable)
(Java의 String과 같다고 생각하면 된다.)

# [1.1.4 undefined](https://poiemaweb.com/js-data-type-variable#114-undefined)
변수 선언에 의해 확보된 메모리 공간을 처음 할당이 이루어질 때 까지 빈상태로 내버려두지 않고 자바스크립트 엔진이 undefined로 초기화한다.자바스크립트 엔진이 변수 초기화에 사용하는 이값을 만약 개발자가 마음대로 할당한다면 undefined의 본래 취지와 어긋날 뿐더러 혼란을 줄 수 있으므로 권장하지 않는다. 만약 개발자가 의도적으로 변수의 값이 없다는 것을 명시하고 싶은 경우엔 undefined가 아닌 null을 할당하면 된다.

# [1.1.5 null](https://poiemaweb.com/js-data-type-variable#115-null)
타입을 나타내는 문자열을 반환하는 typeof 연산자로 null값을 연산해 보면 null이 아닌 object가 나온다. 이는 자바스크립트의 설계상의 오류이다.

~~~
var foo = null;
console.log(typeof foo); // object
~~~

따라서 null 타입을 확인할 때 typeof 연산자를 사용하면 안되고 일치 연산자(===)를 사용하여야 한다.

~~~
var foo = null;
console.log(typeof foo === null); // false
console.log(foo === null); // true
~~~

# [1.1.6 symbol](https://poiemaweb.com/js-data-type-variable#116-symbol)
심볼(symbol)은 ES6에서 새롭게 추가된 7번째 타입으로 변경 불가능한 원시 타입의 값이다. 심볼은 주로 이름의 충돌 위험이 없는 유일한 객체의 프로퍼티 키(property key)를 만들기 위해 사용한다. 심볼은 symbol함수를 호출해 생성한다. 이때 생성된 심볼 값은 다른 심볼 값들과 다른 유일한 심볼 값이다.

~~~
// 심볼 key는 이름의 충돌 위험이 없는 유일한 객체의 프로퍼티 키
var key = Symbol('key');
console.lof(typeof key); // symbol

var obj = {};
obj[key] = 'value';
console.log(obj[key]); // value
~~~

# [1.2 객체 타입 (Object type, Reference type)](https://poiemaweb.com/js-data-type-variable#12-%EA%B0%9D%EC%B2%B4-%ED%83%80%EC%9E%85-object-type-reference-type)
자바스크립트는 객체 기반의 스크립트 언어로서 자바스크립트를 이루고 있는 거의 "모든 것"이 객체이다. 원시 타입(Primitives)을 제외한 나머지 값들(배열, 함수, 정규표현식 등)은 모두 객체이다. 또한 객체는 콜바이레퍼런스 방식으로 전달된다.

# [2.2 변수의 선언](https://poiemaweb.com/js-data-type-variable#21-%EB%B3%80%EC%88%98%EC%9D%98-%EC%84%A0%EC%96%B8)
var 키워드로 선언한 변수는 중복 선언이 가능하다. 다시 말해 변수명이 같은 변수를 중복해 선언해도 에러가 발생하지 않는다. (이전 변수의 값을 덮어쓴다)  만약 동일한 변수명이 선언되어 있는 것을 모르고 변수를 중복 선언했다면 의도치 않게 변수의 값을 변경하는 부작용을 발생시킨다. 따라서 변수의 중복 선언은 문법적으로 허용되지만 사용하지 않는 것이 좋다.

~~~
var x = 1;
console.log(x); // 1

// 변수의 중복 선언
var x = 100;
console.log(x); // 100
~~~

# [2.2 동적 타이핑 (Dynamic Typing)](https://poiemaweb.com/js-data-type-variable#22-%EB%8F%99%EC%A0%81-%ED%83%80%EC%9D%B4%ED%95%91-dynamic-typing)
자바스크립트는 동적 타입(dynamic/weak type) 언어이다. 이것은 변수의 타입 지정없이 값이 할당되는 고자ㅓㅇ에서 값의 타입에 의해 자동으로 타입이 결정될 것이라는 뜻이다. 따라서 같은 변수에 여러 타입의 값을 할당할 수 있따. 이를 동적 타이핑이라 한다.

~~~
var foo;

console.log(typeof foo);  // undefined

foo = null;
console.log(typeof foo);  // object

foo = {};
console.log(typeof foo);  // object

foo = 3;
console.log(typeof foo);  // number

foo = 3.14;
console.log(typeof foo);  // number

foo = 'Hi';
console.log(typeof foo);  // string

foo = true;
console.log(typeof foo);  // boolean
~~~

# [2.3 변수 호이스팅(Variable Hoisting)](https://poiemaweb.com/js-data-type-variable#23-%EB%B3%80%EC%88%98-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85variable-hoisting)
자바스크립트는 C-family 언어와는 달리 모든 선언문은 호이스팅 된다. 쉽게 설명하자면 뒤에 선언된 변수가 미리 스코프에 등록되기 때문에 미리 참조해서 사용해도 ReferenceError가 발생하지 않고 undefined상태가 된다.

~~~
console.log(foo); // ① undefined
var foo = 123;
console.log(foo); // ② 123
{
  var foo = 456;
}
console.log(foo); // ③ 456
~~~

자바스크립트의 변수는 다른 C-family와는 달리 블록 레벨 스코프(block-level scope)를 가지지 않고 함수 레벨 스코프(function-level scope)를 갖는다. 단, ECMAScript 6에서 도입된 let, const 키워드를 사용하면 블록 레벨 스코프를 사용할 수 있다.

- 함수 레벨 스코프(Function-level scope)
    - 함수 내에서 선언된 변수는 함수 내에서만 유효하며 함수 외부에서는 참조할 수 없다. 즉, 함수 내부에서 선언한 변수는 지역 변수이며 함수 외부에서 선언한 변수는 모두 전역 변수이다.
- 블록 레벨 스코프(Block-level scope)
    - 코드 블록 내에서 선언된 변수는 코드 블록 내에서만 유효하며 코드 블록 외부에서는 참조할 수 없다.

# [2.4 var 키워드로 선언된 변수의 문제점](https://poiemaweb.com/js-data-type-variable#24-var-%ED%82%A4%EC%9B%8C%EB%93%9C%EB%A1%9C-%EC%84%A0%EC%96%B8%EB%90%9C-%EB%B3%80%EC%88%98%EC%9D%98-%EB%AC%B8%EC%A0%9C%EC%A0%90)
1) 함수 레벨 스코프(Function-level scope)
- 전역 변수의 남발
- for loop 초기화식에서 사용한 변수를 for loop 외부 또는 전역에서 참조할 수 있다.

2) var 키워드 생략 허용
-의도하지 않은 변수의 전역화

3) 중복 선언 허용
- 의도하지 않은 변수값 변경

4)변수 호이스팅
- 변수를 선언하기 전에 참조가 가능하다.

전역 변수는 유효 범위가 넓어서 어디에서 어떻게 사용될 지 파악하기 힘들다. 이는 의도치 않은 변수의 변경이 발생할 수 있는 가능성이 증가한다. 이는 애플리케이션의 장애로 이어질 가능성이 크다. 그러므로 변수의 유효 범위는 좁게 해야만 한다.

ES6는 이러한 var의 단점을 보완하기 위해 let과 const 키워드를 도입하였다.

#### 출처
- [https://poiemaweb.com/js-data-type-variable](https://poiemaweb.com/js-data-type-variable)