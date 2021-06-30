---
title: "Java로 구현한 AES CBC" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-06-30 +0800
categories: [Security] # categories는 최대 2개까지 가능
tags: [Security, AES, CBC, 대칭키알고리즘] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# Programming Aes Cbc In Java
<img width="844" alt="스크린샷 2020-04-03 오후 2 15 10" src="https://user-images.githubusercontent.com/44339530/78326500-89d21b80-75b5-11ea-9bef-9e903ec00fb5.png"><br>
<img width="844" alt="스크린샷 2020-04-03 오후 2 15 28" src="https://user-images.githubusercontent.com/44339530/78326512-935b8380-75b5-11ea-9e1a-13c060dc5714.png"><br>

- Base64인코딩 : binary를 텍스트로 인코딩하는 Scheme
<img width="844" alt="스크린샷 2020-04-03 오후 1 46 29" src="https://user-images.githubusercontent.com/44339530/78325051-876dc280-75b1-11ea-84e6-ba45b57b2476.png"><br>

- 일반적인 binary값은 출력하면 깨질수도 있기에 a~/까지 텍스트로 인코딩하는 방식<br>
- 64진수 ->6비트로 자름<br>

#### 출처 
- [https://www.javacodegeeks.com/2018/03/aes-encryption-and-decryption-in-javacbc-mode.html](https://www.javacodegeeks.com/2018/03/aes-encryption-and-decryption-in-javacbc-mode.html)
- [https://www.devglan.com/online-tools/aes-encryption-decryption](https://www.devglan.com/online-tools/aes-encryption-decryption)