---
title: "[JPA] default_batch_fetch_size 내부 동작 방식" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-11-21 +0800
categories: [JPA] # categories는 최대 2개까지 가능
tags: [Spring, JPA, SpringDataJPA] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

#### 출처
- [https://www.inflearn.com/questions/34469](https://www.inflearn.com/questions/34469)
- [https://velog.io/@jadenkim5179/Spring-defaultbatchfetchsize%EC%9D%98-%EC%9E%91%EB%8F%99%EC%9B%90%EB%A6%AC](https://velog.io/@jadenkim5179/Spring-defaultbatchfetchsize%EC%9D%98-%EC%9E%91%EB%8F%99%EC%9B%90%EB%A6%AC)