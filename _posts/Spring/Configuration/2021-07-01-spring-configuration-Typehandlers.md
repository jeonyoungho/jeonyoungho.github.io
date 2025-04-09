---
title: "[개발자 블로그] MyBatis TypeHandlers"
date: 2021-07-01 +0800
categories: [Spring, Configuration]
tags: [spring, typehandlers]
toc: true
comments: true
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
