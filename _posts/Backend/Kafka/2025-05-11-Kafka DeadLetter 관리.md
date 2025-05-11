---
title: "Kafka DeadLetter 관리"
date: 2025-05-11 +0800
categories: [Backend, Kafka]
tags: [kafka, deadletter]
toc: true
comments: true
---

MSA 환경에서 이벤트 기반 아키텍처(EDA)를 적용하여 두 서비스 간에 데이터를 동기화할때 Kafka를 주로 사용된다.

카프카 Consumer 메시지 처리 실패시 dead-letter를 어떻게 관리하고 재시도 전략을 수립하면 좋을지 깊게 고찰해보자.


(잘못된 내용 및 피드백은 코멘트로 남겨주시면 최대한 빠르게 확인해보겠습니다😃)

# DeadLetterPublishingRecoverer, DefaultErrorHandler를 활용한 지수 백오프 알고리즘 기반 재처리
spring-kafka에서 제공하는 DeadLetterPublishingRecoverer와 DefaultErrorHandler를 활용하여 지수백오프 알고리즘 기반으로 최대 3회까지 재시도를 할 수 있다.

```java
@Bean
public DeadLetterPublishingRecoverer deadLetterPublishingRecoverer() {
    return new DeadLetterPublishingRecoverer(kafkaTemplate(), (r, e) -> new TopicPartition(deadLetterTopic, 0));
}

@Bean
public DefaultErrorHandler defaultErrorHandler() {
    ExponentialBackOff backOff = new ExponentialBackOff();
    backOff.setMaxAttempts(3); // 최대 3회 시도
    backOff.setInitialInterval(1000L); // 1초 뒤부터 시작
    backOff.setMultiplier(2.0); // 2배수
    backOff.setMaxInterval(10000L); // 최대 10초

    return new DefaultErrorHandler(deadLetterPublishingRecoverer(), backOff);
}
```

**하지만 해당 위 방안은 몇 가지 문제가 존재한다.**

# DeadLetterPublishingRecoverer, DefaultErrorHandler를 활용한 지수 백오프 알고리즘 기반 재처리의 문제점
**재처리 과정에서 컨슈머의 이후 메시지 처리가 블로킹됨으로써 메시지 처리 지연 현상이 발생하게 된다.**

만약 프로듀서 애플리케이션에서 동시에 1000개의 메시지가 발행되었고, 컨슈머 로직의 코드결함으로 모든 메시지 처리에 문제가 발생했다고 가정해보자.

카프카 컨슈머는 하나의 dead-letter 메시지를 Retry 하는데 총 8초가 걸린다. 그렇게 될 경우 1000개의 메시지를 처리하는데 8000초가 걸릴 것이다.

예제 프로젝트의 컨슈머 코드를 다음과 같이 수정후 10개의 메시지를 발행해보았다.

```java
@KafkaListener(topics = "${spring.kafka.topic.order-event}")
public void consume(ConsumerRecord<String, KafkaOrderEventDto> consumerRecord,
                    Acknowledgment acknowledgment) {
    String topic = consumerRecord.topic();
    String key = consumerRecord.key();
    KafkaOrderEventDto value = consumerRecord.value();
    log.info("[KAFKA][CONSUMER] topic = {}, key = {}, value = {}", topic, key, value);

    throw new RuntimeException();

//        orderEventHandler.handle(value);
//        acknowledgment.acknowledge();
}
```

결과는 아래 이미지와 같이 메시지 한 개를 처리하는데 약 8초가 소요되며 메시지 처리 지연 현상이 발생하는 것을 확인할 수 있다.

![Image](/assets/img/posts/Backend/Kafka/Kafka_DeadLetter_메시지지연_스크린샷.png)

# dead-letter 발생 및 재처리 시나리오
dead-letter 발생 및 재처리 시나리오에 대해 살펴보자.(실제 더 다양한 케이스들이 존재할 것이다) 

**1. 컨슈머 로직의 코드 결함**
컨슈머 로직의 코드 결함을 해결후 해당 메시지는 재처리가 가능할 것이다.

