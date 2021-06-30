---
title: "[개발자 블로그] LoadBalancer(로드 밸런서)" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-06-30 +0800
categories: [Backend] # categories는 최대 2개까지 가능
tags: [분산 서버 처리, Loadbalancer] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

## 로드 밸러서란?
- 부하 분산을 위해 가상 IP를 통해 각 서버에 Request를 분배하는 기능
- 즉, 대용량 트래픽을 장애없이 처라히기 위해 여러 대의 서버에 적절히 트래픽을 분배해주는 기능<br>

#### 로드 밸런서의 예시
![1](https://user-images.githubusercontent.com/44339530/114534656-3398bd80-9c8a-11eb-8366-67d2d3413fde.png)<br>
- 다음 그림과 같이 Request A~F는 로드 밸런서 알고리즘에 의해 적절하게 분배된다.
- 만약 라운드 로빈 방식으로 분배된다하면 다음과 같이 분배된다.<br>
![2](https://user-images.githubusercontent.com/44339530/114535042-99854500-9c8a-11eb-8c20-263fcc741830.png)
