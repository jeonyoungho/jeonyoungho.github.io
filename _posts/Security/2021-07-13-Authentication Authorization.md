---
title: "[개발자 블로그] 인증과 인가" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-07-13 +0800
categories: [Security] # categories는 최대 2개까지 가능
tags: [authentication, authorization] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# 인증(Authentication)
<b>사용자가 누구인지 확인하는 것</b><br>

- ex) 건물 출입이 가능한 사람인지 확인
    - 삼성전자 직원입니까? -> 정직원 바로 출입 가능
    - 방문자 입니까? -> 정직원 아님. 출입증을 통해 인증해야함

# 인가(Authorization)
<b>사용자가 요청을 실행할 수 있는 권한 여부가 있는지 확인하는 것</b><br>

- ex) 출입할 수 잇는 건물 공간이 어디까지 인가?
    - 건물 관리자 입니까? -> 모든 공간에 출입 가능
    - 방문자 입니까? -> 허용된 공간만 입장 가능, 일부 공간 접근 불가 (관계자 외 출입 불가 지역 등)

#### 출처
- [https://ivorycode.tistory.com/entry/%EC%9D%B8%EC%A6%9DAuthentication%EA%B3%BC-%EC%9D%B8%EA%B0%80Authorization](https://ivorycode.tistory.com/entry/%EC%9D%B8%EC%A6%9DAuthentication%EA%B3%BC-%EC%9D%B8%EA%B0%80Authorization)