---
title: "[개발자 블로그] 5.4 브라우저 동작 원리(PoiemaWeb-Javascript)"
date: 2021-09-06 17:30:00 +0800
categories: [Frontend, PoiemaWeb, Javascript]
tags: [poiemaweb, javascript, es6]
toc: true
comments: true
---

자바 스크립트는 렌더링 엔진이 아닌 자바스크립트 엔진이 처리한다. HTML파서는 script태그를 만나면 자바 스크립트 코드를 실행하기 위해 DOM 생성 프로세스를 중지하고 자바스크립트 엔진으로 제어 권한을 넘긴다.
제어 권한을 넘겨 받은 자바스크립트 엔진은 script 태그 내의 자바스크립트 코드 또는 script 태그의 src 어트리뷰트에 정의된 자바스크립트 파일을 로드하고 파싱하여 실행한다. 자바스크립트의 실행이 완료되면 다시 HTML 파서로 제어 권한을 넘겨서 브라우저가 중지했던 시점부터 DOM 생성을 재개한다.
이처럼 브라우저는 동기(Synchronous)적으로 HTML, CSS, Javascript를 처리한다.  이것은 script 태그의 위치에 따라 블로킹이 발생하여 DOM의 생성이 지연될 수 있다는 것을 의미한다. 

따라서 script 태그의 위치는 중요한 의미를 갖는다. javascript코드는 body요소의 가장 아래에 위치시키는 것이 가장 이상적이다. 그 이유는 아래와 같다.

- HTML 요소들이 스크립트 로딩 지연으로 인해 렌더링에 지장 받는 일이 발생하지 않아 페이지 로딩 시간이 단축된다.
- DOM이 완성되지 않은 상태에서 자바스크립트가 DOM을 조작한다면 에러가 발생한다.

일반적으로 브라우저의 렌더링엔진은 HTML파일을 위에서 부터 아래로 순서대로 읽어처리한다. 하지만 dom엘리먼트들이 구성되지 않은 상태에서 ajax통신을 통해 데이터를 받아와 dom엘리먼트를 조작한다?
에러가 발생할 것이고 이와 같은 이유로 HTML파일에서 css는 위쪽에 javascript코드는 아래쪽에 위치시키는 것이 좋다.
(직관적으로 생각해봐도 서버로부터 데이터를 받아와서 브라우저에 표시했는데 css가 적용이 안되 깨져보이는 상태면 사용자에게 좋지 못한 인상을 줄 것이다)

#### 출처
- [https://poiemaweb.com/js-browser](https://poiemaweb.com/js-browser)