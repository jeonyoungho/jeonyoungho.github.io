---
title: "[개발자 블로그] Log4j & Logback & Log4j2의 차이" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-07-01 +0800
categories: [Spring, Logging] # categories는 최대 2개까지 가능
tags: [Spring, Logging, Slf4j, Log4j, Logback, Log4j2] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# Log4j&Logback&Log4j2의차이
## log4j
- 콘솔로 출력하는 stdout 외에도 파일 출력도 제공한다. 
- 하지만 2015년에 개발이 중단되었기 때문에 기존 시스템이 아니라면 사용할 이유가 없다.

## logback
- log4j2 전에 개발된 로깅프로그램으로 log4j에서 향상된 성능과 필터링 옵션을 제공한다.
- slf4j도 지원한다. 그리고 자동 리로드도 가능하다.

## log4j2
- logback과 동일하게 자동 리로드 기능과 필터리 기능을 제공한다.
- Log4j 및 logback과 차이점은 Apache에 따르면 멀티 쓰레드 환경에서 비동기 로거(Async Logger)의 경우 처리량이 18배 더 높고 대기 시간이 훨씬 더 짧다.
- 람다 표현식과 사용자 정의 로그 레벨도 지원한다.

#### 출처
- [https://junshock5.tistory.com/124](https://junshock5.tistory.com/124)