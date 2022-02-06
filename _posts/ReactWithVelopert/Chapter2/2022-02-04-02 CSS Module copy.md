---
title: "[2장. 리액트 컴포넌트 스타일링하기] 02. CSS Module" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2022-02-04 +0800
categories: [ReactWithVelopert, Chapter2] # categories는 최대 2개까지 가능
tags: [frontend, react, velopert] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

본 포스팅은 [벨로퍼트와 함께하는 모던 리액트](https://react.vlpt.us/)를 학습 후 정리하는 목적으로 작성하는 포스팅입니다.

# 02. CSS Module
이번에는 CSS Module 이라는 기술에 대해서 알아보자. 리액트 프로젝트에서 컴포넌트를 스타일링 할 때 CSS Module 이라는 기술을 사용하면, CSS 클래스가 중첩되는 것을 완벽히 방지할 수 있다.

CRA 로 만든 프로젝트에서 CSS Module 를 사용 할 때에는, CSS 파일의 확장자를 .module.css 로 하면 되는데, 예를 들어서 다음과 같이 Box.module.css 라는 파일을 만들게 된다면

- Box.module.css

```css
.Box {
  background: black;
  color: white;
  padding: 2rem;
}
```

리액트 컴포넌트 파일에서 해당 CSS 파일을 불러올 때 CSS 파일에 선언한 클래스 이름들이 모두 고유해진다. 고유 CSS 클래스 이름이 만들어지는 과정에서는 파일 경로, 파일 이름, 클래스 이름, 해쉬값 등이 사용 될 수 있다.
