---
title: "[개발자 블로그] ContextLoaderListener와 DispatcherServlet의 차이"
date: 2021-06-27 +0800
categories: [Spring]
tags: [spring, springboot, contextloaderlistener, dispatcherservlet]
toc: true
comments: true
---

## ContextLoaderListener와 DispatcherServlet의 차이
<img width="844" alt="스크린샷 2020-03-24 오후 4 06 17" src="https://user-images.githubusercontent.com/44339530/77397714-7242a800-6de9-11ea-9c5c-e476e45862fb.png"><br>

- ContextLoaderListener와 DispatcherServlet 모두 자체적으로 스프링 컨테이너를 만듬
 - ContextLoaderListener안에 있는 Bean들은 shared beans(공유하는 bean들 -  DAO, DataSource, Service)
- DispatcherServlet안에 있는 bean들은 requset가 왔을때 처리하는 컨트롤러를 주로 가지고 있음
- 나중에 war파일로 패키징하게 되면 webapp이 root가 되기에 webapp을 /로 해서 경로지정<br>

<img width="844" alt="스크린샷 2020-03-24 오후 4 16 42" src="https://user-images.githubusercontent.com/44339530/77398435-db76eb00-6dea-11ea-9a27-400f12c8b1cb.png"><br>
