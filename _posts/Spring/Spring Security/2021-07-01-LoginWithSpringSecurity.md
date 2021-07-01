---
title: "[개발자 블로그] Login With Spring Security" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-07-01 +0800
categories: [Spring] # categories는 최대 2개까지 가능
tags: [Spring, SpringSecurity] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---



## 5) DB에 유저의 정보를 저장하는 방식으로 변경
- data-source-ref -> DB연동이므로 datasource를 지정해줘야함
- users-by-username-query -> 유저 이름을 스프링이 파악하기 위해 sql문 지정
- authorities-by-username-query -> 그 유저에 대한 권한을 스프링이 파악하기 위해 sql문지정<br>
<img width="844" alt="7" src="https://user-images.githubusercontent.com/44339530/76516192-54e31500-649e-11ea-8456-8a0b570c7530.png"><br>

## 6) DB Table 생성 및 레코드 삽입
<img width="844" alt="8" src="https://user-images.githubusercontent.com/44339530/76516194-557bab80-649e-11ea-966e-ff61e5eb0c00.png"><br>
<img width="844" alt="9" src="https://user-images.githubusercontent.com/44339530/76516199-56144200-649e-11ea-819c-4b76e870838c.png"><br>
<img width="844" alt="10" src="https://user-images.githubusercontent.com/44339530/76516200-56144200-649e-11ea-8ca2-31758332de7f.png"><br>

## 7) hasRole('ROLE_USER') -> ROLE_USER의 권한이 있는 사람만 접근하도록 허용
<img width="844" alt="11" src="https://user-images.githubusercontent.com/44339530/76516202-56acd880-649e-11ea-9f15-e639100a292c.png"><br>

## 8) 테스트