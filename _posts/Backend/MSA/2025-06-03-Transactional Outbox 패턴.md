---
title: "Transactional Outbox 패턴"
date: 2025-06-03 +0800
categories: [Backend, MSA]
tags: [transactionaloutboxpattern, kafka, cdc, debezium]
toc: true
comments: true
---

이벤트 기반 아키텍처를 구현할때 고민해야될 포인트는 DB 트랜잭션과 이벤트(메시지) 발행의 원자성 보장이다. 

이에 대한 해결 방법으로 Transactional Outbox 패턴에 대해 정리해보자.

# Transactional Outbox 패턴이란?
**이벤트 기반 아키텍처를 적용할때 로컬 트랜잭션과 이벤트(메시지) 발행 간의 원자성 보장 문제를 해결하기 위한 패턴이다.**

DB와 메시지 브로커는 각각 독립적인 시스템으로 트랜잭션을 통합하는 것이 불가능하다.

예를 들어, 커머스 환경에서 Order 마이크로서비스에서 주문이 발생하면 메시지 브로커로 이벤트를 발행하고 Stock 마이크로서비스에서 관련된 상품의 재고를 차감한다고 가정해보자.

![Image](/assets/img/posts/Backend/MSA/TransactionalOutboxPattern_MessageBroker장애.png)

이때 Order 마이크로서비스에서 주문 생성 DB 트랜잭션은 성공했으나, MessageBroker의 셧다운이 발생하거나 네트워크 문제로 인해 메시지 발행이 실패하게 된다면 메시지가 유실될 것이고 두 마이크로서비스간의 데이터 일관성이 깨지게 될 것이다.

`Transactional Outbox 패턴` 은 위와 같은 문제에서 메시지 유실 문제를 해결하여 두 서비스간의 데이터 일관성을 보장할 수 있게 된다.

![Image](/assets/img/posts/Backend/MSA/TransactionalOutboxPattern_ReliablePublication.png)
_출처: https://microservices.io/patterns/data/transactional-outbox.html_

Outbox는 주로 웹 메일에서 ‘보내는 중이거나 보내기를 대기 중인 메일들이 임시로 저장되는 폴더’를 의미한다. 즉, 메시지 브로커로 발행할 메시지들을 로컬 트랜잭션내에서 outbox 테이블에 저장함으로써 메시지 유실을 방지하는 방법이다.

Transactional Outbox 패턴의 구성 요소는 다음과 같다.

- Sender - 메시지를 보내는 서비스
- Database - 엔티티 및 메시지 outbox를 저장하는 데이터베이스
- Message Outbox - 관계형 데이터베이스인 경우 보낼 메시지를 저장하는 테이블
- Message Relay - outbox에 저장된 메시지를 메시지 브로커로 보내는 서비스(프로세스)

