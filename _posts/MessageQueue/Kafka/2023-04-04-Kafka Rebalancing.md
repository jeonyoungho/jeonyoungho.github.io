---
title: "[Kafka] Kafka Rebalancing" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2023-04-04 +0800
categories: [MessageQueue, Kafka] # categories는 최대 2개까지 가능
tags: [messagequeue, kafka, rebalancing] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---



#### 출처
- [https://joooootopia.tistory.com/30](https://joooootopia.tistory.com/30)
- [https://www.verica.io/blog/understanding-kafkas-consumer-group-rebalancing/](https://www.verica.io/blog/understanding-kafkas-consumer-group-rebalancing/)
- [https://techblog.gccompany.co.kr/%EC%B9%B4%ED%94%84%EC%B9%B4-%EC%BB%A8%EC%8A%88%EB%A8%B8-%EA%B7%B8%EB%A3%B9-%EB%A6%AC%EB%B0%B8%EB%9F%B0%EC%8B%B1-kafka-consumer-group-rebalancing-5d3e3b916c9e](https://techblog.gccompany.co.kr/%EC%B9%B4%ED%94%84%EC%B9%B4-%EC%BB%A8%EC%8A%88%EB%A8%B8-%EA%B7%B8%EB%A3%B9-%EB%A6%AC%EB%B0%B8%EB%9F%B0%EC%8B%B1-kafka-consumer-group-rebalancing-5d3e3b916c9e)
- [https://medium.com/lydtech-consulting/kafka-consumer-group-rebalance-1-of-2-7a3e00aa3bb4](https://medium.com/lydtech-consulting/kafka-consumer-group-rebalance-1-of-2-7a3e00aa3bb4)