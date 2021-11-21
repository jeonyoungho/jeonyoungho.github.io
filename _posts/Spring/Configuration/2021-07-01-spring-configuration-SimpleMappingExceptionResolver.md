---
title: "[개발자 블로그] SimpleMappingExceptionResolver" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-07-01 +0800
categories: [Spring, Configuration] # categories는 최대 2개까지 가능
tags: [spring, simplemappingexceptionresolver] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# SimpleMappingExceptionResolver
- Spring MVC에서 Controller단에 발생한 Exception의 종류에 따라 에러 처리 뷰로 이동<br>
- sample-servlet.xml<br>

~~~
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
  	<property name="defaultErrorView" value="exception/CommonCaseException"/>
  	<property name="exceptionMappings">
  		<props>
  			<prop key="org.egovframe.exception.ACaseException">exception/ACaseException</prop>
  			<prop key="org.egovframe.exception.BCaseException">exception/BCaseException</prop>
  		</props>
  	</property>
</bean>
~~~

- 다음과 같은 설정으로 인해 ACaseException이 발생하면 exception/ACaseException.jsp로 이동시켜주고 BCaseException이 발생하면 exception/BCaseException.jsp로 이동시켜준다.
- 만약 명시된 exception이외에 다른 Excpetion이 발생할 경우 defaultErrorView에 설정된 화면으로 이동시켜준다.

#### 출처
- [https://www.egovframe.go.kr/wiki/doku.php?id=egovframework:rte:ptl:exception_handling](https://www.egovframe.go.kr/wiki/doku.php?id=egovframework:rte:ptl:exception_handling)