# Transactional Outbox 패턴 구현 방식
Transactional Outbox 패턴을 구현하는 대표적으로 방식은 [Polling Publisher](https://microservices.io/patterns/data/polling-publisher.html) 와 [Transaction Log Tailing](https://microservices.io/patterns/data/transaction-log-tailing.html)이 있다.

## Polling Publisher
DB 트랜잭션이 실행될때 outbox 테이블에 발행될 메시지를 저장후 메시지 브로커에 발행하는 방식이다.

## Transaction Log Tailing
DBMS 마다 트랜잭션이 처리되면 log(예를 들어 MySQL의 경우 binlog)를 생성하게 되는데, 해당 log에 대한 CDC(Change Data Capture)를 구현하는 것이다. 주로 [Debezium](https://debezium.io/)과 같은 CDC를 구현하는 오픈소스를 활용해서 구현한다.

> CDC란 데이터베이스에서 발생하는 변경 사항(INSERT, UPDATE, DELETE)을 실시간으로 캡쳐하여 이벤트 형태로 다른 시스템으로 전송하는 기술이다.

## Polling Publisher 와 Transaction Log Tailing 비교
Polling Publisher 방식의 장점은 좀 더 단순한 구조로 구현하기 간편하며, outbox 메시지의 상태를 관리하여 장애 복구시 간편하다는 것이다. 하지만 비교적 높은 비용의 polling이 DB 부하로 이어질수도 있다는 단점이 존재한다. 


Transaction Log Tailing 방식은 DB polling으로 인한 부하가 줄어들게되며 실시간성과 높은 처리량을 보장할 수 있다는 장점이 존재한다. 반면, MySQL binlog에 대해 CDC를 구현하고 그 결과를 바탕으로 Kafka consumer에서 사용하는 메시지 포맷으로 데이터를 생성하는 작업이 필요하다보니 CDC 도구를 학습하고 운영하는 비용과 CDC 도구에서 생성하는 메시지의 schema 를 관리하는 비용이 발생한다는 단점이 존재한다. 또한 장애 시 복구 지점 관리 등이 좀 더 복잡하다.


따라서, 팀과 프로젝트 상황을 고려하여 적절한 방식을 선택하여 구현해야 한다.

> 두 방식의 비교와 관련하여 [29CM의 트랜잭셔널 아웃박스 패턴의 실제 구현 사례 포스팅](https://medium.com/@greg.shiny82/트랜잭셔널-아웃박스-패턴의-실제-구현-사례-29cm-0f822fc23edb)을 참고해보면 좋다😃

# Polling Publisher 방식 구현 예시
outbox에 저장된 메시지를 메시지 브로커로 발행하는 두 가지 예시를 살펴보자.

## 1. Spring의 outbox 테이블 polling
로컬 트랜잭션내에 저장된 outbox 메시지를 주기적인 DB 폴링으로 메시지를 발행하는 방식이다.

```java
@Service
@Transactional(readOnly = true)
@RequiredArgsConstructor
public class OrderService {

    private final OrderRepository orderRepository;

    @Transactional
    public Long create(Long memberId, Long productId) {
        Order savedOrder = orderRepository.save(Order.create(memberId, productId));

        Long savedOrderId = savedOrder.getId();

        Events.raise(OrderCreatedEvent.create(savedOrderId));

        return savedOrder.getId();
    }
}

@Slf4j
@Component
@RequiredArgsConstructor
public class OrderEventListener {

    @Value("${spring.kafka.topic.order-event}")
    private String orderEventTopic;

    private final OutboxEventRepository outboxEventRepository;
    private final KafkaJsonConverter kafkaJsonConverter;

    @TransactionalEventListener(phase = TransactionPhase.BEFORE_COMMIT)
    public void listen(OrderEvent event) {
        outboxEventRepository.save(OutboxEvent.create(orderEventTopic,
                                                      event.getOrderId().toString(),
                                                      kafkaJsonConverter.serialize(event)));
    }
}

@Slf4j
@Component
@RequiredArgsConstructor
public class OutboxEventRelayProcessor {
    ...
    @Scheduled(initialDelay = 5000, fixedDelay = 5000)
    public void sendOutboxEvents() {
        Pageable pageable = PageRequest.of(0, BATCH_SIZE);

        List<OutboxEvent> outboxEvents = outboxEventRepository.findByStatusOrderByIdAsc(Status.WAIT, pageable);
        // 1. OutboxEvent 상태 업데이트 (PROCESSING)
        // 2. 메시지 발행
        // 3. OutboxEvent 상태 업데이트 (SUCCESS or FAIL)
    }
}
```

위 방식의 장점은 다음과 같다.
- 단순한 구조로 쉽게 구현 가능하며 별도 프로세스를 관리하는 비용이 줄어든다.
- 배치 처리 최적화: 한 번에 여러 메시지를 일괄 처리하여 네트워크 오버헤드를 줄일 수 있다.
- 모니터링 용이: 미처리 메시지 개수, 처리량 등을 쉽게 추적할 수 있습니다

반면 단점은 다음과 같다.
- 지연 시간: 폴링 주기만큼 메시지 발행이 지연됩니다 (예: 5초 폴링 시 최대 5초 지연)
- DB 부하: 주기적인 DB 조회 및 업데이트로 인한 부하가 발생한다.
- 리소스 낭비: 처리할 메시지가 없어도 계속 폴링하므로 불필요한 리소스가 소모된다.


그리고 위 예시는 동일 프로세스내에서 메시지 릴레이가 구성되다보니 애플리케이션과 메시지 릴레이의 장애가 서로 영향을 줄 수 있다는 점에 유의해야 한다. 만약 Transactional Outbox 패턴에서 권장하는것처럼 메시지 릴레이를 별도 프로세스로 구성한다면 아래와 같은 방법들을 고려할 수 있다.
- EC2 또는 Fargate 인스턴스를 활용한 단일 프로세스 구성
- AWS EventBridge + Lambda 를 활용한 서버리스 아키텍처 구성

별도 프로세스로 구성하게될 경우 추가적인 프로세스를 관리하는 비용이 들게 되지만 다음과 같은 장점들을 얻을 수 있다.
- 장애 격리: 애플리케이션과 메시지 릴레이가 독립적으로 동작하며 한 서비스의 장애가 다른 서비스에 영향을 주지않게됨.
- 운영 유연성: 메시지 릴레이의 수정이 필요할 경우 독립적으로 수정하여 배포하면됨.


이러한 세부적인 구현 세부사항은 팀과 프로젝트 상황에 따라 적절하게 선택하여 적용하는 것이 중요하다.

## 2. DB 트랜잭션 완료후 outbox 에 저장된 메시지 즉시 발행
로컬 트랜잭션내에 저장된 outbox 메시지를 트랜잭션 완료후 즉시 메시지 브로커로 발행함으로써 주기적인 DB 폴링의 부하 없앨 수 있다.

```java
@Slf4j
@Component
@RequiredArgsConstructor
public class OrderEventListener {

    ...

    @TransactionalEventListener(phase = TransactionPhase.BEFORE_COMMIT)
    public void handleBeforeCommit(OrderEvent event) {
        outboxEventRepository.save(OutboxEvent.create(event.getOutboxEventId(),
                                                      orderEventTopic,
                                                      event.getOrderId().toString(),
                                                      kafkaJsonConverter.serialize(event)));
    }

    @Async(value = OUTBOX_EVENT_TASK_EXECUTOR)
    @TransactionalEventListener(phase = TransactionPhase.AFTER_COMMIT)
    public void handleAfterCommit(OrderEvent event) {
        outboxEventRelayProcessor.process(event.getOutboxEventId());
    }
}
```

위 방식의 장점은 다음과 같다.
- DB 폴링의 부하가 없다.
- 실시간성을 보장하는 메시지 발행이 가능해져 지연 시간이 최소화 된다.
- 필요할 때만 메시지를 발행하므로 불필요한 리소스 낭비가 줄어든다.

반면 단점은 다음과 같다.
- `@Async` 처리를 위한 스레드 풀 설정과 관리가 필요하다.(톰캣의 기본 스레드풀로 처리하게 되면 클라이언트의 응답이 그만큼 지연된다, 네트워크 장애나 브로커 문제로 인해 큰 영향을 미칠 수도 있다)
- 이벤트 발생량에 따른 비동기 작업 큐의 메모리 사용량 변동이 크고 예측하기 어렵다.
- 현재 몇 개의 메시지가 비동기로 처리 대기 중인지 파악하기 어렵다.

## outbox 메시지 재시도
별도 배치 로직을 구현하여 실패한 이벤트들의 재시도를 고려해볼 수 있다.

# 실무 적용 사례
**Polling Publisher 방식**
- RIDI: [https://ridicorp.com/story/transactional-outbox-pattern-ridi/](https://ridicorp.com/story/transactional-outbox-pattern-ridi/)
- 29CM: [https://medium.com/@greg.shiny82/트랜잭셔널-아웃박스-패턴의-실제-구현-사례-29cm-0f822fc23edb](https://medium.com/@greg.shiny82/트랜잭셔널-아웃박스-패턴의-실제-구현-사례-29cm-0f822fc23edb)

**Transaction Log Tailing 방식**
- 배민(Debezium): [https://techblog.woowahan.com/17386/](https://techblog.woowahan.com/17386/)

# 정리
- 이벤트 기반 아키텍처를 구현할때 DB 트랜잭션과 이벤트(메시지) 발행의 원자성 문제는 Transactional Outbox 패턴을 적용하여 해결할 수 있다.
- Transactional Outbox 패턴은 Polling Publisher 방식과 Transaction Log Tailing 방식 두 가지가 존재한다.
- Polling Publisher 방식은 outbox 테이블을 구성하여 발행될 메시지를 같은 DB 트랜잭션으로 영속화시켜둠으로써, 메시지 유실을 방지하는 기법이다.
- Transaction Log Tailing은 DB의 binary log를 활용하여 CDC(Change Data Capture)를 구현하는 것이다. Debezium과 같은 오픈 소스가 주로 사용된다.
- Polling Publisher 방식과 Transaction Log Tailing 방식의 장단점을 고려하여 팀과 프로젝트 상황에 적합한 방식으로 적절하게 선택하는 것이 중요하다.