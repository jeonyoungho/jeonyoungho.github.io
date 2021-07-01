---
title: "[개발자 블로그] Log4j location 설정" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-07-01 +0800
categories: [Spring, Configuration] # categories는 최대 2개까지 가능
tags: [Spring, Log4j] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# log4j location 설정

- log4j.xml 또는 log4j.properties파일은 classes폴더에 있으면, 자동으로 읽어오지만 굳이 classes가 아닌 다른 곳에 위치하고 싶은 경우는 web.xml에 Log4jConfigListener를 등록하여 사용한다.
- 예시<br>
![image](https://user-images.githubusercontent.com/44339530/98518684-0d25bf00-22b3-11eb-9927-4eb010323a2b.png)<br>

#### 출처
- [https://regexr.tistory.com/15](https://regexr.tistory.com/15)


