---
title: "[JPA-Episode2] 영속성 컨텍스트" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2022-06-03 +0800
categories: [학습할래?] # categories는 최대 2개까지 가능
tags: [jpa] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

이전 JPA 에피소드 1탄에서 SQL 중심적인 개발의 문제점과 JPA를 왜 써야하는지?에 대해서 알아보았는데요, 또한 JPA에서 가장 중요한 2가지를 언급드렸었습니다.


- 1) 객체와 관게형 데이터베이스 매핑하기(Object Relational mapping)
  - DB를 어떻게 설계하고 객체를 어떻게 설계해서 중간에 어떻게 JPA로 매핑해서 쓸건지
- 2) 영속성 컨텍스트
  - 실제 내부에서 JPA가 어떻게 동작하는지

오늘은 두 가지 중 영속성 컨텍스트 에 대해 알아보도록 하겠습니다!!! 


## 영속성 컨텍스트란?
영속성 컨텍스트는 엔티티를 영구 저장하는 환경 이라는 뜻으로, 엔티티 는 실제 도메인 클래스라고 생각하시면 됩니다.

```java
EntityManager.persist(entity);
```


아마 백엔드 개발자분들이라면 위 코드를 한 번쯤은 접해보셨을 텐데, persist() 메서드 는 '실제 DB에 저장한다기보단 영속성 컨텍스트에 저장한다' 라고 보실 수 있습니다.

## 엔티티의 생명주기
엔티티는 아래 이미지와 같은 생명주기를 가지고 있습니다. 비영속, 영속, 준영속, 삭제 까지 총 4가지 상태를 가집니다.

