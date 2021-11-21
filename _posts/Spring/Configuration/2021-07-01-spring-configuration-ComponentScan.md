---
title: "[개발자 블로그] Spring Component-Scan configuration" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-07-01 +0800
categories: [Spring, Configuration] # categories는 최대 2개까지 가능
tags: [spring, component-scan] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# Component-Scan

- base-package에 지정된 패키지를 풀스캔하여 bean으로 등록

~~~
<context:component-scan base-package="com.esum.web" use-default-filters="false">
	<context:include-filter type="annotation" expression="org.springframework.sterotype.Controller" />
</context:component-scan>
~~~

- include-filter와 exclude-filter를 통해 자동 스캔 대상에 포함 시킬 클래스와 포함시키지 않을 클래스를 지정 할 수 있다.<br>
- use-default-filters='false'속성은 expression에 지정된 타입만 포함할 수 있다.<br>

#### 출처
- [https://codedragon.tistory.com/9017](https://codedragon.tistory.com/9017)