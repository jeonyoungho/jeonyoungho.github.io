---
title: "[개발자 블로그] PoiemaWeb - javascript" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-09-03 +0800
categories: [Frontend, Javascript] # categories는 최대 2개까지 가능
tags: [javascript, ES6] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# 2021년 9월 3일
1) 브라우저 동작 원리
-> 자바 스크립트는 렌더링 엔진이 아닌 자바스크립트 엔진이 처리한다. HTML파서는 script태그를 만나면 자바 스크립트 코드를 실행하기 위해 DOM 생성 프로세스를 중지하고 자바스크립트 엔진으로 제어 권한을 넘긴다.
제어 권한을 넘겨 받은 자바스크립트 엔진은 script 태그 내의 자바스크립트 코드 또는 script 태그의 src 어트리뷰트에 정의된 자바스크립트 파일을 로드하고 파싱하여 실행한다. 자바슼릡트의실행이 완료되면 다시 HTML 파서로 제어 권한을 넘겨서 브라우저가 중지했던 시점부터 DOM 생성을 재개한다.
이처럼 브라우저는 동기(Synchronous)적으로 HTML, CSS, Javascript를 처리한다.  이것은 script 태그의 위치에 따라 블로킹이 발생하여 DOM의 생성이 지연될 수 있다는 것을 의미한다. 

따라서 script 태그의 위치는 중요한 의미를 갖는다. javascript코드는 body요소의 가장 아래에 위치시키는 것이 가장 이상적이다. 그 이유는 아래와 같다.

- HTML 요소들이 스크립트 로딩 지연으로 인해 렌더링에 지장 받는 일이 발생하지 않아 페이지 로딩 시간이 단축된다.
- DOM이 완성되지 않은 상태에서 자바스크립트가 DOM을 조작한다면 에러가 발생한다.

일반적으로 브라우저의 렌더링엔진은 HTML파일을 위에서 부터 아래로 순서대로 읽어처리한다. 하지만 dom엘리먼트들이 구성되지 않은 상태에서 ajax통신을 통해 데이터를 받아와 dom엘리먼트를 조작한다?
에러가 발생할 것이고 이와 같은 이유로 HTML파일에서 css는 위쪽에 javascript코드는 아래쪽에 위치시키는 것이 좋다.
(직관적으로 생각해봐도 서버로부터 데이터를 받아와서 브라우저에 표시했는데 css가 적용이 안되 깨져보이는 상태면 사용자에게 좋지 못한 인상을 줄 것이다)
(참고: https://poiemaweb.com/js-browser)

2) javascript는 모든 수를 실수(일반적으로 소수를 가리킴)로 처리한다.

~~~
console.log(3/2);
=> 결과: 1.5
~~~


3) 문자열은 배열처럼 인덱스를 통해 접근할 수 있으며 immutable특징을 가진다.

~~~
var str = "string"
str[0] = 'S'
~~~

위의 코드 처럼 이미 생성된 문자열의 일부 문자를 변경해도 반영되지 않는다(이때 에러가 발생하지 않는다). 한 번 생성된 문자열은 read only로서 변경할 수 없다.(immutable)
(Java의 String과 같다고 생각하면 된다.)

# 2021년 9월 3일
1) 브라우저 동작 원리
-> 자바 스크립트는 렌더링 엔진이 아닌 자바스크립트 엔진이 처리한다. HTML파서는 script태그를 만나면 자바 스크립트 코드를 실행하기 위해 DOM 생성 프로세스를 중지하고 자바스크립트 엔진으로 제어 권한을 넘긴다.
제어 권한을 넘겨 받은 자바스크립트 엔진은 script 태그 내의 자바스크립트 코드 또는 script 태그의 src 어트리뷰트에 정의된 자바스크립트 파일을 로드하고 파싱하여 실행한다. 자바슼릡트의실행이 완료되면 다시 HTML 파서로 제어 권한을 넘겨서 브라우저가 중지했던 시점부터 DOM 생성을 재개한다.
이처럼 브라우저는 동기(Synchronous)적으로 HTML, CSS, Javascript를 처리한다.  이것은 script 태그의 위치에 따라 블로킹이 발생하여 DOM의 생성이 지연될 수 있다는 것을 의미한다. 

