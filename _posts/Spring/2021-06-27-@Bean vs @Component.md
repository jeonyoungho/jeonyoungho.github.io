---
title: "[개발자 블로그] @Bean vs @Component"
# post의 layout이 기본적으로 post으로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 된다.
date: 2021-06-27 +0800
categories: [Spring] # categories는 최대 2개까지 가능
tags: [spring, springboot, annotation, bean, component] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
---

## @Bean 어노테이션
- 개발자가 컨트롤이 불가능한 외부 라이브러리들을 Bean으로 등록하고 싶은 경우에 사용한다.
- 예를 들어, RestTemplate 등

## @Component 어노테이션
- 직접 컨트롤이 가능한 Class들을 Bean으로 등록하고 싶은 경우에 사용한다.

## 개발자가 생성한 Class에 @Bean은 설정이 가능한가?
- 불가능하다.
- <b>@Bean과 Component는 각자 선언할 수 있는 타입이 정해져 있어</b> 해당 용도외에는 컴파일 에러를 발생시킨다.<br>

<img width="691" alt="스크린샷 2021-05-03 오후 4 08 04" src="https://user-images.githubusercontent.com/44339530/116849471-c5f90500-ac29-11eb-81b1-3dc430f143cd.png"><br>

#### 출처
- [https://jojoldu.tistory.com/27](https://jojoldu.tistory.com/27)