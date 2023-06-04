---
title: "[DDDSTART] Chapter5-스프링 데이터 JPA를 이용한 조회기능" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2023-05-30 15:10:00 +0800
categories: [DDD, DDDSTART] # categories는 최대 2개까지 가능
tags: [ddd, dddstart] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

### 시작에 앞서
- CQRS 는 명령(COMMAND) 모델과 조회(Query) 모델을 분리하는 패턴이다.
  - 명령 모델은 상태(데이터)를 변경하는 기능을 구현할 때 사용된다(ex. 회원 가입, 암호 변경, 주문 취소)
  - 조회 모델은 데이터를 조회하는 기능을 구현시 사용된다.(ex. 주문 목록, 주문 상세)
- 주문 취소, 배송지 변경 기능을 포함하여 앞에서 살펴본 것 처럼 도메인 모델은 명령 모델로 주로 사용된다.
- 반면에, 이 장에서 설명할 정렬, 페이징, 검색 조건 지정과 같은 기능은 주문 목록, 상품 상세와 같은 조회기능에서 사용된다. 즉 이장에서 살펴볼 구현 방법은 조회 모델을 구현시 주로 사용한다.

> **Note**: 모든 DB연동 코드를 JPA만 사용해서 구현해야 한다고 생각하진 말자. MyBatis, JdbcTemplate 등 다양한 기술을 사용해서 조회 모델을 구현할 수 있다.