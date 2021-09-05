---
title: "[개발자 블로그] Javascript 관련 새로 배운 내용" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-09-03 +0800
categories: [Frontend, Basic] # categories는 최대 2개까지 가능
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

#### 출처
- [https://poiemaweb.com/](https://poiemaweb.com/)