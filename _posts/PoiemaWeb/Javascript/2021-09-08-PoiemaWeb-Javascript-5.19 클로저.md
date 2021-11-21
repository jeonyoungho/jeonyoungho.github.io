---
title: "[개발자 블로그] 5.19 클로저(PoiemaWeb-Javascript)" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-09-08 +0800
categories: [PoiemaWeb, Javascript] # categories는 최대 2개까지 가능
tags: [poiemaweb, javascript, es6] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# [1. 클로저(closure)의 개념](https://poiemaweb.com/js-closure#1-%ED%81%B4%EB%A1%9C%EC%A0%80closure%EC%9D%98-%EA%B0%9C%EB%85%90)

~~~
function outerFunc() {
  var x = 10;
  var innerFunc = function () { console.log(x); };
  return innerFunc;
}

/**
 *  함수 outerFunc를 호출하면 내부 함수 innerFunc가 반환된다.
 *  그리고 함수 outerFunc의 실행 컨텍스트는 소멸한다.
 */
var inner = outerFunc();
inner(); // 10
~~~

위의 예제 코드처럼 자신을 포함하고 있는 외부함수보다 내부함수가 더 오래 유지되는 경우, 외부 함수 밖에서 내부함수가 호출되더라도 외부함수의 지역 변수에 접근할 수 있는데 이러한 함수를 `클로저(Closure)`라고 부른다.

<b>즉, 클로저는 반환된 내부함수가 자신이 선언됐을 때의 환경(Lexical environment)인 스코프를 기억하여 자신이 선언됐을 때의 환경(스코프) 밖에서 호출되어도 그 환경(스코프)에 접근할 수 있는 함수를 말한다.</b> 이를 조금 더 간단히 말하면 <b>클로저는 자신이 생성될 때의 환경(Lexical environment)을 기억하는 함수다라고 말할 수 있겠다.</b>

실행 컨텍스트의 관점에 설명하면, 내부함수가 유효한 상태에서 외부함수가 종료하여 외부함수의 실행 컨텍스트가 반환되어도, 외부함수 실행 컨텍스트 내의 `활성 객체(Activation object)`(변수, 함수 선언 등의 정보를 가지고 있다)는 <b>내부함수에 의해 참조되는 한 유효</b>하여 내부함수가 <b>스코프 체인</b>을 통해 참조할 수 있는 것을 의미한다.

즉 외부함수가 이미 반환되었어도 외부함수 내의 변수는 이를 필요로 하는 내부함수가 하나 이상 존재하는 경우 계속 유지된다. 이때 내부함수가 외부함수에 있는 변수의 복사본이 아니라 실제 변수에 접근한다는 것에 주의하여야 한다.

이를 그림으로 표현하면 아래와 같다.

![image](https://user-images.githubusercontent.com/44339530/132440492-06f8af17-dc25-47a4-a88d-a25d1ac9b986.png)





#### 출처
- [https://poiemaweb.com/js-closure](https://poiemaweb.com/js-closure)