따라서 script 태그의 위치는 중요한 의미를 갖는다. javascript코드는 body요소의 가장 아래에 위치시키는 것이 가장 이상적이다. 그 이유는 아래와 같다.

- HTML 요소들이 스크립트 로딩 지연으로 인해 렌더링에 지장 받는 일이 발생하지 않아 페이지 로딩 시간이 단축된다.
- DOM이 완성되지 않은 상태에서 자바스크립트가 DOM을 조작한다면 에러가 발생한다.

일반적으로 브라우저의 렌더링엔진은 HTML파일을 위에서 부터 아래로 순서대로 읽어처리한다. 하지만 dom엘리먼트들이 구성되지 않은 상태에서 ajax통신을 통해 데이터를 받아와 dom엘리먼트를 조작한다?
에러가 발생할 것이고 이와 같은 이유로 HTML파일에서 css는 위쪽에 javascript코드는 아래쪽에 위치시키는 것이 좋다.
(직관적으로 생각해봐도 서버로부터 데이터를 받아와서 브라우저에 표시했는데 css가 적용이 안되 깨져보이는 상태면 사용자에게 좋지 못한 인상을 줄 것이다)
(참고: https://poiemaweb.com/js-browser)

2) javascript는 모든 수를 실수(일반적으로 소수를 가리킴)로 처리한다.

~~~
console.log(3/2);
=> 결과: 1.5
~~~


3) 문자열은 배열처럼 인덱스를 통해 접근할 수 있으며 immutable특징을 가진다.

~~~
var str = "string"
str[0] = 'S'
~~~

위의 코드 처럼 이미 생성된 문자열의 일부 문자를 변경해도 반영되지 않는다(이때 에러가 발생하지 않는다). 한 번 생성된 문자열은 read only로서 변경할 수 없다.(immutable)
(Java의 String과 같다고 생각하면 된다.)

# 2021년 9월 6일
1) 변수 선언에 의해 확보된 메모리 공간을 처음 할당이 이루어질 때 까지 빈상태로 내버려두지 않고 자바스크립트 엔진이 undefined로 초기화한다.자바스크립트 엔진이 변수 초기화에 사용하는 이값을 만약 개발자가 마음대로 할당한다면 undefined의 본래 취지와 어긋날 뿐더러 혼란을 줄 수 있으므로 권장하지 않는다. 만약 개발자가 의도적으로 변수의 값이 없다는 것을 명시하고 싶은 경우엔 undefined가 아닌 null을 할당하면 된다.

2) 타입을 나타내는 문자열을 반환하는 typeof 연산자로 null값을 연산해 보면 null이 아닌 object가 나온다. 이는 자바스크립트의 설계상의 오류이다.

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

3) 심볼(symbol)은 ES6에서 새롭게 추가된 7번째 타입으로 변경 불가능한 원시 타입의 값이다. 심볼은 주로 이름의 충돌 위험이 없는 유일한 객체의 프로퍼티 키(property key)를 만들기 위해 사용한다. 심볼은 symbol함수를 호출해 생성한다. 이때 생성된 심볼 값은 다른 심볼 값들과 다른 유일한 심볼 값이다.

~~~
// 심볼 key는 이름의 충돌 위험이 없는 유일한 객체의 프로퍼티 키
var key = Symbol('key');
console.lof(typeof key); // symbol

var obj = {};
obj[key] = 'value';
console.log(obj[key]); // value
~~~

4) 자바스크립트는 객체 기반의 스크립트 언어로서 자바스크립트를 이루고 있는 거의 "모든 것"이 객체이다. 원시 타입(Primitives)을 제외한 나머지 값들(배열, 함수, 정규표현식 등)은 모두 객체이다. 또한 객체는 콜바이레퍼런스 방식으로 전달된다.

