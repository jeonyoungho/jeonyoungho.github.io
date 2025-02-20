---
title: "Kafka 기반 대규모 데이터 동시성 최적화: Request-Reply 패턴 활용 사례"
date: 2025-02-17 +0800
categories: [Backend]
tags: [woowacon24]
toc: true
comments: true
---

> 우아콘24의 ['Kafka 기반 대규모 데이터 동시성 최적화: Request-Reply 패턴 활용 사례'](https://www.youtube.com/watch?v=Rcow99TIMmc&list=PLgXGHBqgT2Tu7H-ita_W0IHospr64ON_a&index=5)' 에 대한 학습 내용을 정리하기 위한 목적의 TIL 포스팅입니다.🙆‍♂️

# 도입 배경
- 주문이 급증하는 지역에 소수의 라이더가 존재한다고 가정했을때 고객들의 배송이 지연되는 현상이 발생하게 됨
- 이때 시스템에선 '주문수 대비 라이더가 너무 부족해!' 라는 경고를 띄우게 됨
- 이 경고를 해결하고자 '주문 유입량 조절'이라는 기능을 사용함
  - 주문량을 줄이기 위해 주문 배달 반경을 일시적으로 축소하는 것
- '주문 유입량 조절' 동시 발생 가능한 N개의 트리거가 존재함
  - 1)주문수 대비 라이더 부족
  - 2)기상 악화
  - 3)장애 상황 감지
- 주문 유입량을 조절해야 하는 대상은 가게수 수십만개 이상
- N개의 트리거로 수십만개의 가게가 동시에 변경되면?
  - "동시에 변경되면?" => 여러 프로세스가 디비의 같은 컬럼을 동시에 변경하게 되면?
  - 트리거 1, 2, 3 이 동시에 실행되서 같은 컬럼을 수정하게 되면 "Deadlock found when trying to get lock, Lock wait timout exceeded" 에러를 마주하게 됨
  - 이때 DB를 활용한 낙관락 or 비관락, Redis를 활용한 분산락을 활용하여 해결할 수 있음
- 하지만 트리거의 순서를 보장하여 실행되어야함
  - '주문수 대비 라이더 부족' 트리거와 '장애 상황 감지' 트리거가 동시에 실행되면 순차적으로 실행되어야함
- 단순 락을 걸면 정확한 순서 보장이 가능하지 않음
  - 동시성이 증가할수록 락 획득의 경합이 심화되며, 요청의 순서보장이 복잡해짐
- 요청에 대한 처리 결과가 필요함
  - 왜? => 실패했을 경우 추가(수동) 조치를 할 수 있어야 하기 때문임
- 그래서 다음과 같은 세 가지 포인트를 고려하여 문제를 해결하고자 하였음
  - 1) 동시성 이슈
  - 2) 순서 보장
  - 3) 처리 결과

# 해결 과정
- 어떻게 해결할까? 락을 통해? => 카프카를 통해 해결하자!
  - 동일한 키에 대해 동일 파티션으로 분배되고 그 파티션 안에서는 순서가 보장되기에 '동시성 이슈'와 '순서 보장' 문제를 해결할 수 있다!
- 카프카를 이용한다면 처리 결과는 어떻게 받아보지?
- Enterprise Integration Patterns(엔터프라이즈 시스템 통합시 발생하는 일반적인 문제들에 대한 표준 해결책을 패턴화한것, 기업 통합 패턴이라는 번역서도 있음)의 Request-Reply pattern에 대해 인사이트를 얻게 되었음

<img width="807" alt="Image" src="https://github.com/user-attachments/assets/f289484f-3608-410b-89ff-1d6e61b24486" />

- **요청자가 요청 메시지를 요청 채널로 보내면 응답자는 그 채널을 수신해서 응답 메시지를 만들어 응답 채널로 보내게되면 결론적으로 요청자는 요청에 대한 결과를 응답 채널로부터 받아볼 수 있는 구조임**
- Request-Reply 패턴과 카프카를 결합하여 문제를 해결하였음
- 스프링에서도 Request-Reply 메시지로 [ReplyingKafkaTemplate](https://docs.spring.io/spring-kafka/reference/kafka/sending-messages.html#replying-template)이라는 클래스를 제공해주고 있었음
- 결론적으로 `ReplyingKafkaTemplate`을 활용하여 '동시성 이슈', '순서 보장', '처리 결과'에 대한 포인트를 만족시키며 주어진 문제를 해결할 수 있었음

<img width="781" alt="Image" src="https://github.com/user-attachments/assets/c74afdbe-736b-4279-8ba1-99bb35cba09d" />

- 레디스를 활용한 분산락과는 위와 같은 차이점이 존재하였음
- 실제 적용 과정은 [여기](https://youtu.be/Rcow99TIMmc?si=mlaKu1-jPKJIvYbi&t=745)서부터 참고하면 됨