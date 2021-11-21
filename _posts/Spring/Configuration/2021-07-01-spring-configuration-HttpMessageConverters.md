---
title: "[개발자 블로그] HttpMessageConverters" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-07-01 +0800
categories: [Spring, Configuration] # categories는 최대 2개까지 가능
tags: [spring, httpmessageconverters] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---


# HttpMessageConverters

- 컨트롤러에서 넘어온 데이터(JSON 같은)를 messageConverter로 사용하기 위해 사용<br>

~~~
<mvc:annotation-driven>
	<!--<mvc:message-converters>-->
		<!--<bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">-->
			<!--<property name="supportedMediaTypes" value="application/json" />-->
		<!--</bean>-->
	<!--</mvc:message-converters>-->
</mvc:annotation-driven>
~~~


#### 출처 
- [https://devjackie.tistory.com/entry/spring-32-이상에서-사용할-수-있는-messageConverter-방법](https://devjackie.tistory.com/entry/spring-32-이상에서-사용할-수-있는-messageConverter-방법)