5) 상수는 ES6의 const 키워드를 사용해 표현할 수 있다. 

6) 자바스크립트의 '==' 와 '==='의 차이점
- '==(동등 비교)': 두 변수의 값만 일치하는지 비교
- '===(일치 비교)': 두 변수의 타입과 값이 일치하는지 비교

References
- [https://velog.io/@filoscoder/-%EC%99%80-%EC%9D%98-%EC%B0%A8%EC%9D%B4-oak1091tes](https://velog.io/@filoscoder/-%EC%99%80-%EC%9D%98-%EC%B0%A8%EC%9D%B4-oak1091tes)

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

NaN의 number의 type중 하나이다. number로 해석하려다 해석이 불가능한 값을 NaN이라 볼 수 있다. 예를 들어, 아래와 같은 코드를 예시로 볼 수 있다. 'alice'라는 문자열을 number로 변환하려 하지만 해석이 불가능하기에 결과는 NaN이 출력된다.

~~~
console.log(parseInt('alice')); 
~~~

7) var 키워드로 선언한 변수는 중복 선언이 가능하다. 다시 말해 변수명이 같은 변수를 중복해 선언해도 에러가 발생하지 않는다. (이전 변수의 값을 덮어쓴다)  만약 동일한 변수명이 선언되어 있는 것을 모르고 변수를 중복 선언했다면 의도치 않게 변수의 값을 변경하는 부작용을 발생시킨다. 따라서 변수의 중복 선언은 문법적으로 허용되지만 사용하지 않는 것이 좋다.

~~~
var x = 1;
console.log(x); // 1

// 변수의 중복 선언
var x = 100;
console.log(x); // 100
~~~

8) 자바 스클비트는 동적 타입(dynamic/weak type) 언어이다. 이것은 변수의 타입 지정없이 값이 할당되는 고자ㅓㅇ에서 값의 타입에 의해 자동으로 타입이 결정될 것이라는 뜻이다. 따라서 같은 변수에 여러 타입의 값을 할당할 수 있따. 이를 동적 타이핑이라 한다.

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

9) 자바스크립트는 C-family 언어와는 달리 모든 선언문은 호이스팅 된다. 쉽게 설명하자면 뒤에 선언된 변수가 미리 스코프에 등록되기 때문에 미리 참조해서 사용해도 ReferenceError가 발생하지 않고 undefined상태가 된다.

~~~
console.log(foo); // ① undefined
var foo = 123;
console.log(foo); // ② 123
{
  var foo = 456;
}
console.log(foo); // ③ 456
~~~

Reference: https://poiemaweb.com/js-data-type-variable#23-%EB%B3%80%EC%88%98-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85variable-hoisting

10) 자바스크립트의 변수는 다른 C-family와는 달리 블록 레벨 스코프(block-level scope)를 가지지 않고 함수 레벨 스코프(function-level scope)를 갖는다. 단, ECMAScript 6에서 도입된 let, const 키워드를 사용하면 블록 레벨 스코프를 사용할 수 있다.

- 함수 레벨 스코프(Function-level scope)
    - 함수 내에서 선언된 변수는 함수 내에서만 유효하며 함수 외부에서는 참조할 수 없다. 즉, 함수 내부에서 선언한 변수는 지역 변수이며 함수 외부에서 선언한 변수는 모두 전역 변수이다.
- 블록 레벨 스코프(Block-level scope)
    - 코드 블록 내에서 선언된 변수는 코드 블록 내에서만 유효하며 코드 블록 외부에서는 참조할 수 없다.

