---
title: "[개발자 블로그] 5.9 타입 변환과 단축 평가(PoiemaWeb-Javascript)" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-09-06 18:10:00 +0800
categories: [Frontend, Javascript, PoiemaWeb] # categories는 최대 2개까지 가능
tags: [javascript, ES6] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# [1. 타입 변환이란?](https://poiemaweb.com/js-type-coercion#1-%ED%83%80%EC%9E%85-%EB%B3%80%ED%99%98%EC%9D%B4%EB%9E%80)
암묵적 타입 변환은 변수 값을 재할당해서 변경하는 것이 아니라 자바스크립트 엔진이 표현식을 에러없이 평가하기 위해 기존 값을 바탕으로 새로운 타입의 값을 만들어 단 한번 사용하고 버린다.

# [5. 단축 평가](https://poiemaweb.com/js-type-coercion#5-%EB%8B%A8%EC%B6%95-%ED%8F%89%EA%B0%80)
논리곱 연산자 &&와 논리합 연산자 ||는 논리 평가를 결정한 피연산자의 평가 결과를 그대로 반환한다. 이를 단축 평가(Short-Circuit evaluation)라 부른다.

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

## 단축 평가 사용 예시

### 1) 객체가 null인지 확인하고 프로퍼티를 참조할 때
~~~
var elem = null;

console.log(elem.value); // TypeError: Cannot read property 'value' of null
console.log(elem && elem.value); // null
~~~

객체는 키(key)과 값(value)으로 구성된 프로퍼티(Property)들의 집합이다. 만약 객체가 null인 경우, 객체의 프로퍼티를 참조하면 타입 에러(TypeError)가 발생한다. 이때 단축 평가를 사용하면 에러를 발생시키지 않는다.

### 2) 함수의 인수(argument)를 초기화할 때
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

#### 출처
- [https://poiemaweb.com/js-type-coercion](https://poiemaweb.com/js-type-coercion)