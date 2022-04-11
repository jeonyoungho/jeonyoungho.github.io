---
title: "React에서의 Virtual DOM" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-09-29 +0800
categories: [React] # categories는 최대 2개까지 가능
tags: [react, virtualdom] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# virutal dom 을 알아보기에 앞서 dom이란 무엇인가?
- `Document Object Model` 의 약자
- 객체로 문서 구조를 표현하는 방법(html 파일로부터 브라우저 화면을 표현하는 방법)
- 트리 형태로 구성되며 javascript로 특정 노드의 CRUD 작업이 가능


#### 출처
- [https://brunch.co.kr/@eight-two-five/14](https://brunch.co.kr/@eight-two-five/14)
- [https://velog.io/@gwak2837/React-Virtual-DOM-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0](https://velog.io/@gwak2837/React-Virtual-DOM-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0)
- [https://ko.reactjs.org/docs/faq-internals.html](https://ko.reactjs.org/docs/faq-internals.html)
- [https://dev-cini.tistory.com/10](https://dev-cini.tistory.com/10)
- [https://dev-cini.tistory.com/11](https://dev-cini.tistory.com/11)