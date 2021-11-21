---
title: "[개발자 블로그] ContextLoaderListener와 DispatcherServlet의 차이"
# post의 layout이 기본적으로 post으로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 된다.
date: 2021-06-27 +0800
categories: [Spring] # categories는 최대 2개까지 가능
tags: [spring, springboot, contextloaderlistener, dispatcherservlet] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
---

## ContextLoaderListener와 DispatcherServlet의 차이
<img width="844" alt="스크린샷 2020-03-24 오후 4 06 17" src="https://user-images.githubusercontent.com/44339530/77397714-7242a800-6de9-11ea-9c5c-e476e45862fb.png"><br>

- ContextLoaderListener와 DispatcherServlet 모두 자체적으로 스프링 컨테이너를 만듬
 - ContextLoaderListener안에 있는 Bean들은 shared beans(공유하는 bean들 -  DAO, DataSource, Service)
- DispatcherServlet안에 있는 bean들은 requset가 왔을때 처리하는 컨트롤러를 주로 가지고 있음
- 나중에 war파일로 패키징하게 되면 webapp이 root가 되기에 webapp을 /로 해서 경로지정<br>

<img width="844" alt="스크린샷 2020-03-24 오후 4 16 42" src="https://user-images.githubusercontent.com/44339530/77398435-db76eb00-6dea-11ea-9a27-400f12c8b1cb.png"><br>
