---
title: "[개발자 블로그] 5.12 함수(PoiemaWeb-Javascript)" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-09-06 18:30:00 +0800
categories: [Frontend, Javascript, PoiemaWeb] # categories는 최대 2개까지 가능
tags: [javascript, ES6] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# [2. 함수 호이스팅](https://poiemaweb.com/js-function#2-%ED%95%A8%EC%88%98-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85)
자바스크립트는 ES6의 let, const를 포함하여 모든 선언(var, let, const, function, function*, class)을 호이스팅(Hoisting)한다.

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

# [4.1 매개변수(parameter, 인자) vs 인수(argument)](https://poiemaweb.com/js-function#41-%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98parameter-%EC%9D%B8%EC%9E%90-vs-%EC%9D%B8%EC%88%98argument)
자바스크립트 함수를 호출 시 인수를 전달하지 않으면 매개변수는 undefined로 초기화된다.

#### 출처
- [https://poiemaweb.com/js-object](https://poiemaweb.com/js-object)