---
title: "[개발자 블로그] Log4j location 설정"
date: 2021-07-01 +0800
categories: [Spring, Configuration]
tags: [spring, log4j]
toc: true
comments: true
---

# log4j location 설정

- log4j.xml 또는 log4j.properties파일은 classes폴더에 있으면, 자동으로 읽어오지만 굳이 classes가 아닌 다른 곳에 위치하고 싶은 경우는 web.xml에 Log4jConfigListener를 등록하여 사용한다.
- 예시<br>
![image](https://user-images.githubusercontent.com/44339530/98518684-0d25bf00-22b3-11eb-9927-4eb010323a2b.png)<br>

#### 출처
- [https://regexr.tistory.com/15](https://regexr.tistory.com/15)


