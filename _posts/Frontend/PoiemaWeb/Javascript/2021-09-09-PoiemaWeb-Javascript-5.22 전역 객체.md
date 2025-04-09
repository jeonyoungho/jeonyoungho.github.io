---
title: "[PoiemaWeb] 5.22 전역 객체(PoiemaWeb-Javascript)"
date: 2021-09-09 +0800
categories: [Frontend, PoiemaWeb, Javascript]
tags: [poiemaweb, javascript, es6]
toc: true
comments: true
---

전역 객체(Global Object)는 모든 객체의 유일한 최상위 객체를 의미하며 일반적으로 Browser-side에서는 `window`, Server-side(Node.js)에서는 `global` 객체를 의미한다.
- 전역 객체는 실행 컨텍스트에 컨트롤이 들어가기 이전에 생성이 되며 constructor가 없기 때문에 new 연산자를 이용하여 새롭게 생성할 수 없다. 즉, 개발자가 전역 객체를 생성하는 것은 불가능하다.
- 전역 객체는 전역 스코프(Global Scope)를 갖게 된다.
- 전역 객체의 자식 객체를 사용할 때 전역 객체의 기술은 생략할 수 있다. 예를 들어 document 객체는 전역 객체 window의 자식 객체로서 window.document…와 같이 기술할 수 있으나 일반적으로 전역 객체의 기술은 생략한다.

~~~
document.getElementById('foo').style.display = 'none';
// window.document.getElementById('foo').style.display = 'none';
~~~

- 그러나 사용자가 정의한 변수와 전역 객체의 자식 객체 이름이 충돌하는 경우, 명확히 전역 객체를 기술하여 혼동을 방지할 수 있다.

~~~
function moveTo(url) {
  var location = {'href':'move to '};
  alert(location.href + url);
  // location.href = url;
  window.location.href = url;
}
moveTo('http://www.google.com');
~~~

- 전역 객체는 전역 변수(Global variable)를 프로퍼티로 가지게 된다. 다시 말해 <b>전역 변수는 전역 객체의 프로퍼티이다.</b>

~~~
var ga = 'Global variable';
console.log(ga);
console.log(window.ga);
~~~

- 글로벌 영역에 선언한 함수도 전역 객체의 프로퍼티로 접근할 수 있다. 다시 말해 <b>전역 함수는 전역 객체의 메소드이다.</b>

~~~
function foo() {
  console.log('invoked!');
}
window.foo();
~~~

- <b>Standard Built-in Objects(표준 빌트인 객체)도 역시 전역 객체의 자식 객체이다.</b> 전역 객체의 자식 객체를 사용할 때 전역 객체의 기술은 생략할 수 있으므로 표준 빌트인 객체도 전역 객체의 기술을 생략할 수 있다.

~~~
// window.alert('Hello world!');
alert('Hello world!');
~~~

# [1. 전역 프로퍼티(Global property)](https://poiemaweb.com/js-global-object#1-%EC%A0%84%EC%97%AD-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0global-property)
<>전역 프로퍼티는 전역 객체의 프로퍼티를 의미한다. 애플리케이션 전역에서 사용하는 값들을 나타내기 위해 사용한다.</b> 전역 프로퍼티는 간단한 값이 대부분이며 다른 프로퍼티나 메소드를 가지고 있지 않다.

# [1.1. Infinity](https://poiemaweb.com/js-global-object#11-infinity)
Infinity 프로퍼티는 양/음의 무한대를 나타내는 숫자값 Infinity를 갖는다.

~~~
console.log(window.Infinity); // Infinity

console.log(3/0);  // Infinity
console.log(-3/0); // -Infinity
console.log(Number.MAX_VALUE * 2); // 1.7976931348623157e+308 * 2
console.log(typeof Infinity); // number
~~~

# [1.2. NaN](https://poiemaweb.com/js-global-object#12-nan)
NaN 프로퍼티는 숫자가 아님(Not a Number)를 나타내는 숫자값 NaN을 갖는다. NaN 프로퍼티는 number.NaN 프로퍼티와 같다.

~~~
console.log(window.NaN); // NaN

console.log(Number('xyz')); // NaN
console.log(1 * 'string');  // NaN
console.log(typeof NaN);    // number
~~~

