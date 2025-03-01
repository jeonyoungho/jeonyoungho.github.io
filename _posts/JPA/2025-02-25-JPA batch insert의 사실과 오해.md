---
title: "Spring Data JPA batch insert 의 사실과 오해"
date: 2025-02-25 +0800
categories: [JPA]
tags: [jpa, springdatajpa, batchinsert, mariadb]
toc: true
comments: true
---

SpringBoot + JPA + MariaDB(10.6 이상) 환경에서 JPA 엔티티의 ID 생성 전략을 IDENDITY를 적용하면 batch insert를 사용할 수 없다는 내용은 구글링을 통해 쉽게 찾을수 있다.

- IDENTITY 전략은 auto-increment로 PK 값을 자동으로 DB 에 의존하여 채번하는 방식이다.
- JPA 엔티티를 persist 하려면 @Id로 지정한 필드에 값이 필요한데, IDENTITY 타입은 실제 DB에 insert를 해야만 값을 얻을 수 있기 때문에 batch 처리가 불가능하다.

이를 해결하기 위해 두 가지 방식이 존재한다.

- 1)JdbcTemplate을 활용한 구현
- 2)ID 생성 전략을 Sequence, Table로 적용

**이때 JPA 엔티티의 ID 생성 전략을 Sequence 방식으로 batch insert를 적용하는 과정에서 오해할 수 있을만한 내용들을 정리해보고자 한다.**

실습 프로젝트는 [여기](https://github.com/jeonyoungho/springboot-practice)를 참고하기 바란다.

# JPA batch insert 실습
먼저 JPA batch insert를 적용해보자.

applicayion.yml에 jpa batch insert 설정을 적용하지 않고 시퀀스 전략으로 JPA 엔티티 십만개를 insert 하는 테스트를 수행해보자.

<img width="1114" alt="Image" src="https://github.com/user-attachments/assets/18f3a0d3-dbff-4c3f-bfd8-f988454f9365" />

**22.2초**가 걸린 것을 확인할 수 있다.

그 다음으로 아래와 같이 JPA batch insert 설정을 추가후 동일한 테스트를 수행하였다.

```json
spring:
  datasource:
    driver-class-name: xxx
    url: xxx
    username: xxx
    password: xxx

  jpa:
    hibernate:
      ddl-auto: none
    properties:
      hibernate:
        format_sql: false
        show_sql: true
        dialect: org.hibernate.dialect.MariaDBDialect
        jdbc:
          batch_size: 50
          order_inserts: true
          order_updates: true
```

<img width="1054" alt="Image" src="https://github.com/user-attachments/assets/3ab0e93b-b086-4f1b-a6c4-ddc8bc08f0a1" />

**3.1초**로 줄어들며 확실히 batch insert가 적용된것을 확인할 수 있다.

# Hibernate log 기준으로 단건씩 insert 쿼리가 수행되는데?
실제 Hibernate가 출력하는 쿼리 로그를 확인해보면 insert 쿼리가 단건씩 출력된다.

<img width="470" alt="Image" src="https://github.com/user-attachments/assets/7fde7bf0-64da-4bbd-8e0a-23f20f8d3713" />

insert 쿼리 로그가 단건씩 출력되는 이유는 Hibernate의 `show_sql = true` 옵션을 지정하면 Hibernate가 실행하는 개별적인 PreparedStatement 수준에서 SQL을 로그로 출력하기 때문이다.

# 여러 insert 쿼리를 하나의 sql로 합쳐서 보내는게 맞을까?
batch insert 적용후 아래와 같은 mariadb batch insert 쿼리를 기대할 수 있다.

```sql
insert into product (id, name)
values (1, 'product1'), (2, 'product2'), (3, 'product3'), ...
```

**하지만 Hibernate의 batch insert는 PreparedStatement의 addBatch(), executeBatch() 메서드를 호출함으로써 적용된다.**

<img width="1074" alt="Image" src="https://github.com/user-attachments/assets/76f85f95-0148-41f9-ad51-d2e587bfe47f" />

즉 하나의 엔티티에 대한 insert 쿼리 기준으로 여러 개의 엔티티들에 대한 생성 쿼리를 PreparedStatement을 활용하여 한 번의 네트워크 요청으로 묶어 보내게 되는것이다. 이를 통해 네트워크 호출 비용을 줄이고 sql 구문 분석 결과를 캐싱처리함으로써 성능을 향상시킬수 있는 것이다.

참고로 DB 드라이버 레벨에서 rewriteBatchedStatements 옵션을 적용하면 의도한대로 여러 insert 쿼리를 하나의 insert 쿼리로 합쳐서 전송하게할수도 있다.