11) var 키워드로 선언된 변수의 문제점(https://poiemaweb.com/js-data-type-variable#24-var-%ED%82%A4%EC%9B%8C%EB%93%9C%EB%A1%9C-%EC%84%A0%EC%96%B8%EB%90%9C-%EB%B3%80%EC%88%98%EC%9D%98-%EB%AC%B8%EC%A0%9C%EC%A0%90)
- 전역 변수는 유효 범위가 넓어서 어디에서 어떻게 사용될 지 파악하기 힘들다. 이는 의도치 않은 변수의 변경이 발생할 수 있는 가능성이 증가한다. 이는 애플리케이션의 장애로 이어질 가능성이 크다. 그러므로 변수의 유효 범위는 좁게 해야만 한다.

ES6는 이러한 var의 단점을 보완하기 위해 let과 const 키워드를 도입하였다.

12) 자바스크립트 statement 끝에 세미콜론을 안 붙여줘도 되는 이유는 자바스크립트 엔진에는 statement 끝이라고 예측되는 지점에 세미콜론을 자동으로 붙여주는 세미콜론 자동 삽입 기능(ASI, automatic semicolon insertion)이 있기 때문이다.

13) 세미콜론을 반드시 붙여야 한다는 주장이 대다수를 차지하지만 붙이지 말아야 한다 주장도 설득력이 있다. 하지만 ESLint와 같은 정적 분석 도구에서도 세미콜론 사용을 기본으로 설정하고 있고 TC39(ECMAScript 기술 위원회)도 세미콜론 사용을 권장하는 분위기이므로 세미콜론을 붙이는게 좋다.

14) 표현식의 역할은 값을 생성하는 것이다. 문의 역할은 표현식으로 생성한 값을 사용해 컴퓨터에게 명령을 내리는 것이다. 문에는 표현식인 문과 표현식이 아닌 문이 있다. 예를 들어 선언문은 값으로 평가될 수 없다. 따라서 표현식이 아닌 문이다. 하지만 할당문은 그 자체가 표현식인 문이다.

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

15) 자바스크립트 엔진은 개발자의 의도와 상관없이 암묵적으로 타입을 자동 변환시킨다. 

- true -> 1
- false -> 0
- null -> 0
- null -> 0
- undefined -> NaN

~~~
1 + true       // 2 (true → 1)
1 + false      // 1 (false → 0)
~~~

16) 논리합(||) 연산자와 논리곱(&&) 연산자의 연산 결과는 불리언 값이 아닐 수도 있다. 이 두 연산자는 언제나 피연산자 중 어는 한쪽 값을 반환한다. 이에 대해서는 뒷부분의 '단축 평가'에서 자세히 살펴볼 수 있다.

~~~
// 단축 평가
'Cat' && 'Dog' // “Dog”
~~~

17) typeof 연산자가 반환하는 7가지 데이터 타입
- string
- number
- boolean
- undefined
- symbol
- object
- function

※ null을 반환하는 경우는 없다. 아래처럼 null을 할당 후 typeof연산자를 통해 출력하면 object타입이 출력된다.

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

※ NaN도 number타입으로 출력된다.

~~~
typeof NaN             // "number"
~~~

※ 선언하지 않은 식별자를 typeof 연산자로 연산해 보면 ReferenceError가 발생하지 않고 “undefined”를 반환한다.

~~~
typeof undeclared  // "undefined"
~~~

18) 레이블 문(Label statement)이란 식별자가 붙은 문을 말한다. 

~~~
// foo라는 레이블 식별자가 붙은 레이블 문
foo: console.log('foo');
~~~

레이블 문은 프로그램의 실행 순서를 제어하기 위해 사용한다. 사실 switch 문의 case 문과 default 문도 레이블 문이다. 레이블 문을 탈출하려면 break 문에 레이블 식별자를 지정한다.

~~~
// foo라는 식별자가 붙은 레이블 블록문
foo: {
  console.log(1);
  break foo; // foo 레이블 블록문을 탈출한다.
  console.log(2);
}

console.log('Done!');
~~~

중첩된 for 문의 내부 for 문에서 break 문을 실행하면 내부 for 문을 탈출하여 외부 for 문으로 진입한다. 이때 내부 for 문이 아닌 외부 for 문을 탈출하려면 레이블 문을 사용한다.

