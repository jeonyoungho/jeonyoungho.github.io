---
title: "[개발자 블로그] 5.20 객체 지향 프로래밍(PoiemaWeb-Javascript)" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-09-08 +0800
categories: [PoiemaWeb, Javascript] # categories는 최대 2개까지 가능
tags: [poiemaweb, javascript, es6] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# [2.2 프로토타입 기반 언어](https://poiemaweb.com/js-object-oriented-programming#22-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-%EA%B8%B0%EB%B0%98-%EC%96%B8%EC%96%B4)

객체 지향의 상속, 캡슐화(정보 은닉) 등의 개념은 프로토타입 체인과 클로저 등으로 구현할 수 있다.
- 상속 -> 프로토타입 체인
- 캡슐화(정보 은닉) -> 클로저

# [4. 프로토타입 체인과 메소드의 정의](https://poiemaweb.com/js-object-oriented-programming#4-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-%EC%B2%B4%EC%9D%B8%EA%B3%BC-%EB%A9%94%EC%86%8C%EB%93%9C%EC%9D%98-%EC%A0%95%EC%9D%98)

<b>프로토타입 객체는 상속할 것들이 저장되는 장소이다.</b>

아래는 더글라스 크락포드가 제안한 프로토타입 객체에 메소드를 추가하는 방식이다.

~~~
/**
 * 모든 생성자 함수의 프로토타입은 Function.prototype이다. 따라서 모든 생성자 함수는 Function.prototype.method()에 접근할 수 있다.
 * @method Function.prototype.method
 * @param ({string}) (name) - (메소드 이름)
 * @param ({function}) (func) - (추가할 메소드 본체)
 */
Function.prototype.method = function (name, func) {
  // 생성자함수의 프로토타입에 동일한 이름의 메소드가 없으면 생성자함수의 프로토타입에 메소드를 추가
  // this: 생성자함수
  if (!this.prototype[name]) {
    this.prototype[name] = func;
  }
};

/**
 * 생성자 함수
 */
function Person(name) {
  this.name = name;
}

/**
 * 생성자함수 Person의 프로토타입에 메소드 setName을 추가
 */
Person.method('setName', function (name) {
  this.name = name;
});

/**
 * 생성자함수 Person의 프로토타입에 메소드 getName을 추가
 */
Person.method('getName', function () {
  return this.name;
});

var me  = new Person('Lee');
var you = new Person('Kim');
var him = new Person('choi');

console.log(Person.prototype);
// Person { setName: [Function], getName: [Function] }

console.log(me);  // Person { name: 'Lee' }
console.log(you); // Person { name: 'Kim' }
console.log(him); // Person { name: 'choi' }
~~~


# [5. 상속 (Inheritance)](https://poiemaweb.com/js-object-oriented-programming#5-%EC%83%81%EC%86%8D-inheritance)
클래스 기반 언어에서 객체는 클래스의 인스턴스이며 클래스는 다른 클래스로 상속될 수 있다. 자바스크립트는 기본적으로 프로토타입을 통해 상속을 구현한다. 이것은 프로토타입을 통해 객체가 다른 객체로 직접 상속된다는 의미이다. 이러한 점이 자바스크립트의 약점으로 여겨지기도 하지만 프로토타입 상속 모델은 사실 클래스 기반보다 강력한 방법이다.

자바스크립트의 상속 구현 방식은 크게 두 가지로 구분할 수 있다. 하나는 `클래스 기반 언어의 상속 방식을 흉내 내는 것(의사 클래스 패턴 상속. Pseudo-classical Inheritance)`이고, 두번째는 `프로토타입으로 상속을 구현하는 것(프로토타입 패턴 상속. Prototypal Inheritance)`이다.

# [5.1 의사 클래스 패턴 상속 (Pseudo-classical Inheritance)](https://poiemaweb.com/js-object-oriented-programming#51-%EC%9D%98%EC%82%AC-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%8C%A8%ED%84%B4-%EC%83%81%EC%86%8D-pseudo-classical-inheritance)
의사 클래스 패턴은 자식 생성자 함수의 prototype 프로퍼티를 부모 생성자 함수의 인스턴스로 교체하여 상속을 구현하는 방법이다. 부모와 자식 모두 생성자 함수를 정의하여야 한다.

~~~
// 부모 생성자 함수
var Parent = (function () {
  // Constructor
  function Parent(name) {
    this.name = name;
  }

  // method
  Parent.prototype.sayHi = function () {
    console.log('Hi! ' + this.name);
  };

  // return constructor
  return Parent;
}());

