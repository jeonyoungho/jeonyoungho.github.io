---
title: "Message Queue"
date: 2021-06-30 +0800
categories: [DevOps, MessageQueue]
tags: [messagequeue]
toc: true
comments: true
---

# MessageQueue
- 프로세스(프로그램)간에 데이터를 교환할 때 사용하는 통신 방법 중에 하나
- MOM(Message Oriented Middleware: 비동기 메시지를 사용하는 프로그램 간의 데이터 송수신을 의미)을 구현한 시스템
- 작업을 늦출 수 있는 유연성을 제공
- 메시지를 교환할때 AMQP(Advanced Message Queuing Protocol: MQ의 오픈소스에 기반한 표준 프로토콜)을 이용
- kafka, RabbitMQ, Active MQ, AWS SQS, Java JMS등이 존재

# MessageQueue를 사용해야 하는 이유
- 프로세스간의 연관이 많아지는 구조에서(SOA, MSA등) 서비스간 결합도를 낮추고, 비동기 요청, 성능, 안정성 등 여러가지 이점이 있어서 메시징 시스템을 사용하게 된다.

# MessageQueue의 장점
- 비동기 : Queue에 넣기 때문에 나중에 처리할 수 있음(각 서비스는 비동기 방식으로 메시지를 보내기만 하면, Message Broker에서 순서 보장, 메시지 전송 보장등을 처리)
- 비동조 : Application과 분리할 수 있음(서비스간의 결합성이 낮아지므로 각자의 비즈니스 로직에만 집중할 수 있음)
- 탄력성 : 일부가 실패 시 전체는 영향을 받지 않습니다(메시징 시스템이 잠깐 다운되어도 각 서비스에 직접적인 영향을 미치지 않음)
- 과잉 : 실패할 경우 재실행이 가능합니다
- 확장성 : 다수의 프로세스들이 큐에 메시지를 보낼 수 있습니다.

# MessageQueue의 단점
- 함수 호출, 공유 메모리 사용 방식 보다 메시징 시스템을 사용했을 때 호출 구간이 늘어나므로 <b>네트워크 비용 발생</b>
- Message Broker 구축, 예를 들면 kafka 클러스터 구축에 필요한 금전, 인적자원에 대한 비용

# 메시지 큐 사용처
- 다른 곳의 API로부터 데이터 송수신
- 다양한 Application에서 비동기 통신 가능
- 이메일 발송 및 문서 업로드 가능
- 많은 양의 프로세스 처리

# MessageQueue를 사용하면 좋은 상황
- Failover
    - 서버간 데이터를 주고 받을 때 항상 시스템 장애를 염두해두어야 하는데, 서버에 문제나 네트워크의 문제에 대하여 Failover 할 수 있음
- 서버 부하가 많은 작업
    - 서버 부하가 많은 경우 원하는 만큼의 메시지를 소비하는 것이 가능함
- 부하 분산에 용이
    - 한 큐에 여러 consumer가 존재할 수 있기 때문에 부하 분산이 가능하고, Scaling 대응에 적합함
- 데이터 손실 방지
    - consumer가 broker에 처리 완료에 대한 응답을 주지 않은 경우(메시지 처리 중 문제가 발생하는 경우) 브로커는 큐에 메시지를 다시 넣어 처리할 수 있게 할 수 있음

#### 출처
- [https://velog.io/@ppageugeu90/Message-Queue%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0](https://velog.io/@ppageugeu90/Message-Queue%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0)
- [https://steady-snail.tistory.com/165](https://steady-snail.tistory.com/165)