~~~
// outer라는 식별자가 붙은 레이블 for 문
outer: for (var i = 0; i < 3; i++) {
  for (var j = 0; j < 3; j++) {
    // i + j === 3이면 외부 for 문을 탈출한다.
    if (i + j === 3) break outer;
  }
}

console.log('Done!');
~~~

※ 중첩된 for 문을 외부로 탈출할 때 레이블 문은 유용하지만 그 외의 경우 레이블 문은 일반적으로 권장하지 않는다. 레이블 문을 사용하면 프로그램의 흐름이 복잡해져서 가독성이 나빠지고 오류를 발생시킬 가능성이 높아지기 때문이다.

19) 암묵적 타입 변환은 변수 값을 재할당해서 변경하는 것이 아니라 자바스크립트 엔진이 표현식을 에러없이 평가하기 위해 기존 값을 바탕으로 새로운 타입의 값을 만들어 단 한번 사용하고 버린다.

20) 논리곱 연산자 &&와 논리합 연산자 ||는 논리 평가를 결정한 피연산자의 평가 결과를 그대로 반환한다. 이를 단축 평가(Short-Circuit evaluation)라 부른다.

~~~
// 논리합(||) 연산자
'Cat' || 'Dog'  // 'Cat'
false || 'Dog'  // 'Dog'
'Cat' || false  // 'Cat'

// 논리곱(&&) 연산자
'Cat' && 'Dog'  // Dog
false && 'Dog'  // false
'Cat' && false  // false
~~~

21) 단축 평가를 유용하게 사용하는 방법

21-1) 객체가 null인지 확인하고 프로퍼티를 참조할 때

~~~
var elem = null;

console.log(elem.value); // TypeError: Cannot read property 'value' of null
console.log(elem && elem.value); // null
~~~

객체는 키(key)과 값(value)으로 구성된 프로퍼티(Property)들의 집합이다. 만약 객체가 null인 경우, 객체의 프로퍼티를 참조하면 타입 에러(TypeError)가 발생한다. 이때 단축 평가를 사용하면 에러를 발생시키지 않는다.

21-2) 함수의 인수(argument)를 초기화할 때

~~~
// 단축 평가를 사용한 매개변수의 기본값 설정
function getStringLength(str) {
  str = str || '';
  return str.length;
}

getStringLength();     // 0
getStringLength('hi'); // 2

// ES6의 매개변수의 기본값 설정
function getStringLength(str = '') {
  return str.length;
}

getStringLength();     // 0
getStringLength('hi'); // 2
~~~

함수를 호출할 때 인수를 전달하지 않으면 매개변수는 undefined를 갖는다. 이때 단축 평가를 사용하여 매개변수의 기본값을 설정하면 undefined로 인해 발생할 수 있는 에러를 방지할 수 있다.

22) 자바스크립트의 객체 생성 방법
자바와 같은 클래스 기반 객체 지향 언어는 클래스를 사전에 정의하고 필요한 시점에 new 연산자를 이용하여 인스턴스를 생성하는 방식으로 객체를 생성한다. 하지만 자바스크립트는 프로토타입 객체 지향 언어로서 클래스라는 개념이 없고 별도의 객체 생성 방법이 존재한다.

> **Note**: ECMAScript 6에서 새롭게 [클래스](https://poiemaweb.com/es6-class)가 도입되었다. 프로토타입 기반 프로그래밍은 클래스가 존재하지 않는 객체지향 프로그래밍 스타일이다. 클래스없이 프로토타입 체인과 클로저 등으로 객체 지향 언어의 상속, 캡슐화(정보 은닉) 등의 개념을 구현한다. 하지만 클래스 기반 언어에 익숙한 프로그래머들은 혼란을 일으킬 수 있으며 자바스크립트를 어렵게 느끼게하는 하나의 장벽처럼 인식되었다. ES6의 클래스는 기존 프로토타입 기반 객체지향 프로그래밍보다 클래스 기반 언어에 익숙한 프로그래머가 보다 빠르게 학습할 수 있는 단순하고 깨끗한 새로운 문법을 제시하고 있다. ES6의 클래스가 새로운 객체지향 모델을 제공하는 것이 아니며 클래스도 사실 함수이고 기존 프로토타입 기반 패턴의 문법적 설탕([Syntactic sugar](https://en.wikipedia.org/wiki/Syntactic_sugar))이다.

23) 자바스크립트의 생성자 함수