**2. 컨슈머 로직 정상 & Zero-Payload 방식에서 외부 서비스의 코드 결함으로 장애가 전파된 경우**
외부 서비스의 코드 결함을 해결후 해당 메시지는 재처리가 가능할 것이다.

**3. 컨슈머 로직 정상 & Zero-Payload 방식에서 외부 서비스의 슬로우 쿼리로 타임아웃이 발생한 경우**
외부 서비스의 슬로우 쿼리를 해결후 해당 메시지는 재처리가 가능할 것이다.

**4. 컨슈머 로직 정상 & Zero-Payload 방식에서 외부 서비스가 셧다운된 경우**
외부 서비스가 다시 복구된후 해당 메시지는 재처리가 가능할 것이다.

**5. 컨슈머 로직 정상 & DB 장애**
DB가 복구된후 해당 메시지는 재처리가 가능할 것이다.

위 케이스들중 1, 2, 3번은 수동적인 원인 조치 및 복구의 과정이 필요하다.

단 4, 5번의 경우엔 서버, DB의 자동 복구 메커니즘이 적용된 상태에서 해당 메시지 처리가 셧다운의 원인이 아니라면 자동 재처리를 적용해볼 수 있을것이다. 

> **Note**: 만약 컨슈머의 메시지 처리 과정에서 외부 서비스 셧다운(ex. 외부 서비스의 internal-api 에서 대용량 데이터 조회 OOM 발생) or DB 셧다운(ex. 대용량 데이터 조회 및 처리 과정에서 발생하는 DB 부하)이 발생한 것이라면 동일한 셧다운 현상이 지속해서 발생할 것이다. 하지만 Zero-Payload 방식에서는 보통 PK 기반으로 조회하기에 대용량 데이터와 관련된 문제는 크게 고려하지 않아도 괜찮지 않을까 싶다..😂

# dead-letter 관리 및 재시도 전략
위의 내용들을 고려하여 다음과 같은 2가지 dead-letter 관리 및 재시도 전략을 고려해볼 수 있을 것 같다.

## 1. 컨슈머 로직 실패시 Retry 없이 단일 dead-letter 토픽으로 메시지를 발행 및 컨슘후 별도 DB에 관리하여 개발자가 확인후 수동으로 백오피스 api를 통해 복구
dead-letter 메시지를 개발자가 확인후 수동으로 복구하는 방안이다. 개발자가 확인후 조치할 수 있도록 알림이 필수적으로 적용되야 한다.

> **Note**: 만약 애플리케이션이 사용중인 DB와 dead-letter 를 같은 스토리지에서 관리할 경우, DB 셧다운이 발생하면 dead-letter 메시지가 유실될 수 있다는 점을 유의해야 한다.

## 2. RetryTopicConfiguration 을 활용하여 Retry 할 수 있는 예외와 토픽을 식별후, 메시지 처리 지연 현상이 발생하지 않도록 비동기적으로 Retry 적용
- Retry 할 수 있는 토픽으로는 비즈니스 중요도에 따라 식별될 수 있을 것이다.
- Retry 할 수 있는 예외로는 일시적인 네트워크 문제 및 DB, 외부 서비스 셧다운 등이 있을 것이다.
- 원본 Exception 을 한 번 감싼 형태로 KafkaConsumer 계층의 예외로 감싸 던지는 구조가 될 것이다.
- `RetryTopicConfiguration`을 활용하면 메시지 처리 지연 현상을 방지하면서 비동기적으로 Retry를 적용할 수 있다.
  - [https://gisungcu.tistory.com/531](https://gisungcu.tistory.com/531)
  - [https://velog.io/@wwlee94/Kafka-재시도-DLT-빌더-접근-방식으로-리팩토링](https://velog.io/@wwlee94/Kafka-재시도-DLT-빌더-접근-방식으로-리팩토링)
  - [https://kkang-joo.tistory.com/85](https://kkang-joo.tistory.com/85)

# 정리
Kafka DeadLetter 관리 및 재처리 방안에 대해 정리해보았다.

Kafka DeadLetter Retry는 메시지 소비 지연 현상이 발생하지 않도록 유의해야하고, Retry 할 수 있는 예외를 식별후 비동기적으로 별도 스레드에서의 Retry 처리가 고려되야 한다.

