---
title: "[개발자 블로그] 5.30 배열 고차 함수" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-09-09 +0800
categories: [PoiemaWeb, Javascript] # categories는 최대 2개까지 가능
tags: [javascript, ES6] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

고차 함수(Higher order function)는함수를 인자로 전달받거나 함수를 결과로 반환하는 함수를 말한다. 다시 말해, 고차 함수는 인자로 받은 함수를 필요한 시점에 호출하거나 [클로저](https://poiemaweb.com/js-closure)를 생성하여 반환한다. 자바스크립트의 함수는 [일급 객체](https://poiemaweb.com/js-function#3-first-class-object-%EC%9D%BC%EA%B8%89-%EA%B0%9D%EC%B2%B4)이므로 값처럼 인자로 전달할 수 있으며 반환할 수도 있다.

~~~
// 함수를 인자로 전달받고 함수를 반환하는 고차 함수
function makeCounter(predicate) {
  // 자유 변수. num의 상태는 유지되어야 한다.
  let num = 0;
  // 클로저. num의 상태를 유지한다.
  return function () {
    // predicate는 자유 변수 num의 상태를 변화시킨다.
    num = predicate(num);
    return num;
  };
}

// 보조 함수
function increase(n) {
  return ++n;
}

// 보조 함수
function decrease(n) {
  return --n;
}

// makeCounter는 함수를 인수로 전달받는다. 그리고 클로저를 반환한다.
const increaser = makeCounter(increase);
console.log(increaser()); // 1
console.log(increaser()); // 2

// makeCounter는 함수를 인수로 전달받는다. 그리고 클로저를 반환한다.
const decreaser = makeCounter(decrease);
console.log(decreaser()); // -1
console.log(decreaser()); // -2
~~~

고차 함수는 외부 상태 변경이나 가변(mutable) 데이터를 피하고 `불변성(Immutability)`을 지향하는 함수형 프로그래밍에 기반을 두고 있다. <b>함수형 프로그래밍은 순수 함수(Pure function)와 보조 함수의 조합을 통해 로직 내에 존재하는 조건문과 반복문을 제거하여 복잡성을 해결하고 변수의 사용을 억제하여 상태 변경을 피하려는 프로그래밍 패러다임이다.</b> 조건문이나 반복문은 로직의 흐름을 이해하기 어렵게 하여 가독성을 해치고, 변수의 값은 누군가에 의해 언제든지 변경될 수 있어 오류 발생의 근본적 원인이 될 수 있기 때문이다.

<b>함수형 프로그래밍은 결국 순수 함수를 통해 부수 효과(Side effect)를 최대한 억제하여 오류를 피하고 프로그램의 안정성을 높이려는 노력의 한 방법이라고 할 수 있다.</b>

자바스크립트는 고차 함수를 다수 지원하고 있다. 특히 Array 객체는 매우 유용한 고차 함수를 제공한다. 이들 함수는 2가지 유형으로 나뉘며 이에 대해 살펴보도록 하자.
1)메소드는 this(원본 배열)를 변경한다.
2)메소드는 this(원본 배열)를 변경하지 않는다.

- [1. Array.prototype.sort(compareFn?: (a: T, b: T) => number): this ES1 (원본 배열 변경 O)](https://poiemaweb.com/js-array-higher-order-function#1-arrayprototypesortcomparefn-a-t-b-t--number-this-%EF%B8%8F-es1)
- [2. Array.prototype.forEach(callback: (value: T, index: number, array: T[]) => void, thisArg?: any): void ES5 (원본 배열 변경 X)](https://poiemaweb.com/js-array-higher-order-function#2-arrayprototypeforeachcallback-value-t-index-number-array-t--void-thisarg-any-void--es5)
- [3. Array.prototype.map<U>(callbackfn: (value: T, index: number, array: T[]) => U, thisArg?: any): U[] ES5 (원본 배열 변경 X)](https://poiemaweb.com/js-array-higher-order-function#3-arrayprototypemapucallbackfn-value-t-index-number-array-t--u-thisarg-any-u--es5)
- [4. Array.prototype.filter(callback: (value: T, index: number, array: Array) => any, thisArg?: any): T[] ES5 (원본 배열 변경 X)](https://poiemaweb.com/js-array-higher-order-function#4-arrayprototypefiltercallback-value-t-index-number-array-array--any-thisarg-any-t--es5)
- [5. Array.prototype.reduce<U>(callback: (state: U, element: T, index: number, array: T[]) => U, firstState?: U): U ES5 (원본 배열 변경 X)](https://poiemaweb.com/js-array-higher-order-function#5-arrayprototypereduceucallback-state-u-element-t-index-number-array-t--u-firststate-u-u--es5)
- [6. Array.prototype.some(callback: (value: T, index: number, array: Array) => boolean, thisArg?: any): boolean ES5 (원본 배열 변경 X)](https://poiemaweb.com/js-array-higher-order-function#6-arrayprototypesomecallback-value-t-index-number-array-array--boolean-thisarg-any-boolean--es5)
- [7. Array.prototype.every(callback: (value: T, index: number, array: Array) => boolean, thisArg?: any): boolean ES5 (원본 배열 변경 X)](https://poiemaweb.com/js-array-higher-order-function#7-arrayprototypeeverycallback-value-t-index-number-array-array--boolean-thisarg-any-boolean--es5)
- [8. Array.prototype.find(predicate: (value: T, index: number, obj: T[]) => boolean, thisArg?: any): T | undefined ES6 (원본 배열 변경 X)](https://poiemaweb.com/js-array-higher-order-function#8-arrayprototypefindpredicate-value-t-index-number-obj-t--boolean-thisarg-any-t--undefined--es6)
- [9. Array.prototype.findIndex(predicate: (value: T, index: number, obj: T[]) => boolean, thisArg?: any): number ES6 (원본 배열 변경 X)](https://poiemaweb.com/js-array-higher-order-function#9-arrayprototypefindindexpredicate-value-t-index-number-obj-t--boolean-thisarg-any-number--es6)

#### 출처
- [https://poiemaweb.com/js-array-higher-order-function](https://poiemaweb.com/js-array-higher-order-function)