# [1.3. undefined](https://poiemaweb.com/js-global-object#13-undefined)
undefined 프로퍼티는 원시 타입 undefined를 값으로 갖는다.

~~~
console.log(window.undefined); // undefined

var foo;
console.log(foo); // undefined
console.log(typeof undefined); // undefined
~~~

# [2. 전역 함수(Global function)](https://poiemaweb.com/js-global-object#2-%EC%A0%84%EC%97%AD-%ED%95%A8%EC%88%98global-function)
전역 함수는 애플리케이션 전역에서 호출할 수 있는 함수로서 전역 객체의 메소드이다.

# [2.1. eval()](https://poiemaweb.com/js-global-object#21-eval)
매개변수에 전달된 문자열 구문 또는 표현식을 평가 또는 실행한다. 사용자로 부터 입력받은 콘텐츠(untrusted data)를 eval()로 실행하는 것은 보안에 매우 취약하다. eval()의 사용은 가급적으로 금지되어야 한다.

~~~
eval(string)
// string: code 또는 표현식을 나타내는 문자열. 표현식은 존재하는 객체들의 프로퍼티들과 변수들을 포함할 수 있다.
~~~

~~~
var foo = eval('2+2');
console.log(foo);

var x = 5;
var y = 4;
console.log(eval('x*y'));
~~~

# [2.2. isFinite()](https://poiemaweb.com/js-global-object#22-isfinite)
매개변수에 전달된 값이 정상적인 유한수인지 검사하여 그 결과를 Boolean으로 반환한다. 매개변수에 전달된 값이 숫자가 아닌 경우, 숫자로 변환한 후 검사를 수행한다.

~~~
isFinite(testValue) // testValue: 검사 대상 값
~~~

~~~
console.log(isFinite(Infinity));
console.log(isFinite(NaN));
console.log(isFinite('Hello'));
console.log(isFinite('2005/12/12'));

console.log(isFinite(0));
console.log(isFinite(2e64));
console.log(isFinite('10'));
console.log(isFinite(null));
~~~

<b>isFinite(null)은 true를 반환하는데 이것은 null을 숫자로 변환하여 검사를 수행하였기 때문이다.</b>

~~~
// null이 숫자로 암묵적 강제 형변환이 일어난 경우
console.log(Number(null));

// null이 불리언로 암묵적 강제 형변환이 일어난 경우
console.log(Boolean(null));
~~~

# [2.3. isNaN()](https://poiemaweb.com/js-global-object#23-isnan)
매개변수에 전달된 값이 NaN인지 검사하여 그 결과를 Boolean으로 반환한다. 매개변수에 전달된 값이 숫자가 아닌 경우, 숫자로 변환한 후 검사를 수행한다.

~~~
isNaN(testValue) // testValue: 검사 대상 값
~~~

~~~
isNaN(NaN)       // true
isNaN(undefined) // true: undefined → NaN
isNaN({})        // true: {} → NaN
isNaN('blabla')  // true: 'blabla' → NaN

isNaN(true)      // false: true → 1
isNaN(null)      // false: null → 0
isNaN(37)        // false

// strings
isNaN('37')      // false: '37' → 37
isNaN('37.37')   // false: '37.37' → 37.37
isNaN('')        // false: '' → 0 (빈 문자열 -> 숫자로 변환하면 0)
isNaN(' ')       // false: ' ' → 0 (공백 -> 숫자로 변환하면 0)
~~~

> **Note**: Date 객체를 Number로 형변환을 하게 되면 1970년 1월 1일 자정(UTC) 이후의 밀리초 수를 반환하게 되어 NaN이 아니게 된다.
하지만 Date 객체를 String으로 변환하면 단순한 문자열로 된 현재 날짜를 반환하게 된다.

~~~
// dates
isNaN(new Date())             // false: new Date() → Number
isNaN(new Date().toString())  // true:  String → NaN ()

console.log(new Number(new Date())); // result: [Number: 1631150130751]
console.log(new Date()); // result: Thu Sep 09 2021 10:15:30 GMT+0900 (대한민국 표준시)
~~~

# [2.4. parseFloat()](https://poiemaweb.com/js-global-object#24-parsefloat)
매개변수에 전달된 문자열을 부동소수점 숫자(floating point number)로 변환하여 반환한다.

