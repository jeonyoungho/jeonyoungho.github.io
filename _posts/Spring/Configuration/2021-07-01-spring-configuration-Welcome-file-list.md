---
title: "[개발자 블로그] Spring welcome-file-list"
date: 2021-07-01 +0800
categories: [Spring, Configuration]
tags: [spring, welcome-file-list]
toc: true
comments: true
---

# welcome-file-list
- 서버의 루트(/)경로로 request가 들어올 때 띄워주는 파일을 지정
- 예시<br>
![image](https://user-images.githubusercontent.com/44339530/98515586-938bd200-22ae-11eb-9810-5c5ab0efcc3c.png)<br>

- 다음과 같이 web.xml에 설정이 되어있을때 루트(/) 경로로 request가 들어온다면 main1.jsp를 띄워주게 된다. 만약 main1.jsp를 찾이 못한다면 그 다음 등록되 있는 main2.jsp를 띄워준다.
 
#### 출처
- [https://admm.tistory.com/82](https://admm.tistory.com/82)

