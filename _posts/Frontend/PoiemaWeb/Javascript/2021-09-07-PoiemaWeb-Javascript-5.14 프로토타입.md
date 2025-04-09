---
title: "[PoiemaWeb] 5.14 프로토타입(PoiemaWeb-Javascript)"
date: 2021-09-07 +0800
categories: [Frontend, PoiemaWeb, Javascript]
tags: [poiemaweb, javascript, es6]
toc: true
comments: true
---

# [1. 프로토타입 객체](https://poiemaweb.com/js-prototype#1-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-%EA%B0%9D%EC%B2%B4)
자바스크립트의 모든 객체는 자신의 부모 역할을 담당하는 객체와 연결되어 있다. 그리고 이것은 마치 객체 지향의 상속 개념과 같이 부모 객체의 프로퍼티 또는 메소드를 상속받아 사용할 수 있게 한다. 이러한 부모 객체를 `Prototype(프로토타입) 객체` 또는 줄여서 Prototype(프로토타입)이라 한다.

객체를 생성할 때 프로토타입은 결정된다. 결정된 프로토타입 객체는 다른 임의의 객체로 변경할 수 있다. 이것은 부모 객체인 프로토타입을 동적으로 변경할 수 있다는 것을 의미한다. 이러한 특징을 활용하여 객체의 상속을 구현할 수 있다.

# [3. constructor 프로퍼티](https://poiemaweb.com/js-prototype#3-constructor-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0)
프로토타입 객체는 constructor 프로퍼티를 갖는다. 이 constructor 프로퍼티는 객체의 입장에서 자신을 생성한 객체를 가리킨다.

예를 들어 Person()생성자 함수에 의해 생성된 객체를 foo라 하자. 이 foo 객체를 생성한 객체는 Person() 생성자 함수이다. 이때 foo 객체 입장에서 자신을 생성한 객체는 Person() 생성자 함수이며, foo객체의 프로토타입 객체는 Person.protytype이다. 따라서 프로토타입 객체 Person.protytype의 constructor프로퍼티는 Person()생성자 함수를 가리킨다.

# [4. Prototype chain](https://poiemaweb.com/js-prototype#4-prototype-chain)
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

# [4.2 생성자 함수로 생성된 객체의 프로토타입 체인](https://poiemaweb.com/js-prototype#42-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98%EB%A1%9C-%EC%83%9D%EC%84%B1%EB%90%9C-%EA%B0%9D%EC%B2%B4%EC%9D%98-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-%EC%B2%B4%EC%9D%B8)

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

# [5. 프로토타입 객체의 확장](https://poiemaweb.com/js-prototype#5-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-%EA%B0%9D%EC%B2%B4%EC%9D%98-%ED%99%95%EC%9E%A5)

프로토타입 객체도 객체이므로 일반 객체와 같이 프로퍼티를 추가/삭제할 수 있다. 그리고 이렇게 추가/삭제된 프로퍼티는 즉시 프로토타입 체인에 반영된다.

~~~
function Person(name) {
  this.name = name;
}

var foo = new Person('Lee');

Person.prototype.sayHello = function(){
  console.log('Hi! my name is ' + this.name);
};

foo.sayHello();
~~~

# [6. 원시 타입(Primitive data type)의 확장](https://poiemaweb.com/js-prototype#6-%EC%9B%90%EC%8B%9C-%ED%83%80%EC%9E%85primitive-data-type%EC%9D%98-%ED%99%95%EC%9E%A5)
자바스크립트에서 원시 타입(숫자, 문자열, boolean, null, undefined)을 제외한 모든 것은 객체이다. 그런데 아래 예제를 살펴보면 원시 타입인 문자열이 객체와 유사하게 동작한다.

~~~
var str = 'test';
console.log(typeof str);                 // string
console.log(str.constructor === String); // true
console.dir(str);                        // test

var strObj = new String('test');
console.log(typeof strObj);                 // object
console.log(strObj.constructor === String); // true
console.dir(strObj);
// {0: "t", 1: "e", 2: "s", 3: "t", length: 4, __proto__: String, [[PrimitiveValue]]: "test" }

console.log(str.toUpperCase());    // TEST
console.log(strObj.toUpperCase()); // TEST
~~~

원시 타입 문자열과 String() 생성자 함수로 생성한 문자열 객체의 타입은 분명히 다르다. 원시 타입은 객체가 아니므로 프로퍼티나 메소드를 가질 수 없다. 하지만 <b>원시 타입으로 프로퍼티나 메소드를 호출할 때 원시 타입과 연관된 객체로 일시적으로 변환되어 프로토타입 객체를 공유하게 된다.</b>

원시 타입은 객체가 아니므로 프로퍼티나 메소드를 직접 추가할 수 없다.

~~~
var str = 'test';

// 에러가 발생하지 않는다.
str.myMethod = function() {
  console.log('str.myMethod');
};

str.myMethod(); // Uncaught TypeError: str.myMethod is not a function
~~~

하지만 String 객체의 프로토타입 객체 String.prototype에 메소드를 추가하면 원시 타입, 객체 모두 메소드를 사용할 수 있다.

~~~
var str = 'test';
String.prototype.meMethod = function() {
  return 'myMethod';
};

console.log(str.myMethod()); // myMethod
console.log('string'.myMethod()); // myMethod
console.dir(String.protytype);
~~~

