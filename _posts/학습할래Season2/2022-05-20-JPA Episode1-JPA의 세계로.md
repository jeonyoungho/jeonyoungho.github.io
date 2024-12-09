---
title: "[학습할래?][JPA-Episode1] JPA의 세계로"
date: 2022-05-20 +0800
categories: [학습할래?]
tags: [jpa]
toc: true
comments: true
---

## JPA 왜 써야하는가?
애플리케이션은 객체 지향 개발을 하면서 코드를 까보면 SQL만 가득차 있는 모습을 볼 수 가 있는데요. 이러한 SQL 중심적인 개발은 많은 문제점들을 야기합니다.

### 1. 무한 반복, 지루한 코드
CRUD 쿼리 무한 반복해야됩니다. 테이블이 10개면 10개를 다해줘야해서 생산성이 저하되고 힘들게 됩니다.(객체에 필드가 추가되면 쿼리들에 하나씩 일일이 다 추가해줘야함… 하나라도 놓치게 되면..)

![image-20220519-100047](https://github.com/jeonyoungho/jeonyoungho.github.io/assets/44339530/8a7cd6e3-7035-46f0-b7e6-bf4f39cb4ff9)


### 2. 패러다임의 불일치(객체 지향 vs 관계형 DB)
- 관계형 DB는 데이터를 정교화해서 저장하는게 목표이고, 객체는 필드나 메서드 같은게 묶여서 캡슐화해서 쓰는게 목표입니다.
- 이처럼 이 둘의 패러다임이 안맞기에 여러 가지 문제가 생기게 됩니다.
- RDB가 인식할 수 있는 것은 SQL뿐이기 때문에 결국, Object를 SQL로 짜야합니다.
  - 객체 -> `SQL 변환` -> RDB에 저장
  - `개발자 == SQL 매퍼` 라고 할만큼 SQL 작업을 너무 많이 하게 됩니다…

![image](https://github.com/jeonyoungho/jeonyoungho.github.io/assets/44339530/265a91b0-0c3e-4621-bb88-4f5293b652f9)
_출처: [공부기록](https://medium.com/joonghoonc-datastudy/210316-%EA%B3%B5%EB%B6%80%EA%B8%B0%EB%A1%9D-af11939893fb)_


## 3. 객체와 관계형 데이터베이스의 차이

### 1)) 상속
- 객체에는 상속 관계가 있지만, 관계형DB 에는 상속 관계가 없습니다. (유사한게 있긴함)
- 객체의 상속 관계와 유사한 모델이 RDB의 슈퍼타입 서브타입 관계입니다. Album을 DB에 저장한다하면 ITEM테이블 삽입 sql, ALBUM테이블 삽입 sql 총 2개를 작성해야 합니다. 만약 Album을 조회한다하면 조인으로 Album 테이블과 ITEM테이블을 조회해서 나온 결과 값을 일일이 Album 객체와 Item 객체에 일일이 필드 값을 넣어줘야 합니다. 이러한 문제로 개발자는 SQL 매핑 작업을 한땀 한땀 하다보면 생산성이 저하되게 되며 실수할 가능성도 높아지게 됩니다.

![image-20220407-101614](https://github.com/jeonyoungho/jeonyoungho.github.io/assets/44339530/2ee2c195-7d92-42e1-ae35-60a4ac80742a)


### 2) 연관관계
- 객체는 레퍼런스를 가질 수 있지만, RDB는 PK, FK를 사용하여 join을 사용해야합니다.

![image-20220519-100730](https://github.com/jeonyoungho/jeonyoungho.github.io/assets/44339530/352fd80e-c3a8-46a5-97c3-76840588e594)


- Object
  - 참조(Reference)를 사용하여 연관 관계를 찾습니다.(`Member.getTeam()`)
  - 양뱡향 참조 할 수 없습니다. (위 이미지 상의 연관관계를 기준으로 Member에서 Team으로 참조 가능하지만, Team에서 Member로는 불가능합니다)
- RDB
  - 외래키(FK)를 사용하여 Join 쿼리를 통해 연관 관계를 찾습니다.`(JOIN ON M.TEAM_ID = T.TEAM_ID)`
  - 양방향 참조 가능합니다.(테이블은 Team에서 Member로도 fk로 join을 통해 참조가능합니다)

### 3) 데이터 타입
가장 쉬운 예시로는 java에는 boolean 타입이 존재하지만 RDB에는 boolean 타입이 없기에 0 또는 1로 저장합니다….

## 4. 객체 그래프 탐색
객체는 `자유롭게 객체 그래프를 탐색` 할 수 있어야 합니다.

![image-20220519-101458](https://github.com/jeonyoungho/jeonyoungho.github.io/assets/44339530/fdad2f2b-1dbd-4f54-a3f9-ddb6cdfea807)

```sql
SELECT M.*, T.*
FROM MEMBER M
JOIN TEAM T ON M.TEAM_ID = T.TEAM_ID

member.getTeam(); //OK
member.getOrder(); //null
```

위와 같은 객체들 간의 연관관계가 존재하고 위 쿼리를 실행하여 Member 객체를 조회한 후 `getTeam()`과 `getOrder()`메서드를 실행한다고 가정해봅시다. 쿼리를 보면 Member테이블과 Team 테이블만을 조인해서 데이터를 가져오는 것을 보실 수 있습니다.

**여기서 문제는 처음 실행하는 SQL에 따라 탐색 범위가 결정되는 문제가 발생합니다.** 예를 들어, 아래 조회 쿼리를 작성 후 getTeam() 메서드를 통해 Team객체를 조회하는 것은 가능하지만 Order 객체를 조회하는 것은 불가능하게 됩니다. (처음 SQL 실행 할 때 Member랑 Team만 조회해서 가져오기 때문에)

위와 같은 문제는 **엔티티 신뢰 문제**를 야기하게 됩니다.

```java
class MemberService {
  ...
  public void process() {
    Member member = memberDAO.find(memberId);
    member.getTeam(); //???
    member.getOrder().getDelivery(); // ???
  }
}
```

memberDAO에서 어떤 쿼리를 날렸는지 알 수 없는 이상 자유롭게 getTeam(), getOrder()와 같은 메서드를 사용할 수 없게되고, 즉 신뢰성이 떨어지게 됩니다


> **Note**: 위와 같은 이유들을 해결하는 것과 더불어 객체를 자바 컬렉션에 저장하듯이 DB에 저장하고 불러올 수 있는 방법이 없을까를 고민해왔고 그러다보니 JPA가 만들어졌다고 합니다 :)

## JPA 란 그래서 무엇일까?
- Java Persistence API의 준말
- Java에서의 ORM 표준 기술

## ORM이란?
- Object-relational mapping(객체 관계 매핑)의 준말
- 위에서 설명했던 객체와 RDB 간의 불일치 패러다임을 줄여줄 수 있도록 둘 사이를 매핑
- Java 뿐만 아니라 많은 언어에서 대부분의 ORM 기술이 존재 (nodejs, kotlin…)

![image-20220519-103404](https://github.com/jeonyoungho/jeonyoungho.github.io/assets/44339530/43e12f4d-0d34-401f-9572-aa261e942008)

JPA는 애플리케이션과 JDBC 사이에서 동작합니다. 개발자가 JPA를 사용하면 JPA 내부에서 JDBC API를 사용하여 DB를 제어한다고 생각해주시면 됩니다.

> **Note**: 처음 듣는 프론트분들께서는 axios가 XMLHttpRequest를 랩핑하여 axios를 사용하면 axios가 XMLHttpRequest를 대신해서 호출해주는 것을 빗대서 이해하시면 좋습니다. 


JPA는 **인터페이스의 모음(구현체 x)** 입니다. 그리고 이 인터페이스를 구현한(JPA 2.1 표준 명세를 구현한) 3가지 구현체는 `Hibernate`, `EclipseLink`, `DataNucleus` 입니다.

![image-20220519-104141](https://github.com/jeonyoungho/jeonyoungho.github.io/assets/44339530/825950a7-61ef-4bbd-a19d-45627db164e9)

실제로 INHR+ 백엔드는 Sprint Data JPA에 대한 의존성을 가지고 있어 구현체 Hibernate 를 사용한다고 보시면 됩니다.

## JPA의 장점

### 1) SQL 중심적인 개발에서 객체 중심으로 개발
위에서 설명 드렸던 SQL 중심적인 개발의 문제점들을 쉽게 해결해줄 수 있습니다…!!!!!

### 2) 생산성 - JPA와 CRUD
- 저장: jpa.persist(member)
- 조회: Member member = jpa.find(memberId)
- 수정: member.setName(“변경할 이름”)
- 삭제: jpa.remove(member)

한 줄만 작성하면 되기에 좋은 생산성을 가질 수 있게 됩니다. 뒤에 나오겠지만 JPA의 영속성 컨텍스트로 인해 컬렉션에서 단순하게 조회 후 setName만 호출하여 update 할 수 있게 됩니다:)

### 3) 유지보수 - 기존: 필드 변경시 모든 SQL 수정

 ![image](https://github.com/jeonyoungho/jeonyoungho.github.io/assets/44339530/f2a6c7c1-d46f-42f8-9aef-752f7f14dc02)

만약 MyBatis 또는 JdbcTemplate 을 사용하는 경우 필드 변경시 아래와 같이 모든 SQL을 수정해야 합니다…. 만약 하나라도 놓친다면..?ㅠㅠ

**하지만 JPA를 사용하면 필드를 수정하더라도 쿼리를 손댈 필요가 없고 엔티티 객체의 필드만 수정해주면 되기에 이러한 걱정이 사라지게 됩니다..!!!!!**

### 4) JPA의 성능 최적화 기능
- 1)1차 캐시와 동일성(identity) 보장
- 2)트랜잭션을 지원하는 쓰기 지연(transactional write-behind)
- 3)지연 로딩(Lazy Loading)

> JPA의 성능 최적화 기능과 관련해서는 이어질 에피소드들을 기대해주세요 :)