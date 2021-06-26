---
title: "[개발자 블로그] Library와Framework의 차이" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-06-26 00:00:00 +0800
categories: [Spring] # categories는 최대 2개까지 가능
tags: [Library, Framework, Spring] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

## Framework란?
- “소프트웨어의 구체적인 부분에 해당하는 설계와 구현을 재사용이 가능하게끔 일련의 협업화된 형태로 클래스들을 제공하는 것” - 랄프 존슨(Ralph Johnson) -
- 말 그대로 뼈대나 근간을 이루는 코드들을 묶어놓은 것
프레임워크는 반제품으로 애플리케이션 구조 및 코드의 상당 부분을 제공하여 개발자는 핵심 비즈니스로직에만 집중할 수 있다.

## Framework의 장점
- 1)생산성
    - Framework가 소프트웨어의 구조 및 기반이 되는 클래스를 제공하기에 개발자는 비즈니스 로직에만 집중할 수 있기에 생산성을 높일 수 가 있다.
- 2) 코드 품질
    - 코드의 재사용 및 유지 보수 용이
    - 확장성을 가진 코드 설계 가능

## Framework와 Library의 차이
- <b>가장 중요한 차이점은 "Inversion of Control"</b>

#### 프레임워크
- 제어의 주체는 프레임워크: 프레임워크에서 여러분의 코드를 호출(제어의 역전)
- 소프트웨어의 구체적인 부분에 해당하는 설계와 구현을 재사용이 가능하게끔 일련의 협업화된 형태로 클래스들을 제공하는 것
- ex) 자동차의 프레임, 즉 기본적으로 구성하고 있는 뼈대

#### 라이브러리
- 제어의 주체는 개발자: 코드에서 라이브러리 함수를 호출
자주 사용되는 로직을 재사용하기 편리하도록 잘 정리한 일련의 클래스들의 집합
- ex) 자동차의 기능을 하는 부품

#### 출처
- https://gmlwjd9405.github.io/2018/10/26/spring-framework.html