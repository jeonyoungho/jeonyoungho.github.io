---
title: "[개발자 블로그] Import"
date: 2021-07-01 +0800
categories: [Spring, Configuration]
tags: [spring, import]
toc: true
comments: true
---

# Import
- spring-context.xml에서 다른 context.xml을 import하여 등록하고 싶을 경우 다음과 같이 설정한다.

~~~
<import resource="spring/datasource.xml"/>
<import resource="spring/mybatis.xml"/>
~~~

#### 출처
- [https://cheershennah.tistory.com/77](https://cheershennah.tistory.com/77)