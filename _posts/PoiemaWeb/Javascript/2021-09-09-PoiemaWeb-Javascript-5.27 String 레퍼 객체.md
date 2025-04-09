---
title: "[PoiemaWeb] 5.27 String 레퍼 객체"
date: 2021-09-09 +0800
categories: [Frontend, PoiemaWeb, Javascript]
tags: [poiemaweb, javascript, es6]
toc: true
comments: true
---

String 객체는 원시 타입인 문자열을 다룰 때 유용한 프로퍼티와 메소드를 제공하는 레퍼(wrapper) 객체이다. 변수 또는 객체 프로퍼티가 문자열을 값으로 가지고 있다면 String 객체의 별도 생성없이 String 객체의 프로퍼티와 메소드를 사용할 수 있다.

원시 타입이 wrapper 객체의 메소드를 사용할 수 있는 이유는 원시 타입으로 프로퍼티나 메소드를 호출할 때 원시 타입과 연관된 wrapper 객체로 일시적으로 변환되어 프로토타입 객체를 공유하게 되기 때문이다.

~~~
const str = 'Hello world!';
console.log(str.toUpperCase()); // 'HELLO WORLD!'
~~~

위에서 원시 타입 문자열을 담고 있는 변수 str이 String.prototype.toUpperCase() 메소드를 호출할 수 있는 것은 변수 str의 값이 일시적으로 wrapper객체로 변환되었기 때문이다.

사용 빈도가 높은 String 객체의 프로퍼티와 메소드에 대해 살펴보도록 하자.

# [1. String Constructor](https://poiemaweb.com/js-string#1-string-constructor)
String 객체는 String 생성자 함수를 통해 생성할 수 있다. 이때 전달된 인자는 모두 문자열로 변환된다.

~~~
new String(value);
~~~

~~~
let strObj = new String('Lee');
console.log(strObj); // String {0: 'L', 1: 'e', 2: 'e', length: 3, [[PrimitiveValue]]: 'Lee'}

strObj = new String(1);
console.log(strObj); // String {0: '1', length: 1, [[PrimitiveValue]]: '1'}

strObj = new String(undefined);
console.log(strObj); // String {0: 'u', 1: 'n', 2: 'd', 3: 'e', 4: 'f', 5: 'i', 6: 'n', 7: 'e', 8: 'd', length: 9, [[PrimitiveValue]]: 'undefined'}
~~~

new 연산자를 사용하지 않고 String 생성자 함수를 호출하면 String 객체가 아닌 문자열 리터럴을 반환한다. 이때 형 변환이 발생할 수 있다.

~~~
var x = String('Lee');

console.log(typeof x, x); // string Lee
~~~

일반적으로 문자열을 사용할 때는 원시 타입 문자열을 사용한다.

~~~
const str = 'Lee';
const strObj = new String('Lee');

console.log(str == strObj);  // true
console.log(str === strObj); // false

console.log(typeof str);    // string
console.log(typeof strObj); // object
~~~

# [2. String Property](https://poiemaweb.com/js-string#3-string-method)
String 객체의 모든 메소드는 언제나 새로운 문자열을 반환한다. 문자열은 변경 불가능한(immutable)한 원시 값이기 때문이다.

# [2.1 String.length](https://poiemaweb.com/js-string#21-stringlength)
문자열 내의 문자 갯수를 반환한다. String 객체는 length 프로퍼티를 소유하고 있으므로 유사 배열 객체이다.

~~~
const str1 = 'Hello';
console.log(str1.length); // 5

const str2 = '안녕하세요!';
console.log(str2.length); // 6
~~~

# [3.1 String.prototype.charAt(pos: number): string ES1]

# [3. String Method](https://poiemaweb.com/js-string#3-string-method)
String 객체의 모든 메소드는 언제나 새로운 문자열을 반환한다. 문자열은 변경 불가능(immutable)한 원시 값이기 때문이다.

- [3.1 String.prototype.charAt(pos: number): string ES1](https://poiemaweb.com/js-string#31-stringprototypecharatpos-number-string-es1)
- [3.2 String.prototype.concat(…strings: string[]): string ES3](https://poiemaweb.com/js-string#32-stringprototypeconcatstrings-string-string-es3)
- [3.3 String.prototype.indexOf(searchString: string, fromIndex=0): number ES1](https://poiemaweb.com/js-string#33-stringprototypeindexofsearchstring-string-fromindex0-number-es1)
- [3.4 String.prototype.lastIndexOf(searchString: string, fromIndex=this.length-1): number ES1](https://poiemaweb.com/js-string#34-stringprototypelastindexofsearchstring-string-fromindexthislength-1-number-es1)
- [3.5 String.prototype.replace(searchValue: string | RegExp, replaceValue: string | replacer: (substring: string, …args: any[]) => string): string): string ES3](https://poiemaweb.com/js-string#35-stringprototypereplacesearchvalue-string--regexp-replacevalue-string--replacer-substring-string-args-any--string-string-string-es3)
- [3.6 String.prototype.split(separator: string | RegExp, limit?: number): string[] ES3](https://poiemaweb.com/js-string#36-stringprototypesplitseparator-string--regexp-limit-number-string-es3)
- [3.7 String.prototype.substring(start: number, end=this.length): string ES3](https://poiemaweb.com/js-string#37-stringprototypesubstringstart-number-endthislength-string-es3)
- [3.8 String.prototype.slice(start?: number, end?: number): string ES3](https://poiemaweb.com/js-string#38-stringprototypeslicestart-number-end-number-string-es3)
- [3.9 String.prototype.toLowerCase(): string ES1](https://poiemaweb.com/js-string#39-stringprototypetolowercase-string-es1)
- [3.10 String.prototype.toUpperCase(): string ES1](https://poiemaweb.com/js-string#310-stringprototypetouppercase-string-es1)
- [3.11 String.prototype.trim(): string ES5](https://poiemaweb.com/js-string#311-stringprototypetrim-string-es5)
- [3.12 String.prototype.repeat(count: number): string ES6](https://poiemaweb.com/js-string#312-stringprototyperepeatcount-number-string-es6)
- [3.13 String​.prototype​.includes(searchString: string, position?: number): boolean ES6](https://poiemaweb.com/js-string#312-stringprototyperepeatcount-number-string-es6)

#### 출처
- [https://poiemaweb.com/js-string](https://poiemaweb.com/js-string)