앞서 살펴본 바와 같이 모든 객체는 프로토타입 체인에 의해 Object.protytype 객체의 메소드를 사용할 수 있었다. Object.prototype 객체는 프로토타입 체인의 종점으로 모든 객체가 사용할 수 있는 메소드를 갖는다.

이후 살펴보게 될 [Built-in object(내장 객체)](https://poiemaweb.com/js-built-in-object)의 Global objects인 String, Number, Array 객체 등이 가지고 있는 표준 메소드는 프로토타입 객체인 String.prototype, Number.prototype, Array.prototype 등에 정의되어 있다. 이들 프로토타입 객체 또한 Object.prototype를 프로토타입 체인에 의해 자신의 프로토타입 객체로 연결한다.

<b>자바스크립트는 표준 내장 객체의 프로토타입 객체에 개발자가 정의한 메소드의 추가를 허용한다.</b>

~~~
var str = 'test';

String.prototype.myMethod = function() {
  return 'myMethod';
}

console.log(str.myMethod());
console.dir(String.prototype);

console.log(str.__proto__ === String.prototype);                 // ① true
console.log(String.prototype.__proto__  === Object.prototype);   // ② true
console.log(String.prototype.constructor === String);            // ③ true
console.log(String.__proto__ === Function.prototype);            // ④ true
console.log(Function.prototype.__proto__  === Object.prototype); // ⑤ true
~~~

![image](https://user-images.githubusercontent.com/44339530/132291780-e5f2873b-5454-480c-87a3-c51a3e5d2f7a.png)

# [7. 프로토타입 객체의 변경](https://poiemaweb.com/js-prototype#7-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-%EA%B0%9D%EC%B2%B4%EC%9D%98-%EB%B3%80%EA%B2%BD)
객체를 생성할 때 프로토타입은 결정된다. 결정된 프로토타입 객체는 다른 임의의 객체로 변경할 수 있다. 이것은 부모 객체인 프로토타입을 동적으로 변경할 수 있다는 것을 의미한다. 이러한 특징을 활용하여 객체의 상속을 구현할 수 있다.

이때 주의할 것은 프로토타입 객체를 변경하면
- 프로토타입 객체 변경 시점 이전에 생성된 객체
  - 기존 프로토타입 객체를 [[Prototype]]에 바인딩한다.
- 프로토타입 객체 변경 시점 이후에 생성된 객체
  - 변경된 프로토타입 객체를 [[Prototype]]에 바인딩한다.

~~~
function Person(name) {
  this.name = name;
}

var foo = new Person('Lee');

// 프로토타입 객체의 변경
Person.prototype = { gender: 'male' };

var bar = new Person('Kim');

console.log(foo.gender); // undefined
console.log(bar.gender); // 'male'

console.log(foo.constructor); // ① Person(name)
console.log(bar.constructor); // ② Object()
~~~

![image](https://user-images.githubusercontent.com/44339530/132303016-eeb2ee3b-7db3-482e-a306-62eb892c476e.png)

① constructor 프로퍼티는 Person() 생성자 함수를 가리킨다.

② 프로토타입 객체 변경 후, Person() 생성자 함수의 Prototype 프로퍼티가 가리키는 프로토타입 객체를 일반 객체로 변경하면서 Person.prototype.constructor 프로퍼티도 삭제되었다. 따라서 프로토타입 체인에 의해 bar.constructor의 값은 프로토타입 체이닝에 의해 Object.prototype.constructor 즉 Object() 생성자 함수가 된다.

# [8. 프로토타입 체인 동작 조건](https://poiemaweb.com/js-prototype#8-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-%EC%B2%B4%EC%9D%B8-%EB%8F%99%EC%9E%91-%EC%A1%B0%EA%B1%B4)
객체의 프로퍼티를 참조하는 경우, 해당 객체에 프로퍼티가 없는 경우, 프로토타입 체인이 동작한다.

객체의 프로퍼티에 값을 할당하는 경우, 프로토타입 체인이 당작하지 않는다. 이는 개체에 해당 프로퍼티가 있는 경우, 값을 재할당하고 해당 프로퍼티가 없는 경우는 해당 객체에 프로퍼티를 동적으로 추가하기 때문이다.

~~~
function Person(name) {
  this.name = name;
}

Person.prototype.gender = 'male'; // ①

var foo = new Person('Lee');
var bar = new Person('Kim');

console.log(foo.gender); // ① 'male'
console.log(bar.gender); // ① 'male'

// 1. foo 객체에 gender 프로퍼티가 없으면 프로퍼티 동적 추가
// 2. foo 객체에 gender 프로퍼티가 있으면 해당 프로퍼티에 값 할당
foo.gender = 'female';   // ②

console.log(foo.gender); // ② 'female'
console.log(bar.gender); // ① 'male'
~~~

![image](https://user-images.githubusercontent.com/44339530/132303661-9a8dff15-862e-40be-97ec-f7356d60f0dd.png)

foo 객체의 gender 프로퍼티에 값을 할당하면 프로토타입 체인이 발생하여 Person.prototype 객체의 gender 프로퍼티에 값을 할당하는 것이 아니라 foo 객체에 프로퍼티를 동적으로 추가한다.

#### 출처
- [https://poiemaweb.com/js-prototype](https://poiemaweb.com/js-prototype)