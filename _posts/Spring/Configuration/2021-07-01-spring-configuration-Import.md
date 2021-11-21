---
title: "[개발자 블로그] Import" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-07-01 +0800
categories: [Spring, Configuration] # categories는 최대 2개까지 가능
tags: [spring, import] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# Import
- spring-context.xml에서 다른 context.xml을 import하여 등록하고 싶을 경우 다음과 같이 설정한다.

~~~
<import resource="spring/datasource.xml"/>
<import resource="spring/mybatis.xml"/>
~~~

#### 출처
- [https://cheershennah.tistory.com/77](https://cheershennah.tistory.com/77)