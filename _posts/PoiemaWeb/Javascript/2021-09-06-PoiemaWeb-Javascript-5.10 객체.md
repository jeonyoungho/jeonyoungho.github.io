---
title: "[PoiemaWeb] 5.10 객체(PoiemaWeb-Javascript)"
date: 2021-09-06 18:20:00 +0800
categories: [Frontend, PoiemaWeb, Javascript]
tags: [poiemaweb, javascript, es6]
toc: true
comments: true
---

# [2. 객체 생성 방법](https://poiemaweb.com/js-object#2-%EA%B0%9D%EC%B2%B4-%EC%83%9D%EC%84%B1-%EB%B0%A9%EB%B2%95)
자바와 같은 클래스 기반 객체 지향 언어는 클래스를 사전에 정의하고 필요한 시점에 new 연산자를 이용하여 인스턴스를 생성하는 방식으로 객체를 생성한다. 하지만 자바스크립트는 프로토타입 객체 지향 언어로서 클래스라는 개념이 없고 별도의 객체 생성 방법이 존재한다.

> **Note**: ECMAScript 6에서 새롭게 [클래스](https://poiemaweb.com/es6-class)가 도입되었다. 프로토타입 기반 프로그래밍은 클래스가 존재하지 않는 객체지향 프로그래밍 스타일이다. 클래스없이 프로토타입 체인과 클로저 등으로 객체 지향 언어의 상속, 캡슐화(정보 은닉) 등의 개념을 구현한다. 하지만 클래스 기반 언어에 익숙한 프로그래머들은 혼란을 일으킬 수 있으며 자바스크립트를 어렵게 느끼게하는 하나의 장벽처럼 인식되었다. ES6의 클래스는 기존 프로토타입 기반 객체지향 프로그래밍보다 클래스 기반 언어에 익숙한 프로그래머가 보다 빠르게 학습할 수 있는 단순하고 깨끗한 새로운 문법을 제시하고 있다. ES6의 클래스가 새로운 객체지향 모델을 제공하는 것이 아니며 클래스도 사실 함수이고 기존 프로토타입 기반 패턴의 문법적 설탕([Syntactic sugar](https://en.wikipedia.org/wiki/Syntactic_sugar))이다.

# [2.3 생성자 함수](https://poiemaweb.com/js-object#23-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)
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

# [3.6 for-in 문](https://poiemaweb.com/js-object#36-for-in-%EB%AC%B8)
for-in 문은 객체의 문자열 키(key)를 순회하기 위한 문법이다. 배열에는 사용하지 않는 것이 좋다. 이유는 아래와 같다.
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

# [4.Pass-by-reference, 5. Pass-by-value](https://poiemaweb.com/js-object#4-pass-by-reference)
자바스크립의 함수 호출 시 인자 전달 방식은 자바와 동일하다고 생각하면 된다. (call by value)
- primitive타입의 경우에는 메모리에 있는 값을 복사하여 전달하기에 원본 값은 영향을 받지 않는다.
- 객체(참조)타입의 경우에는 메모리의 주소 값을 가지고 있는 레퍼런스를 복사하여 전달하기에 원본에 영향을 받게 된다.

##  단순 복사, 얕은 복사(shallow copy), 깊은 복사(deep copy)
단순 복사: 완전히 동일한 객체(mutable한 경우에는 pass-by-reference로 값이 수정될 수 있지만, immutable한 경우에는 pass-by-value로 값이 수정되는 것이 아닌 아예 새로운 객체로 변경됨)

얕은 복사: 복합객체(껍데기)를 새롭게 생성하지만, 하지만 객체 내부의 객체는 동일한 객체!(원본 손상 가능성 있음)

깊은 복사: 복합객체(껍데기)를 새롭게 생성하고 그 안의 내용까지 재귀적으로 새롭게 생성

<b>자세한 내용은 [여기](https://blueshw.github.io/2016/01/20/shallow-copy-deep-copy/)를 참고하자.</b>

#### 출처
- [https://poiemaweb.com/js-object](https://poiemaweb.com/js-object)