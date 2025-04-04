---
title: "[도메인주도개발시작하기] Chapter11-CQRS"
date: 2023-06-17 +0800
categories: [책, 도메인주도개발시작하기]
tags: [ddd, dddstart]
toc: true
comments: true
published: false
---

## 11. 1 단일 모델의 단점
- 주문 내역 조회 기능을 구현하려면 여러 애그리거트에서 데이터를 가져와야 한다.
- Order 에서 주문 정보를 가져와야 하고, Product 에서 상품 이름을 가져와야 하고, Member 에서 회원 이름과 ID를 가져와야 한다.

![KakaoTalk_Photo_2023-06-17-18-43-19](https://github.com/jeonyoungho/jeonyoungho.github.io/assets/44339530/2fb1ad6e-689c-4c32-b677-4902dc9a7284)

- 조회 화면 특성상 조회 속도가 빠를수록 좋은데 여러 애그리거트의 데이터가 필요하면 구현 방법을 고민해야 한다.
- 3장에서 언급한 식별자를 이용해서 애그리거트를 참조하는 방식을 사용하면 즉시 로딩 방식과 같은 JPA의 쿼리 관련 최적화 기능을 사용할 수 없다.
  - 이는 한 번의 SELECT 쿼리로 조회 화면에 필요한 데이터를 읽어올 수 없어 조회 성능에 문제가 생길 수 있다.

- 애그리거트 간 연관을 식별자가 아닌 직접 참조하는 방식으로 연결해도 고민거리가 생긴다.
- 조회 화면 특성에 따라 같은 연관도 즉시 로딩이나 지연 로딩으로 처리해야 하기 때문이다.
- 조회 기능을 구현할 때 DBMS 가 제공하는 전용 기능이 필요하면 JPA의 네이티브 쿼리를 사용해야 할 수도 있다.

- 이런 고민이 발생하는 이유는 시스템 상태를 변경할 때와 조회할 때 단일 도메인 모델을 사용하기 떄문이다.
- 객체 지향으로 도메인 모델을 구현할 때 주로 사용하는 ORM 기법은 Order#cancel() 이나 Order#changeShippingInfo() 기능처럼 도메인 상태 변경 기능을 구현하는데는 적합하지만 주문 상세 조회 화면처럼 여러 애그리거트에서 데이터를 가져와 출력하는 기능을 구현하기엔 고려할게 많아서 구현을 복잡하게 만드는 원인이 된다.

- 이런 구현 복잡도를 낮추는 간단한 방법은 상태 변경을 위한 모델과 조회를 위한 모델을 분리하는 것이다.

> **Note**: CQRS 에 대한 내용을 저자가 영상으로도 정리한 것이 있다. [유튜브 영상](https://youtu.be/xf0kXMTFJm8)을 함께 보면 CQRS 를 이해하는데 도움이 될 것이다.

## 11.2 CQRS
- 시스템이 제공하는 기능은 크게 두 가지로 나눌 수 있다.
  - 하나는 상태를 변경하는 기능이다. (ex. 주문 생성, 배송지 정보 변경, 회원 암호 변경)
  - 하나는 사용자 입장에서 상태 정보를 조회하는 기능이다. (ex. 주문 상세 내역 보기, 게시글 목록 보기, 회원 정보 보기, 판매 통계 보기)
  
- 도메인 모델 관점에선 상태 변경 기능은 주로 한 애그리거트의 상태를 변경한다.
  - 예를 들어 주문 취소 기능과 배송지 정보 변경 기능은 한 개의 Order 애그리거트를 변경한다.
- 반면에 조회 기능에 필요한 데이터를 표시하려면 두 개 이상의 애그리거트가 필요할 때가 많다.(앞서 살펴본 주문 상세 조회 기능이 이에 해당)

- <b>상태를 변경하는 범위와 상태를 조회하는 범위가 정확하게 일치하지 않기 떄문에 단일 모델로 두 종류의 기능을 구현하면 모델이 불필요하게 복잡해진다.</b>
- <b>단일 모델을 사용할 때 발생하는 복잡도를 해결하기 위해 사용하는 방법이 바로 CQRS다.</b>

- CQRS 는 Command Query Responsibility Segregation 의 약자로 아래 이미지와 같이 `상태를 변경하는 명령 모델`과 `상태를 제공하는 조회를 위한 모델`을 분리하는 패턴이다.

![image](https://github.com/jeonyoungho/jeonyoungho.github.io/assets/44339530/278b98f7-cec5-45d1-8ec5-6005bbc3bc55)

- CQRS 는 복잡한 도메인에 적합하다.
- 도메인이 복잡할수록 명령 기능과 조회 기능이 다루는 데이터 범위에 차이가 난다.
- <b>이 두 기능을 단일 모델로 처리하면 조회 기능의 로딩 속도를 위해 모델 구현이 필요 이상으로 복잡해진다.</b>
- 예를 들어, 온라인 쇼핑에서 다양한 차원에서 주문/판매 통계를 조회해야 한다고 해보자.
- JPA 기반 단일 도메인 모델을 사용하면 통계 값을 빠르게 조회하기 위해 JPA와 관련된 다양한 성능 관련 기능을 모델에 적용해야 한다.
- 이런 도메인에 CQRS를 적용하면 통계를 위한 조회 모델을 별도로 만들기 때문에 조회 기능 때문에 도메인 모델이 복잡해지는 것을 막을 수 있다.

- CQRS 를 사용하면 각 모델에 맞는 구현 기술을 선택할 수 있다.
  - 예를 들어 명령 모델은 객체 지향에 기반해서 도메인 모델을 구현하기에 적당한 JPA를 사용해서 구현하고,
  - 조회 모델은 DB 테이블에서 SQL 로 데이터를 조회할 때 좋은 마이바티스를 사용해서 구현하면 된다.

![image](https://github.com/jeonyoungho/jeonyoungho.github.io/assets/44339530/2b3c823c-3e6a-4c7e-80e2-04f00354b550)

- 위 이미지를 보면 조회 모델에는 응용 서비스가 존재하지 않는다.
- 단순히 데이터를 읽어와 조회하는 기능은 응용 로직이 복잡하지 않기 떄문에 컨트롤러에서 바로 DAO를 실행해도 무방하다.
- 물론 데이터를 표현 영역에 전달하는 과정에서 몇 가지 로직이 필요하다면 응용 서비스를 두고 로직을 구현하면 된다.

- <b>아래 이미지는 명령 모델과 조회 모델의 설계 예를 보여준다.</b>

![image](https://github.com/jeonyoungho/jeonyoungho.github.io/assets/44339530/dadbbe1b-2517-43c6-a838-29da4ab6c02e)

- 상태 변경을 위한 명령 모델은 객체를 기반으로 한 도메인 모델을 이용해서 구현했다.
- 반면 조회 모델은 주문 요약 목록을 제공할 때 필요한 정보를 담고 있는 데이터 타입을 이용한다.
- <b>두 모델 모두 주문과 관련되어 있지만 명령 모델은 상태를 변경하는 도메인 로직을 수행하는데 초점을 맞춰 설계했고, 조회 모델은 화면 보여줄 데이터를 조회하는데 초점을 맞춰 설계했다.</b>

- 명령 모델과 조회 모델이 같은 구현 기술을 사용할 수도 있다. 이에 대한 내용은 이미 5장에서 다룬 바 있다.
  - 5장에서 JPQL을 이용한 `동적 인스턴스 생성`과 `하이버네이트의 @Subselect` 를 이용하는 방법을 설명했는데, <b>여기서 동적 인스턴스로 사용할 클래스와 @Subselect 를 적용한 클래스가 조회 모델에 해당한다.</b>

- <b>아래 이미지처럼 명령 모델과 조회 모델이 서로 다른 데이터 저장소를 사용할 수도 있다.</b>
  - 명령 모델은 트랜잭션을 지원하는 RDBMS를 사용하고, 조회 모델은 조회 성능이 좋은 메모리 기반 NoSQL을 사용할 수 있을 것이다.

![image](https://github.com/jeonyoungho/jeonyoungho.github.io/assets/44339530/c0be5ddc-2dad-43b0-866a-a37691f3c0b7)

- <b>두 데이터 저장소 간 데이터 동기화는 10장에서 배운 이벤트를 활용해서 처리한다.</b>
- <b>명령 모델에서 상태를 변경하면 이에 해당하는 이벤트가 발생하고, 그 이벤트를 조회 모델에 전달해서 변경 내역을 반영하면 된다.</b>

- <b>명령 모델과 조회 모델이 서로 다른 데이터 저장소를 사용할 경우 데이터 동기화 시점에 따라 구현 방식이 달라질 수 있다.</b>
  - <b>명령 모델에서 데이터가 바뀌자마자 변경 내역을 바로 조회 모델에 반영해야 한다면 동기 이벤트와 글로벌 트랜잭션을 사용해서 실시간으로 동기화할 수 있다.</b>
  - <b>하지만 10장에서 언급한 것처럼 동기 이벤트와 글로벌 트랜잭션을 사용하면 전반적인 성능(응답 속도와 처리량)이 떨어지는 단점이 있다.</b>

- <b>만약 서로 다른 저장소의 데이터를 특정 시간 안에만 동기화해도 된다면 비동기로 데이터를 전송하면 된다.</b>
  - 예를 들어, 통계 처리 목적으로 조회 전용 저장소를 구축했다고 하자.
  - 통계 데이터는 수초, 수분, 또는 1시간 단위로 최근 데이터를 반영해도 문제가 되지 않을 때가 많다.
  - 이런 경우라면 비동기로 데이터를 보냄으로써 데이터 동기화로 인해 명령 모델의 성능이 나빠지지 않도록 할 수 있다.

> **Note**: CQRS 패턴을 적용하기 위해 사용해야 할 필수 기술이 따로 존재하는 것은 아니다. 5장에서 언급한 것처럼 JPA만 사용해서 명령 모델과 조회 모델을 구현할 수도 있다. 명령 모델은 JPA로 구현하고 조회 모델은 직접 SQL을 사용해서 구현할 수도 있다.

## 11.2.1 웹과 CQRS
- 일반적인 웹 서비스는 상태를 변경하는 요청보다 상태를 조회하는 요청이 많다. 
  - 온라인 쇼핑몰을 예로 들면 주문 요청보단 카탈로그를 조회하고 상품의 상세 정보를 조회하는 요청이 비교할 수 없을 정도로 많다.
  - 게시판도 한 번 등록한 게시글을 수십에서 수천 내지 수백만 조회한다.

- 포털이나 대형 온라인 쇼핑몰과 같이 조회 기능 요청 비율이 월등히 높은 서비스를 만드는 개발팀은 조회 성능을 높이기 위해 다양한 기법을 사용한다.
  - 기본적으로 쿼리를 최적화해서 쿼리 실행 속도 자체를 높이고, 메모리에 조회 데이터를 캐싱해서 응답 속도를 높이기도 한다.
  - 그리고 조회 전용 저장소를 따로 사용하기도 한다.

- <b>이렇게 조회 성능을 높이기 위해 다양한 기법을 사용하는 것은 결과적으로 CQRS 를 적용하는 것과 같은 효과를 만든다.</b>
  - <b>메모리에 캐싱하는 데이터는 DB에 보관된 데이터를 그대로 저장하기보단 화면에 맞는 모양으로 변환한 데이터를 캐싱할 때 성능에 더 유리하다.</b>
  - 즉, 조회 전용 모델을 캐시하는 것이다.
- 비슷하게 조회 속도를 높이기 위해 쿼리를 최적화한다는 것은 조회 화면에 보여줄 데이터를 빠르게 읽어올 수 있도록 쿼리를 작성하는 것이다.

- <b>대규모 트래픽이 발생하는 웹 서비스는 알게 모르게 CQRS를 적용하게 된다.</b>
  - <b>단지 명시적으로 명령 모델과 조회 모델을 구분하지 않을 뿐이다.</b>
  - <b>조회 속도를 높이기 위해 별도 처리를 하고 있따면 명싱적으로 명령 모델과 조회 모델을 구분하자.</b>
  - <b>이를 통해 조회 기능 떄문에 명령 모델이 복잡해지는 것을 막을 수 있고, 명령 모델에 관계없이 조회 기능에 특화된 구현 기법을 보다 쉽게 적용할 수 있다.</b>

## 11.2.2 CQRS 장단점

### 장점
- <b>1) 명령 모델을 구현할 떄 도메인 자체에 집중할 수 있다.</b>
  - 복잡한 도메인은 주로 상태 변경 로직이 복잡한데 명령 모델과 조회 모델을 구분하면 조회 성능을 위한 코드가 명령 모델에 없으므로 도메인 로직을 구현하는데 집중할 수 있다.
  - 또한 명령 모델에서 조회 관련 로직이 사라져 복잡도가 낮아진다.
- <b>2) 조회 성능을 향상시키는데 유리하다.</b>
  - 조회 단위로 캐시 기술을 적용할 수 있고, 조회에 특화된 쿼리를 마음대로 사용할 수도 있다.
  - 캐시뿐만 아니라 조회 전용 저장소를 사용하면 조회 처리량을 대폭 늘릴 수도 있다.
  - 조회 전용 모델을 사용하기 때문에 조회 성능을 높이기 위한 코드가 명령 모델에 영향을 주지 않는다.

![KakaoTalk_Photo_2023-06-17-20-16-48](https://github.com/jeonyoungho/jeonyoungho.github.io/assets/44339530/fc9ae318-a469-4d5d-8855-511e7f03f712)

### 단점
- <b>2) 구현해야 할 코드가 더 많다.</b>
  - 단일 모델을 사용할 때 발생하는 복잡함 때문에 발생하는 구현 비용과 조회 전용 모델을 만들 때 발생하는 구현 비용을 따져봐야 한다.
  - 도메인이 복잡하거나 대규모 트래픽이 발생하는 서비스라면 조회 전용 모델을 만드는 것이 향후 유지 보수에 유리하다.
  - 반면에 도메인이 단순하거나 트래픽이 많지 않은 서비스라면 조회 전용 모델을 따로 만들때 얻을 이점이 있는지 따져봐야 한다.
- <b>1) 더 많은 구현 기술이 필요하다.</b>
  - 명령 모델과 조회 모델을 다른 구현 기술을 사용해서 구현하기도 하고 경우에 따라 다른 저장소를 사용하기도 한다.
  - 또한 데이터 동기화를 위해 메시징 시스템을 도입해야 할 수도 있다.

<b>위와 같은 장단점을 고려해서 CQRS 패턴을 도입할지 여부를 결정해야 한다. 도메인이 복잡하지 않은데 CQRS를 도입하면 두 모델을 유지하는 비용만 높아지고 얻을 수 있는 이점은 없다. 반면에 트래픽이 높은 서비스인데 단일 모델을 고집하면 유지 보수 비용이 오히려 높아질 수 있으므로 CQRS 도입을 고려하자.</b>

## Reference
- 예제 코드 및 이미지
  - [https://sangjaeoh.github.io/books/%EB%8F%84%EB%A9%94%EC%9D%B8%EC%A3%BC%EB%8F%84%EA%B0%9C%EB%B0%9C%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0/#11-cqrs](https://sangjaeoh.github.io/books/%EB%8F%84%EB%A9%94%EC%9D%B8%EC%A3%BC%EB%8F%84%EA%B0%9C%EB%B0%9C%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0/#11-cqrs)