~~~
function Person(name, gender) {
var married = true;         // private
this.name = name;           // public
this.gender = gender;       // public
this.sayHello = function(){ // public
    console.log('Hi! My name is ' + this.name);
    console.log('married: ' + married)
};
}

var person = new Person('Lee', 'male');

console.log(typeof person); // object
console.log(person); // Person { name: 'Lee', gender: 'male', sayHello: [Function] }

console.log(person.gender);  // 'male'
console.log(person.married); // undefined

person.sayHello();
~~~

- 생성자 함수 이름은 일반적으로 대문자로 시작한다. 이것은 생성자 함수임을 인식하도록 도움을 준다.
- 프로퍼티 또는 메소드명 앞에 기술한 this는 생성자 함수가 생성할 인스턴스(instance)를 가리킨다.
- this에 연결(바인딩)되어 있는 프로퍼티와 메소드는 public(외부에서 참조 가능)하다.
- 생성자 함수 내에서 선언된 일반 변수는 private(외부에서 참조 불가능)하다. 즉, 생성자 함수 내부에서는 자유롭게 접근이 가능하나 외부에서 접근할 수 없다.

24) for-in 문은 객체의 문자열 키(key)를 순회하기 위한 문법이다. 배열에는 사용하지 않는 것이 좋다. 이유는 아래와 같다.
- 객체의 경우, 프로퍼티의 순서가 보장되지 않는다. 그 이유는 원래 객체의 프로퍼티에는 순서가 없기 때문이다. 배열은 순서를 보장하는 데이터 구조이지만 객체와 마찬가지로 순서를 보장하지 않는다.
- 배열 요소들만을 순회하지 않는다.

~~~
var person = {
  'first-name': 'Ung-mo',
  'last-name': 'Lee',
  gender: 'male'
};

// prop에 객체의 프로퍼티 이름이 반환된다. 단, 순서는 보장되지 않는다.
for (var prop in person) {
  console.log(prop + ': ' + person[prop]);
}

/*
first-name: Ung-mo
last-name: Lee
gender: male
*/

var array = ['one', 'two'];

// index에 배열의 경우 인덱스가 반환된다
for (var index in array) {
  console.log(index + ': ' + array[index]);
}

/*
0: one
1: two
*/

// 배열 요소들만을 순회하지 않는다.
var array = ['one', 'two'];
array.name = 'my array';

for (var index in array) {
  console.log(index + ': ' + array[index]);
}

/*
0: one
1: two
name: my array
*/
~~~

이와 같은 for-in 문의 단점을 극복하기 위해 ES6에서 for-of 문이 추가되었다.

~~~
const array = [1, 2, 3];
array.name = 'my array';

for (const value of array) {
  console.log(value);
}

/*
1
2
3
*/

for (const [index, value] of array.entries()) {
  console.log(index, value);
}

/*
0 1
1 2
2 3
*/
~~~

※ for–in 문은 객체의 프로퍼티를 순회하기 위해 사용하고 for–of 문은 배열의 요소를 순회하기 위해 사용한다.

25) 자바스크립의 함수 호출 시 인자 전달 방식은 자바와 동일하다고 생각하면 된다. (call by value)
- primitive타입의 경우에는 메모리에 있는 값을 복사하여 전달하기에 원본 값은 영향을 받지 않는다.
- 객체(참조)타입의 경우에는 메모리의 주소 값을 가지고 있는 레퍼런스를 복사하여 전달하기에 원본에 영향을 받게 된다.

