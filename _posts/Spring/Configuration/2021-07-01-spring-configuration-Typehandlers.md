---
title: "[개발자 블로그] MyBatis TypeHandlers" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-07-01 +0800
categories: [Spring, Configuration] # categories는 최대 2개까지 가능
tags: [spring, typehandlers] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# TypeHandlers
- 마이바티스가 PreparedStatement에 파라미터를 설정하고 ResultSet에서 값을 가져올때마다 TypeHandler는 적절한 자바 타입의 값을 가져오기 위해 사용

~~~
<!-- java.sql.Timestamp 를 java.util.Date 형으로 반환 --> 
<typeHandlers>
	<typeHandler javaType="java.sql.Timestamp" handler="org.apache.ibatis.type.DateTypeHandler"/>
</typeHandlers>
~~~

#### 출처
- [https://mybatis.org/mybatis-3/ko/configuration.html](https://mybatis.org/mybatis-3/ko/configuration.html)