~~~
parseFloat(string)
// string: 변환 대상 문자열
~~~

<b>문자열의 첫 숫자만 반환되며 전후 공백은 무시된다.</b> 그리고 첫문자를 숫자로 변환할 수 없다면 NaN을 반환한다.

~~~
parseFloat('3.14');     // 3.14
parseFloat('10.00');    // 10
parseFloat('34 45 66'); // 34
parseFloat(' 60 ');     // 60
parseFloat('40 years'); // 40
parseFloat('He was 40') // NaN
~~~

# [2.5. parseInt()](https://poiemaweb.com/js-global-object#25-parseint)
<b>매개변수에 전달된 문자열을 정수형 숫자(Integer)로 해석(parsing)하여 반환한다. 반환값은 언제나 10진수이다.</b>

~~~
parseInt(string, radix);
// string: 파싱 대상 문자열
// radix: 진법을 나타내는 기수(2 ~ 36, 기본값 10)
~~~

첫번째 매개변수에 전달된 값이 `문자열이 아니면 문자열로 변환한 후 숫자로 해석`하여 반환한다.

~~~
parseInt(10);     // 10
parseInt(10.123); // 10
~~~

<b>2번째 매개변수에는 진법을 나타내는 기수(2 ~ 36)를 지정할 수 있다. 기수를 생략하면 첫번째 매개변수에 전달된 문자열을 10진수로 해석하여 반환한다.</b>

~~~
parseInt('10');     // 10
parseInt('10.123'); // 10
~~~

<b>두번째 매개변수에 진법을 나타내는 기수를 지정하면 첫번째 매개변수에 전달된 문자열을 해당 기수의 숫자로 해석하여 반환한다. 이때 반환값은 언제나 10진수이다.</b>

parseInt('10', 2);  // 2진수 10 → 10진수 2
parseInt('10', 8);  // 8진수 10 → 10진수 8
parseInt('10', 16); // 16진수 10 → 10진수 16

> **Note**: 기수를 지정하여 10진수 숫자를 해당 기수의 문자열로 변환하여 반환하고 싶을 때는 [Number.prototype.toString 메소드](https://poiemaweb.com/js-number#38-numberprototypetostringradix-number-string-es1)를 사용한다.

두번째 매개변수에 진법을 나타내는 기수를 지정하지 않더라도 첫번째 매개변수에 전달된 문자열이 “0x” 또는 “0X”로 시작한다면 16진수로 해석하여 반환한다.

~~~
parseInt('0x10'); // 16진수 10 → 10진수 16 
~~~

두번째 매개변수에 진법을 나타내는 기수를 지정하지 않더라도 첫번째 매개변수에 전달된 문자열이 “0”로 시작한다면 8진수로 해석하지 않고 10진수로 해석한다.

> **Note**: ES5 이전까지는 비록 사용을 금지하고는 있었지만 “0”로 시작하는 숫자를 8진수로 해석하였다. ES6부터는 “0”로 시작하는 숫자를 8진수로 해석하지 않고 10진수로 해석한다.

<b>따라서 문자열을 8진수로 해석하려면 지수를 반드시 지정하여야 한다.</b>

~~~
parseInt('010'); // 8진수 10으로 인식하지 않는다.
parseInt('010', 8); // 8진수 10 → 10진수 8
parseInt('10', 8); // 8진수 10 → 10진수 8
~~~

parseInt는 첫번째 매개변수에 전달된 문자열의 첫번째 문자가 해당 지수의 숫자로 변환될 수 없다면 NaN을 반환한다.

~~~
parseInt('A0'));   // NaN
parseInt('20', 2); // NaN
~~~

하지만 첫번째 매개변수에 전달된 문자열의 두번째 문자부터 해당 진수를 나타내는 숫자가 아닌 문자(예를 들어 2진수의 경우, 2)와 마주치면 이 문자와 계속되는 문자들은 전부 무시되며 해석된 정수값만을 반환한다.

~~~
parseInt('1A0'));    // 1
parseInt('102', 2)); // 2
parseInt('58', 8);   // 5
parseInt('FG', 16);  // 15
~~~

