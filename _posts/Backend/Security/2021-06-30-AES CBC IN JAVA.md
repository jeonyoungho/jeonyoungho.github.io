---
title: "[보안] Java로 구현한 AES CBC"
date: 2021-06-30 +0800
categories: [Backend, Security]
tags: [security, aes, cbc]
toc: true
comments: true
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