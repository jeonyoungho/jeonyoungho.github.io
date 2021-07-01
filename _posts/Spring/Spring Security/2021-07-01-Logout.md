---
title: "[개발자 블로그] Logout With Spring Security" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-07-01 +0800
categories: [Spring] # categories는 최대 2개까지 가능
tags: [Spring, SpringSecurity] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

<img width="844" alt="1-" src="https://user-images.githubusercontent.com/44339530/76522456-f58b0200-64a9-11ea-82a3-160ed21f9f93.png"><br><br>

# 1) Logout버튼을 누르면 /logout으로 POST방식으로 보냄. 그러면 spring에 의해 logout절차가 이뤄짐(사용자가 사용했던 쿠키 및 로그인에 관련된 정보를 제거함)

# 2) 성공적으로 logout됐을때 /logout?logout으로 GET방식으로 리다이렉트 시켜준다.

# 3) security-context.xml에 logout에 관련된 설정추가
<img width="844" alt="2-" src="https://user-images.githubusercontent.com/44339530/76522466-fa4fb600-64a9-11ea-89bd-a335f8b978c0.png"><br>
<img width="844" alt="3-" src="https://user-images.githubusercontent.com/44339530/76522470-fae84c80-64a9-11ea-8c89-c4b34650698d.png"><br>

- logout성공시 /login에 logout이란 param을 같이 보내준다.

# 4) 로그아웃에 대한 link나 버튼 추가
<img width="844" alt="4-" src="https://user-images.githubusercontent.com/44339530/76522472-fb80e300-64a9-11ea-91d7-01358500c428.png"><br>

- userPrincipal.name(로그인한 사용자의 이름)이 null이 아니면 출력하도록 설정
- logout이라는 id를 가진 form을 자동적으로 /logout으로 post방식으로 submit하도록 설정
(이때 csrf 토큰도 같이 보내줘야함)<br>
<img width="844" alt="5-" src="https://user-images.githubusercontent.com/44339530/76522474-fc197980-64a9-11ea-8470-3b5024dddcf3.png"><br>

# 5) 테스트
<img width="844" alt="6-" src="https://user-images.githubusercontent.com/44339530/76522476-fcb21000-64a9-11ea-8096-4b8008df4a8c.png"><br>

# 6) controller에서 'logout' param처리 하도록 추가
<img width="844" alt="7-" src="https://user-images.githubusercontent.com/44339530/76522478-fde33d00-64a9-11ea-89c1-f5d059376ae0.png"><br>

# 7) login.jsp에서 logout메시지 출력하도록 추가
<img width="844" alt="8-" src="https://user-images.githubusercontent.com/44339530/76522480-fde33d00-64a9-11ea-91f3-63919d428af6.png"><br>

# 8) security-context.xml에 접근허가 설정
<img width="844" alt="9-" src="https://user-images.githubusercontent.com/44339530/76522484-fe7bd380-64a9-11ea-9da1-bc57bfcebd21.png"><br>

# 9) 테스트
<img width="844" alt="10-" src="https://user-images.githubusercontent.com/44339530/76522486-ff146a00-64a9-11ea-9433-302003e48bf9.png"><br>

`※ 기존의 HTML폼 같은 경우엔 반드시 csrf 토큰을 명시해줘야하고 Spring에서 제공되는 폼은 내부적으로 csrf토큰을 보내주게 되있기때문에 명시안해줘도 된다.`