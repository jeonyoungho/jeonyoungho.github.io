---
title: "[JPA] JPA 양방향 Entity 무한재귀 문제" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-07-01 +0800
categories: [JPA] # categories는 최대 2개까지 가능
tags: [spring, jpa] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# JPA 양방향 Entity 무한재귀 문제
- @JsonIgnoreProperties 어노테이션으로 무시할 속성 지정

#### 출처
- [https://thxwelchs.github.io/JPA%20%EC%96%91%EB%B0%A9%ED%96%A5%20Entity%20%EB%AC%B4%ED%95%9C%20%EC%9E%AC%EA%B7%80%20%EB%AC%B8%EC%A0%9C%20%ED%95%B4%EA%B2%B0/](https://thxwelchs.github.io/JPA%20%EC%96%91%EB%B0%A9%ED%96%A5%20Entity%20%EB%AC%B4%ED%95%9C%20%EC%9E%AC%EA%B7%80%20%EB%AC%B8%EC%A0%9C%20%ED%95%B4%EA%B2%B0/)