26) 단순 복사, 얕은 복사(shallow copy), 깊은 복사(deep copy)
- 단순 복사: 완전히 동일한 객체(mutable한 경우에는 pass-by-reference로 값이 수정될 수 있지만, immutable한 경우에는 pass-by-value로 값이 수정되는 것이 아닌 아예 새로운 객체로 변경됨)
- 얕은 복사: 복합객체(껍데기)를 새롭게 생성하지만, 하지만 객체 내부의 객체는 동일한 객체!(원본 손상 가능성 있음)
- 깊은 복사: 복합객체(껍데기)를 새롭게 생성하고 그 안의 내용까지 재귀적으로 새롭게 생성

References
- https://blueshw.github.io/2016/01/20/shallow-copy-deep-copy/

27) 자바스크립트는 ES6의 let, const를 포함하여 모든 선언(var, let, const, function, function*, class)을 호이스팅(Hoisting)한다.

- 호이스팅이란 var 선언문이나 function 선언문 등 모든 선언문이 해당 Scope의 선두로 옮겨진 것처럼 동작하는 특성을 말한다. 즉, 자바스크립트는 모든 선언문(var, let, const, function, function*, class)이 선언되기 이전에 참조 가능하다.

~~~
var res = square(5);

function square(number) {
  return number * number;
}
~~~

- 함수 선언문으로 정의된 함수는 자바스크립트 엔진이 스크립트가 로딩되는 시점에 바로 초기화하고 이를 VO(variable object)에 저장한다. 즉, 함수 선언, 초기화, 할당이 한번에 이루어진다. 그렇기 때문에 함수 선언의 위치와는 상관없이 소스 내 어느 곳에서든지 호출이 가능하다.

~~~
var res = square(5); // TypeError: square is not a function

var square = function(number) {
  return number * number;
}
~~~

- 함수 선언문의 경우와는 달리 TypeError가 발생하였다. 함수 표현식의 경우 함수 호이스팅이 아니라 변수 호이스팅이 발생한다.
- 변수 호이스팅은 변수 생성 및 초기화와 할당이 분리되어 진행된다. 호이스팅된 변수는 undefined로 초기화 되고 실제값의 할당은 할당문에서 이루어진다.
- 함수 표현식은 함수 선언문과는 달리 스크립트 로딩 시점에 변수 객체(VO)에 함수를 할당하지 않고 runtime에 해석되고 실행되므로 이 두가지를 구분하는 것은 중요하다.
- 자바스크립트의 권위자인 더글러스 크락포드(Douglas Crockford)는 이와 같은 문제 때문에 함수 표현식만을 사용할 것을 권고하고 있다. 함수 호이스팅이 함수 호출 전 반드시 함수를 선언하여야 한다는 규칙을 무시하므로 코드의 구조를 엉성하게 만들 수 있다고 지적한다.
- 또한 함수 선언문으로 함수를 정의하면 사용하기에 쉽지만 대규모 애플리케이션을 개발하는 경우 인터프리터가 너무 많은 코드를 변수 객체(VO)에 저장하므로 애플리케이션의 응답속도는 현저히 떨어질 수 있으므로 주의해야 할 필요가 있다.

28) 자바스크립트 함수를 호출 시 인수를 전달하지 않으면 매개변수는 undefined로 초기화된다.


29) 삼항연산자는 값을 도출해내서 활용하는 목적(연산, return...)으로 쓸 때 사용하고 그 외의 경우에는 if ~ else로 처리한다.

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

# 2021년 9월 7일

## [1. 프로토타입 객체](https://poiemaweb.com/js-prototype#1-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-%EA%B0%9D%EC%B2%B4)
자바스크립트의 모든 객체는 자신의 부모 역할을 담당하는 객체와 연결되어 있다. 그리고 이것은 마치 객체 지향의 상속 개념과 같이 부모 객체의 프로퍼티 또는 메소드를 상속받아 사용할 수 있게 한다. 이러한 부모 객체를 `Prototype(프로토타입) 객체` 또는 줄여서 Prototype(프로토타입)이라 한다.