![image](https://user-images.githubusercontent.com/44339530/171622091-191ce9e4-2ac9-4706-82e3-ab0f2635041c.png)

### 1) 비영속 (new/transient)
- 영속성 컨텍스트와 전혀 관계가 없는 새로운 상태
- 일반적으로 객체를 생성 후 persist() 메서드를 호출하지 않은 상태

```java
//객체를 생성한 상태(비영속)
Member member = new Member();
member.setId("member1");
member.setUsername("회원1");
```

### 2) 영속 (managed)
- persist() 메서드를 호출하여 영구 저장한 상태(영속성 컨텍스트에 관리되는 상태)

### 3) 준영속 (detached)
- 영속성 컨텍스트에 저장되었다가 분리된 상태(더 이상 영속성 컨텍스트가 관리하지 않은 상태)
- `em.detach(member);`

### 4) 삭제 (removed)
- 엔티티를 아예 삭제한 상태
- `em.remove(member);`


영속성 컨텍스트는 논리적인 개념으로 `EntityManager` 를 통해 접근합니다.

![image](https://user-images.githubusercontent.com/44339530/171622164-5333ed63-3f3b-4244-9bdd-0a77f1b07170.png)

스프링 없이 JPA만을 학습하는 입장에선 EntityManager 하나 당 영속성 컨텍스트 하나씩 생긴다고 이해하시면 좋습니다!

**그렇다면 어떻게 엔티티 매니저로 고객의 요청을 처리할 수 있을까요?**

아래 이미지처럼 고객의 요청이 들어올때마다, 즉 프론트에서 api 요청을 호출 할 때마다 엔티티매니저팩토리 가 엔티티 매니저 를 하나씩 생성 후 엔티티 매니저가 DB 커넥션 풀로부터 커넥션을 사용하여 DB에 접근하여 처리되게 됩니다.

![image](https://user-images.githubusercontent.com/44339530/171622283-d7ef77ea-2669-4a55-8259-5ebbfefc9705.png)


<b>그렇다면 영속성 컨텍스트를 활용함으로써 얻을 수 있는 이점은 어떤게 있을까요? 애플리케이션과 RDB 사이의 중간 계층이다보니 버퍼링이나 캐싱을 할 수 있게 되는데 구체적으로는 아래와 같습니다.</b>


## 영속성 컨텍스트의 이점
- 1차 캐시
- 동일성(identity) 보장
- 트랜잭션을 지원하는 쓰기 지연(transactional write-behind)
- 변경 감지(Dirty Checking)
- 지연 로딩(Lazy Loading)

### 1) 1차 캐시

```java
//엔티티를 생성한 상태(비영속)
Member member = new Member();
member.setId("member1");
member.setUsername("회원1");

//엔티티를 영속
em.persist(member);
```

![image](https://user-images.githubusercontent.com/44339530/171622774-dd26d535-882a-43c7-9b34-5b25d80e0652.png)

영속성 컨텍스트는 내부에 1차 캐시 라는 것을 두고 있습니다. 

1차 캐시는 Map 형태로 id(pk)와 실제 Entity를 담고 있습니다.! 

만약 아래와 같은 코드를 실행하면 어떻게 될까요?


```java
Member member = new Member();
member.setId("member1");
member.setUsername("회원1");

Member member = new Member();
member.setId("member2");
member.setUsername("회원2");

em.persist(member);

Member findMember = em.find(Member.class, "member1");
```

당연히 DB에서 Member 객체를 조회해오겠지? 라고 생각하시는 분들이 계실 수 도 있으실텐데,  

5line에서 persist 를 호출할 때 DB에 저장하는 것이 아닌 1차 캐시에 저장하게 됩니다.

그 후 em.find() 가 호출 될 때 1차 캐시에서 먼저 찾고 존재할 시엔 캐시에 있는 값을 그냥 조회해 오게 됩니다. 그렇게 되면 당연히 메모리에서 꺼내오는 것이다보니 DB에서 찾는 것보다 속도가 훨씬 빠르게 됩니다. 만약 1차 캐시에 존재하지 않는다면 그 다음으로 DB에서 찾게 됩니다.

![image](https://user-images.githubusercontent.com/44339530/171622864-8c113d36-c4a8-4f3b-bf6e-5c6aa1ce5b09.png)

<b>만약 위 예제 코드에서 “member2” 를 조회하게 된다면 어떤 일이 일어날까요?</b>

![image](https://user-images.githubusercontent.com/44339530/171622922-85d73874-1ad4-4ecc-b938-a4f1665abe57.png)


1) 1차 캐시에서 찾은 후 없으므로 DB에 쿼리를 날려 조회한다.

2) 1차 캐시에 조회된 엔티티를 저장한다.

3) 저장된 엔티티를 반환한다.


> **Note**: 사실 이게 큰 도움은 안된다고 합니다! 왜냐하면 EntityManager라는 것은 DB 트랜잭션 단위로 만들고 DB 트랜잭션이 끝날때마다 삭제되는데 즉, 고객의 요청이 하나 들어와서 비즈니스가 끝나면 영속성 컨텍스트가 지워지게 됩니다. 굉장히 복잡할땐 비즈니스 요구사항이 존재한다면 도움이 될 수 있겠지만 일반적으로 굉장히 짧은 시간 동안 유지하기에 큰 도움은 없다고 볼 수 있다합니다 


### 2) 동일성(identity) 보장

```java
Member a = em.find(Member.class, "member1");
Member b = em.find(Member.class, "member1");

System.out.println(a == b); //동일성 비교 true
```

이전 시간에 JPA는 객체를 컬렉션에 넣듯이 처리 할 수 있게 하고자 한다고 설명 드렸었는데요..!

위에서 설명 드렸던 것처럼 1차 캐시로부터 엔티티를 얻어오기에 당연히 동일한 두 레코드는 동일성을 보장하게 됩니다.

### 3) 트랜잭션을 지원하는 쓰기 지연

```java
EntityManager em = emf.createEntityManager();
EntityTransaction transaction = em.getTransaction();

transaction.begin(); // [트랜잭션] 시작, 엔티티 매니저는 데이터 변경시 트랜잭션을 시작해야 한다.

em.persist(memberA);
em.persist(memberB);
//여기까지 INSERT SQL을 데이터베이스에 보내지 않는다.

transaction.commit(); // [트랜잭션] 커밋, 실제 커밋하는 순간 데이터베이스에 INSERT SQL을 보낸다.
```

위 코드를 실행하면 내부적으로는 아래 이미지와 같은 과정이 일어나게 됩니다. 



영속성 컨텍스트는 내부적으로 1차 캐시 뿐만 아닌 쓰기 지연 SQL 저장소가 존재합니다.

![image](https://user-images.githubusercontent.com/44339530/171623042-0cddeab6-7bca-4034-9e8f-b1567ea6a9e1.png)

MemberA를 persist()메서드를 통해 저장하면 MemberA가 1차 캐시에 들어감과 동시에 JPA가 엔티티를 분석해서 insert쿼리를 생성해서 쓰기 지연 SQL 저장소 에 쌓아두게 됩니다.

MemberB를 저장해도 위처럼 동일하게 처리 됩니다. 그렇다면 언제 쿼리가 DB로 날라갈까용?

![image](https://user-images.githubusercontent.com/44339530/171623102-b72116e3-dc78-421e-922a-eb825e6a8540.png)

트랜잭션을 커밋 하는 시점에 쓰기 지연 SQL 저장소에 있던 쿼리들이 flush(플러시)되면서 쿼리가 DB로 날라가게 됩니다.

영속성 컨텍스트를 플러시하는 방법은 아래와 같이 세 가지 방법이 있습니다.


1) em.flush() - 직접 호출

2) 트랜잭션 커밋 - 플러시 자동 호출

3) JPQL 쿼리 실행 - 플러시 자동 호출

JPQL이란 객체 지향 쿼리 언어로 실제 SQL문을 사용해서 데이터를 조회해오는 것이 아닌 객체를 중심으로 데이터를 조회해 오는 쿼리라고 보시면 됩니다!

```java
List<Member> members = em.createQuery("SELECT m FROM Member m")
    .getResultList();
```

### 4) 변경 감지(dirty checking)

```java
EntityManager em = emf.createEntityManager();
EntityTransaction transaction = em.getTransaction();
transaction.begin(); // [트랜잭션] 시작

// 영속 엔티티 조회
Member memberA = em.find(Member.class, "memberA");

// 영속 엔티티 데이터 수정
memberA.setUsername("hi");
memberA.setAge(10);

//em.update(member) 이런 코드가 있어야 하지 않을까? 하지만 없는게 맞다.!!!!

transaction.commit(); // [트랜잭션] 커밋
```

JPA는 변경 감지라는 기능으로 entity를 변경할 수 있는 기능이 제공됩니다. 자바 컬렉션처럼 데이터를 조회해서 값을 바꿔도 다시 persist() 를 호출해서 저장할 필요가 없습니다. 비밀은 영속성 컨텍스트 안에 다 있습니다.

![image](https://user-images.githubusercontent.com/44339530/171623190-252255d8-8109-4adc-8d67-313aa78bf718.png)

JPA는 DB 트랜잭션을 커밋하는 시점에 내부적으로 flush() 메서드가 호출됩니다. 그러면 엔티티와 스냅샷을 비교(1차 캐시 안에 존재) 합니다. 

1차 캐시는 엔티티가 처음 영속성 컨텍스트가 저장된 상태를 스냅샷에 저장합니다. 스냅샵과 현재 엔티티를 비교 후 상태 값이 변경되었으면 update쿼리를 쓰기 지연 SQL 저장소 에 만들어두고 DB에 반영하고 커밋함으로써 자동적으로 update 쿼리가 DB에 날라가게 됩니다:)


### 5) 엔티티 삭제

```java
//삭제 대상 엔티티 조회 (위의 매커니즘과 동일하고 트랜잭션 커밋 시점에 delete 쿼리가 호출된다)
Member memberA = em.find(Member.class, “memberA");

em.remove(memberA); //엔티티 삭제
```

위 코드처럼 엔티티를 조회 해 remove 메소드만 호출하면 위의 더티 체킹 매커니즘과 동일하게 delete 쿼리가 DB에 날아가게 됩니다.