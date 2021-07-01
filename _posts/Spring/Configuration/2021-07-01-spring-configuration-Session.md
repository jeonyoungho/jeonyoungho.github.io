---
title: "[개발자 블로그] spring session configuration" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-07-01 +0800
categories: [Spring, Configuration] # categories는 최대 2개까지 가능
tags: [Spring, quartz] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# session설정
- 세션: 웹 서버쪽의 웹 컨테이너에 상태를 유지하기 위한 정보<br>
- web.xml에 세션 유지 시간 지정 (단위: 분)<br>
- 디폴트는 30분<br>
![image](https://user-images.githubusercontent.com/44339530/98612902-83b8d000-2338-11eb-83ee-e448a587245f.png)<br>

# Spring Security 동시 세션 제어
- session timeout만료 후에도 session이 제거되지 않는 현상을 해결
- SessionDestroyedEvent 이벤트를 발생시켜주는 Event Publisher인 스프링에서 제공하는 HttpSessionEventPublisher를 listener로 다음과 같이 web.xml에 등록<br>
![image](https://user-images.githubusercontent.com/44339530/100308201-1e790600-2feb-11eb-9e7d-5872c1cae235.png)<br>

- HttpSessionEventPublisher가 session이 만료되는 것을 리스닝하면서 만료될때마다 sessionDestroyed를 호출하여 sesseion을 종료심<br>

#### 출처
- [https://codedragon.tistory.com/5124](https://codedragon.tistory.com/5124)
- [https://behonestar.tistory.com/75](https://behonestar.tistory.com/75)