객체를 생성할 때 프로토타입은 결정된다. 결정된 프로토타입 객체는 다른 임의의 객체로 변경할 수 있다. 이것은 부모 객체인 프로토타입을 동적으로 변경할 수 있다는 것을 의미한다. 이러한 특징을 활용하여 객체의 상속을 구현할 수 있다.
## [3. constructor 프로퍼티](https://poiemaweb.com/js-prototype#3-constructor-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0)
프로토타입 객체는 constructor 프로퍼티를 갖는다. 이 constructor 프로퍼티는 객체의 입장에서 자신을 생성한 객체를 가리킨다.

예를 들어 Person()생성자 함수에 의해 생성된 객체를 foo라 하자. 이 foo 객체를 생성한 객체는 Person() 생성자 함수이다. 이때 foo 객체 입장에서 자신을 생성한 객체는 Person() 생성자 함수이며, foo객체의 프로토타입 객체는 Person.protytype이다. 따라서 프로토타입 객체 Person.protytype의 constructor프로퍼티는 Person()생성자 함수를 가리킨다.

## [4. Prototype chain](https://poiemaweb.com/js-prototype#4-prototype-chain)
자바스크립트는 특정 객체의 프로퍼티나 메소드에 접근하려고 할 때 해당 객체에 접근하려는 프로퍼티 또는 메소드가 없다면 [[Protytype]]이 가리키는 링크를 따라 자신의 부모 역할을 하는 프로토타입 객체의 프로퍼티나 메소드를 차례대로 검색한다. 이것을 `프로토타입 체인`이라 한다.

~~~
var student = {
  name: 'Lee',
  score: 90
}

// Object.prototype.hasOwnProperty()
console.log(student.hasOwnProperty('name')); // true
~~~

student 객체는 hasOwnProperty 메소드를 가지고 있지 않으므로 에러가 발생하여야 하나 정상적으로 결과가 출력되었다. 이는 student 객체의 [[Prototype]]이 가리키는 링크를 따라가서 student 객체의 부모 역할을 하는 프로토타입 객체(Object.protytype)의 메소드 hasOwnProperty를 호출하였기 때문에 가능한 것이다.

## [4.2 생성자 함수로 생성된 객체의 프로토타입 체인](https://poiemaweb.com/js-prototype#42-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98%EB%A1%9C-%EC%83%9D%EC%84%B1%EB%90%9C-%EA%B0%9D%EC%B2%B4%EC%9D%98-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-%EC%B2%B4%EC%9D%B8)

~~~
function Person(name, gender) {
  this.name = name;
  this.gender = gender;
  this.sayHello = function(){
    console.log('Hi! my name is ' + this.name);
  };
}

var foo = new Person('Lee', 'male');

console.dir(Person);
console.dir(foo);

console.log(foo.__proto__ === Person.prototype);                // ① true
console.log(Person.prototype.__proto__ === Object.prototype);   // ② true
console.log(Person.prototype.constructor === Person);           // ③ true
console.log(Person.__proto__ === Function.prototype);           // ④ true
console.log(Function.prototype.__proto__ === Object.prototype); // ⑤ true
~~~

![image](https://user-images.githubusercontent.com/44339530/132288173-b752be84-3727-478e-9aca-09d5b22daa11.png)

foo 객체의 프로토타입 객체인 Person.protytype 객체와 Person() 생성자 함수의 프로토타입 객체인 Function.protytype의 프로토타입 객체는 Object.protytype 객체이다.

이는 객체 리터럴 방식이나 생성자 함수 방식이나 결국은 모든 객체의 부모 객체인 Object.protytype 객체에서 프로토타입 체인이 끝나기 때문이다. 이때 Object.protytype 객체를 <b>프로토타입 체인의 종점(End of protytype chain)</b>이라 한다.

#### 출처
- [https://poiemaweb.com/](https://poiemaweb.com/)