// 자식 생성자 함수
var Child = (function () {
  // Constructor
  function Child(name) {
    this.name = name;
  }

  // 자식 생성자 함수의 프로토타입 객체를 부모 생성자 함수의 인스턴스로 교체.
  Child.prototype = new Parent(); // ②

  // 메소드 오버라이드
  Child.prototype.sayHi = function () {
    console.log('안녕하세요! ' + this.name);
  };

  // sayBye 메소드는 Parent 생성자함수의 인스턴스에 위치된다
  Child.prototype.sayBye = function () {
    console.log('안녕히가세요! ' + this.name);
  };

  // return constructor
  return Child;
}());

var child = new Child('child'); // ①
console.log(child);  // Parent { name: 'child' }

console.log(Child.prototype); // Parent { name: undefined, sayHi: [Function], sayBye: [Function] }

child.sayHi();  // 안녕하세요! child
child.sayBye(); // 안녕히가세요! child

console.log(child instanceof Parent); // true
console.log(child instanceof Child);  // true
~~~

![image](https://user-images.githubusercontent.com/44339530/132473837-6d0ba3de-1713-476b-8acc-a39299ea9677.png)

의사 클래스 패턴은 클래스 기반 언어의 상속을 흉내내어 상속을 구현하였다. 구동 상에 문제는 없지만 의사 클래스 패턴은 아래와 같은 문제를 가지고 있다.

<b>1) new 연산자를 통해 인스턴스를 생성한다.</b>

이는 자바스크립트의 프로토타입 본질에 모순되는 것이다. 프로토타입 본성에 맞게 객체에서 다른 객체로 직접 상속하는 방법을 갖는 대신 생성자 함수와 new 연산자를 통해 객체를 생성하는 불필요한 간접적인 단계가 있다. 클래스와 비슷하게 보이는 일부 복잡한 구문은 프로토타입 메커니즘을 명확히 나타내지 못하게 한다.

게다가 생성자 함수의 사용에는 심각한 위험이 존재한다. 만약 생성자 함수를 호출할 때 new 연산자를 포함하는 것을 잊게 되면 this는 새로운 객체와 바인딩되지 않고 전역객체에 바인딩된다. (new 연산자와 함께 호출된 생성자 함수 내부의 this는 새로 생성된 객체를 참조한다.)

이런 문제점을 경감시키기 위해 파스칼 표시법(첫글자를 대문자 표기)으로 생성자 함수 이름을 표기하는 방법을 사용하지만, 더 나은 대안은 new 연산자의 사용을 피하는 것이다.

<b>2) 생성자 링크의 파괴</b>

위 그림을 보면 child 객체의 프로토타입 객체는 Parent 생성자 함수가 생성한 new Parent() 객체이다. 프로토타입 객체는 내부 프로퍼티로 constructor를 가지며 이는 생성자 함수를 가리킨다. 하지만 의사 클래스 패턴 상속은 프로토타입 객체를 인스턴스로 교체하는 과정에서 constructor의 연결이 깨지게 된다. 즉, child 객체를 생성한 것은 Child 생성자 함수이지만 child.constructor의 출력 결과는 Child 생성자 함수가 아닌 Parent 생성자 함수를 나타낸다. 이는 child 객체의 프로토타입 객체인 new Parent() 객체는 constructor가 없기 때문에 프로토타입 체인에 의해 Parent.prototype의 constructor를 참조했기 때문이다.

~~~
console.log(child.constructor);  // [Function: Parent]
~~~

<b>3) 객체리터럴</b>

의사 클래스 패턴 상속은 기본적으로 생성자 함수를 사용하기 때문에 객체리터럴 패턴으로 생성한 객체의 상속에는 적합하지 않다. 이는 객체리터럴 패턴으로 생성한 객체의 생성자 함수는 Object()이고 이를 변경할 방법이 없기 때문이다.

~~~
var o = {};
console.log(o.__proto__ === Object.prototype); // true
~~~

# [5.2 프로토타입 패턴 상속 (Prototypal Inheritance)](https://poiemaweb.com/js-object-oriented-programming#52-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-%ED%8C%A8%ED%84%B4-%EC%83%81%EC%86%8D-prototypal-inheritance)
프로토타입 패턴 상속은 [Object.create 함수](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/create)를 사용하여 객체에서 다른 객체로 직접 상속을 구현하는 방식이다. 프로토타입 패턴 상속은 개념적으로 의사 클래스 패턴 상속보다 더 간단하다. 또한 의사 클래스 패턴의 단점인 new 연산자가 필요없으며, 생성자 링크도 파괴되지 않으며, 객체리터럴에도 사용할 수 있다.

생성자 함수를 사용한 프로토타입 패턴 상속은 아래와 같다.