첫번째 매개변수에 전달된 문자열에 공백이 있다면 첫번째 문자열만 해석하여 반환하며 전후 공백은 무시된다. <b>만일 첫번째 문자열을 숫자로 파싱할 수 없는 경우, NaN을 반환한다.</b>

~~~
parseInt('34 45 66'); // 34
parseInt(' 60 ');     // 60
parseInt('40 years'); // 40
parseInt('He was 40') // NaN
~~~

# [2.6. encodeURI() / decodeURI()](https://poiemaweb.com/js-global-object#26-encodeuri--decodeuri)
encodeURI()은 매개변수로 전달된 URI(Uniform Resource Identifier)를 인코딩한다.

![image](https://user-images.githubusercontent.com/44339530/132607435-7943a7f8-6dad-4e04-8fc7-140e3e6243ad.png)

여기서 <b>인코딩이란 URI의 문자들을 이스케이프 처리하는 것을 의미한다.</b>

`이스케이프 처리`
네트워크를 통해 정보를 공유할 때 어떤 시스템에서도 읽을 수 있는 [ASCII Character-set](https://en.wikipedia.org/wiki/ASCII)로 변환하는 것이다. UTF-8 특수문자의 경우, 1문자당 1~3byte, UTF-8 한글 표현의 경우, 1문자당 3btye이다. 예를 들어 특수문자 공백(space)은 %20, 한글 ‘가’는 %EC%9E%90으로 인코딩된다.

`이스케이프 처리 이유`
URI 문법 형식 표준 [RFC3986](https://www.ietf.org/rfc/rfc3986.txt)에 따르면 URL은 ASCII Character-set으로만 구성되어야 하며 한글을 포함한 대부분의 외국어나 ASCII에 정의되지 않은 특수문자의 경우 URL에 포함될 수 없다. 따라서 URL 내에서 의미를 갖고 있는 문자(%, ?, #)나 URL에 올 수 없는 문자(한글, 공백 등) 또는 시스템에 의해 해석될 수 있는 문자(<, >)를 이스케이프 처리하여 야기될 수 있는 문제를 예방하기 위함이다.

단 아래의 문자는 이스케이프 처리에서 제외된다.
- 알파벳, 0~9의 숫자, - _ . ! ~ * ‘ ( )

decodeURI()은 매개변수로 전달된 URI을 디코딩한다.

~~~
encodeURI(URI)
// URI: 완전한 URI

decodeURI(encodedURI)
// encodedURI: 인코딩된 완전한 URI
~~~

~~~
var uri = 'http://example.com?name=이웅모&job=programmer&teacher';
var enc = encodeURI(uri);
var dec = decodeURI(enc);
console.log(enc);
// http://example.com?name=%EC%9D%B4%EC%9B%85%EB%AA%A8&job=programmer&teacher
console.log(dec);
// http://example.com?name=이웅모&job=programmer&teacher
~~~

# [2.7. encodeURIComponent() / decodeURIComponent()](https://poiemaweb.com/js-global-object#27-encodeuricomponent--decodeuricomponent)
<b>encodeURIComponent()는 인수를 쿼리스트링의 일부라고 간주한다. 따라서 =, ?, &를 인코딩한다. 반면 encodeURI()는 인수를 URI 전체라고 간주하며 파라미터 구분자인 =, ?, &를 인코딩하지 않는다.</b>

~~~
encodeURIComponent(URI)
// URI: URI component(구성 요소)

decodeURIComponent(encodedURI)
// encodedURI: 인코딩된 URI component(구성 요소)
~~~

~~~
var uriComp = '이웅모&job=programmer&teacher';

// encodeURI / decodeURI
var enc = encodeURI(uriComp);
var dec = decodeURI(enc);
console.log(enc);
// %EC%9D%B4%EC%9B%85%EB%AA%A8&job=programmer&teacher
console.log(dec);
// 이웅모&job=programmer&teacher

// encodeURIComponent / decodeURIComponent
enc = encodeURIComponent(uriComp);
dec = decodeURIComponent(enc);
console.log(enc);
// %EC%9D%B4%EC%9B%85%EB%AA%A8%26job%3Dprogrammer%26teacher
console.log(dec);
// 이웅모&job=programmer&teacher
~~~

#### 출처
- [https://poiemaweb.com/js-global-object](https://poiemaweb.com/js-global-object)