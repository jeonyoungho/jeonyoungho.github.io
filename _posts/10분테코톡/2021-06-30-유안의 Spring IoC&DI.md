---
title: "[개발자 블로그] 유안의 Spring IoC/DI" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-06-30 +0800
categories: [10분 테코톡] # categories는 최대 2개까지 가능
tags: [Java, Spring, IoC, DI, 10분 테코톡] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

- [유튜브 영상](https://www.youtube.com/watch?v=_OI9mKuFb7c&list=PLgXGHBqgT2TvpJ_p9L_yZKPifgdBOzdVH&index=24)

## IoC(Inversion Of Control)/DI
- Spring 컨테이너가 빈들을 자동으로 생성 및 관리하면서 빈들의 연관관계도 자동으로 엮어준다.
- 애플리케이션 제어의 주체는 Spring이 되어 개발자는 비즈니스 로직에만 집중하면 된다.