~~~
// 부모 생성자 함수
var Parent = (function () {
  // Constructor
  function Parent(name) {
    this.name = name;
  }

  // method
  Parent.prototype.sayHi = function () {
    console.log('Hi! ' + this.name);
  };

  // return constructor
  return Parent;
}());

// create 함수의 인수는 프로토타입이다.
var child = Object.create(Parent.prototype);
child.name = 'child';

child.sayHi();  // Hi! child

console.log(child instanceof Parent); // true
~~~

![image](https://user-images.githubusercontent.com/44339530/132473856-fc3f3a73-b854-4ec0-b836-adc7674a217c.png)

<b>객체리터럴 패턴으로 생성한 객체에도 프로토타입 패턴 상속을 사용할 수 있다.</b>

~~~
var parent = {
  name: 'parent',
  sayHi: function() {
    console.log('Hi! ' + this.name);
  }
};

// create 함수의 인자는 객체이다.
var child = Object.create(parent);
child.name = 'child';

// var child = Object.create(parent, {name: {value: 'child'}});

parent.sayHi(); // Hi! parent
child.sayHi();  // Hi! child

console.log(parent.isPrototypeOf(child)); // true
~~~

![image](https://user-images.githubusercontent.com/44339530/132474307-0a5b9416-b8e7-46e4-bcb6-ea7fc1aaa0d2.png)

<b>Object.create 함수는 매개변수에 프로토타입으로 설정할 객체 또는 인스턴스를 전달하고 이를 상속하는 새로운 객체를 생성한다.</b> Object.create 함수는 표준에 비교적 늦게 추가되어 IE9 이상에서 정상적으로 동작한다. 따라서 [크로스 브라우징](https://blog.naver.com/insaweb/221926915225)에 주의하여야 한다. Object.create 함수의 폴리필(Polyfill: 특정 기능이 지원되지 않는 브라우저를 위해 사용할 수 있는 코드 조각이나 플러그인)을 살펴보면 상속의 핵심을 이해할 수 있다.

~~~
// Object.create 함수의 폴리필
if (!Object.create) {
  Object.create = function (o) {
    function F() {}  // 1
    F.prototype = o; // 2
    return new F();  // 3
  };
}
~~~

위 폴리필은 프로토타입 패턴 상속의 핵심을 담고 있다.

1) 비어있는 생성자 함수 F를 생성한다.
2) 생성자 함수 F의 prototype 프로퍼티에 매개변수로 전달받은 객체를 할당한다.
3) 생성자 함수 F를 생성자로 하여 새로운 객채를 생성하고 반환한다.

