---
title: "Spring Cloud 개요" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-07-01 +0800
categories: [MSA, SpringCloud] # categories는 최대 2개까지 가능
tags: [MSA, MicroServiceArchitecture, SpringCloud] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# Spring Cloud란?
- 마이크로서비스의 개발, 배포, 운영에 필요한 아키텍처를 쉽게 구성할 수 있도록 지원하는 Spring Boot기반의 프레임워크
- <b>즉, MSA구성을 지원하는 Springboot기반의 프레임워크이다.</b>

## Spring Cloud의 핵심 Component
![3](https://user-images.githubusercontent.com/44339530/115350118-a86e7900-a1ef-11eb-86bc-f6383465687e.png)<br>

## Spring cloud 컴포넌트 메뉴얼
- Spring cloud는 Spring community인 spring.io에서 오픈소스 프로젝트로 관리되고 있다.
- http://spring.io 를 들어가셔서 Projects > Spring Cloud에서 자세하게 확인 가능하다.(밑에 링크를 통해서 바로 접속 가능)<br>
- [https://docs.spring.io/spring-cloud/docs/current/reference/html/](https://docs.spring.io/spring-cloud/docs/current/reference/html/)

## Spring Cloud Netflix의 zuul, hystrix, ribbon EOS(End Of Service)
- 2018년 12월부터 아래 component들은 Maintenance모드(기능 업그레이드 없고 유지만 함)로 들어갔습니다.
- 또한 Springboot 2.4.X부터는 더 이상 지원하지 않습니다.
- 따라서 대체되는 다른 Component들을 사용하는것이 좋습니다.
- https://spring.io/blog/2018/12/12/spring-cloud-greenwich-rc1-available-now<br>

#### 출처
- [https://happycloud-lee.tistory.com/207?category=902419](https://happycloud-lee.tistory.com/207?category=902419)