![image](https://user-images.githubusercontent.com/44339530/132475543-75dc22c6-e5b2-490b-b79e-2e8c38f0de62.png)

# [6. 캡슐화(Encapsulation)와 모듈 패턴(Module Pattern)](https://poiemaweb.com/js-object-oriented-programming#6-%EC%BA%A1%EC%8A%90%ED%99%94encapsulation%EC%99%80-%EB%AA%A8%EB%93%88-%ED%8C%A8%ED%84%B4module-pattern)
`캡슐화`는 관련있는 멤버 변수와 메소드를 클래스와 같은 하나의 틀 안에 담고 <b>외부에 공개될 필요가 없는 정보는 숨기는 것</b>을 말하며 다른 말로 `정보 은닉(information hiding)`이라고 한다.

자바스크립트는 자바처럼 `public`, `private`과 같은 접근 제어자 키워드를 제공하지 않는다. 하지만 정보 은닉이 불가능한 것은 아니다.

~~~
var Person = function(arg) {
  var name = arg ? arg : ''; // ①

  this.getName = function() {
    return name;
  };

  this.setName = function(arg) {
    name = arg;
  };
}

var me = new Person('Lee');

var name = me.getName();

console.log(name);

me.setName('Kim');
name = me.getName();

console.log(name);
~~~

①의 name 변수는 private 변수가 된다. <b>자바스크립트는 function-level scope를 제공하므로 함수 내의 변수는 외부에서 참조할 수 없다. 만약에 var 때신 this를 사용하면 public 멤버가 된다. 단 new 키워드로 객체를 생성하지 않으면 this는 생성된 객체에 바인딩되지 않고 전역객체에 연결된다.</b>

그리고 <b>public 메소드 getName, setName은 클로저로서 private 변수(자유 변수)에 접근할 수 있다.</b> 이것이 기본적인 정보 은닉 방법이다.

위 예제를 조금 더 정리해보자.

~~~
var person = function(arg) {
  var name = arg ? arg : '';

  return {
    getName: function() {
      return name;
    },
    setName: function(arg) {
      name = arg;
    }
  }
}

var me = person('Lee'); /* or var me = new person('Lee'); */

var name = me.getName();

console.log(name);

me.setName('Kim');
name = me.getName();

console.log(name);
~~~

person 함수는 객체를 반환한다. 이 객체 내의 메소드 getName, setName은 클로저로서 private 변수 name에 접근할 수 있다. 이러한 방식을 `모듈 패턴`이라 하며 캡슐화와 정보 은닉를 제공한다. 많은 라이브러리에서 사용되는 유용한 패턴이다.

이 모듈 패턴은 다음과 같은 주의할 점이 있다.
- <b>private 멤버가 객체나 배열일 경우, 반환된 해당 멤버의 변경이 가능하다.</b>

~~~
var person = function (personInfo) {
  var o = personInfo;

  return {
    getPersonInfo: function() {
      return o;
    }
  };
};

var me = person({ name: 'Lee', gender: 'male' });

var myInfo = me.getPersonInfo();
console.log('myInfo: ', myInfo);
// myInfo:  { name: 'Lee', gender: 'male' }

myInfo.name = 'Kim';

myInfo = me.getPersonInfo();
console.log('myInfo: ', myInfo);
// myInfo:  { name: 'Kim', gender: 'male' }
~~~

<b>객체를 반환하는 경우 반환값은 [얕은 복사(shallow copy)](https://blueshw.github.io/2016/01/20/shallow-copy-deep-copy/)로 private 멤버의 참조값을 반환하게 된다. 따라서 외부에서도 private 멤버의 값을 변경할 수 있다. 이를 회피하기 위해서는 객체를 그대로 반환하지 않고 반환해야 할 객체의 정보를 새로운 객체에 담아 반환해야 한다. 반드시 객체 전체가 그대로 반환되어야 하는 경우에는 깊은 복사(deep copy)로 복사본을 만들어 반환한다.</b>

- <b>person 함수가 반환한 객체는 person 함수 객체의 프로토타입에 접근할 수 없다. 이는 상속을 구현할 수 없음을 의미한다.</b>

<i>앞에서 살펴본 모듈 패턴은 생성자 함수가 아니며 단순히 메소드를 담은 객체를 반환한다. 반환된 객체는 객체 리터럴 방식으로 생성된 객체로 함수 person의 프로토타입에 접근할 수 없다.</i>

~~~
var person = function(arg) {
  var name = arg ? arg : '';

  return {
    getName: function() {
      return name;
    },
    setName: function(arg) {
      name = arg;
    }
  }
}

var me = person('Lee');

console.log(person.prototype === me.__proto__); // false
console.log(me.__proto__ === Object.prototype); // true: 객체 리터럴 방식으로 생성된 객체와 동일하다
~~~

![image](https://user-images.githubusercontent.com/44339530/132479407-6fb3e271-df1a-42c8-9d10-57db02876740.png)

반환된 객체가 함수 person의 프로토타입에 접근할 수 없다는 것은 person을 부모 객체로 상속할 수 없다는 것을 의미한다.
함수 person을 부모 객체로 상속할 수 없다는 것은 함수 person이 반환하는 객체에 모든 메소드를 포함시켜야한다는 것을 의미한다.

<b>이 문제를 해결하기 위해서는 객체를 반환하는 것이 아닌 함수를 반환해야 한다.</b>

~~~
var Person = function() {
  var name;

  var F = function(arg) { name = arg ? arg : ''; };

  F.prototype = {
    getName: function() {
      return name;
    },
    setName: function(arg) {
      name = arg;
    }
  };

  return F;
}();

var me = new Person('Lee');

console.log(Person.prototype === me.__proto__);

console.log(me.getName());
me.setName('Kim')
console.log(me.getName());
~~~

![image](https://user-images.githubusercontent.com/44339530/132480344-c459de9a-c92d-4ffe-8486-9548271512a7.png)

<b>캡슐화를 구현하는 패턴은 다양하며 각각의 패턴에는 장단점이 있다. 다양한 패턴의 장단점을 분석하고 파악하는 것이 보다 효율적인 코드를 작성하는데 중요하다.</b>

지금까지 자바스크립트에서의 객체지향 프로그래밍을 구현하는 방법을 알아보았다. 사실 자바스크립트는 클래스 기반 언어가 아니므로 기존의 전통적 방식으로 구현하려는 시도는 바른 판단은 아니다. 다만 객체지향 프로그래밍이 추구하는 `재사용성`, `유지보수의 용이성` 등을 극대화하기 위한 노력의 일환으로 보아야 한다. 자바스크립트만의 방식을 잘 활용하여 기존의 방식에 얽매이지 않는다면 보다 효율적인 프로그래밍이 가능할 것이다.

#### 출처
- [https://poiemaweb.com/js-object-oriented-programming](https://poiemaweb.com/js-object-oriented-programming)