---
title: "[DDDSTART] Chapter5-스프링 데이터 JPA를 이용한 조회기능" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2023-05-30 15:10:00 +0800
categories: [DDD, DDDSTART] # categories는 최대 2개까지 가능
tags: [ddd, dddstart] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

### 시작에 앞서
- CQRS 는 명령(COMMAND) 모델과 조회(Query) 모델을 분리하는 패턴이다.
  - 명령 모델은 상태(데이터)를 변경하는 기능을 구현할 때 사용된다(ex. 회원 가입, 암호 변경, 주문 취소)
  - 조회 모델은 데이터를 조회하는 기능을 구현시 사용된다.(ex. 주문 목록, 주문 상세)
- 주문 취소, 배송지 변경 기능을 포함하여 앞에서 살펴본 것 처럼 도메인 모델은 명령 모델로 주로 사용된다.
- 반면에, 이 장에서 설명할 정렬, 페이징, 검색 조건 지정과 같은 기능은 주문 목록, 상품 상세와 같은 조회기능에서 사용된다. 즉 이장에서 살펴볼 구현 방법은 조회 모델을 구현시 주로 사용한다.

> **Note**: 모든 DB연동 코드를 JPA만 사용해서 구현해야 한다고 생각하진 말자. MyBatis, JdbcTemplate 등 다양한 기술을 사용해서 조회 모델을 구현할 수 있다.

### 검색을 위한 스펙
- 검색 조건이 고정되어 있다면 특정 조건으로 조회하는 기능을 만들면 되지만, 만약 검색 조건을 다양하게 조합해야 할 때 사용할 수 있는 것이 스펙(Specification)이다.
- 스펙은 애그리거트가 특정 조건을 충족하는지를 검사할 때 사용하는 인터페이스다.

```java
public interface Specification<T> {
  boolean isSatisfiedBy(T agg);
}
```

- agg 파라미터는 검사 대상이 되는 객체이며 리포지터리에서 사용하면 애그리거트 루트가 되고 스펙을 DAO 에 사용하면 검색 결과로 리턴할 데이터 객체가 된다.
- isSatisfiedBy() 메서드는 검사 대상 객체가 조건을 충족하면 true를 리턴하고, 그렇지 않으면 false 를 리턴한다.
- 예를 들어, Order 애그리거트 객체가 특정 고객의 주문인지 확인하는 스펙은 다음과 같이 구현 가능하다.

```java
public class OrdererSpec implements Specification<Order> {
  private String orderId;

  public OrdererSpec(String ordererId) {
    this.ordererId = ordererId;
  }

  public boolean isSatisfiedBy(Order agg) {
    return agg.getOrdererId().getMemberId().getId().equasl(orererId);
  }
}
```

- 리포지터리나 DAO는 검색 대상을 걸러내는 용도로 스펙을 사용한다.
- 만약 리포지토리가 메모리에 모든 애그리거트를 보관하고 있다면 다음과 같이 스펙을 사용할 수 있다.

```java
public class MemberOrderRepository implements OrderRepository {
  public List<Order> findAll(Specification<Order> spec) {
    List<Order> allOrders = findAll();
    return allOrders.stream()
                    .filter(order -> spec.isSatisfiedBy(order))
                    .toList();
  }
  ...
}

// 검색 조건을 표현하는 스펙을 생성
SPecification<Order> ordererSpec = new OrdererSpec("madvirus");
// 리포지터리에 전달
List<Order> orders = orderRepository.findAll(ordererSpec);
```

- 하지만 실제 스펙은 이렇게 구현하지 않는다.
- 모든 애그리거트 객체를 메모리에 보관핟기도 어렵고 설사 메모리에 다 보관할 수 있다하더라도 조회 성능에 심각한 문제가 발생하기 때문이다.
- 실제 스펙은 사용하는 기술에 맞춰 구현하면 되는데 스프링 데이터 JPA를 이용한 스펙 구현에 대해 알아볼 것이다.

### 스프링 데이터 JPA를 이용한 스펙 구현

- 스프링 데이터 JPA 는 검색 조건을 표현하기 위한 인터페이스인 Specification 을 제공한다.

```java
public interface Specification<T> extends Serializable {
  // not, where, and, or 메서드 생략

  @Nullable
  Predicate toPredicate(Root<T> root, 
                        CriteriaQuery query, 
                        CriteriaBuilder cb);
}
```

- 제네릭 타입 파라미터 T는 JPA엔티티 타입을 의미하며, toPredicate() 메서드는 JPA Criteria API에서 조건을 표현하는 Predicate 를 생성한다.
- 이를 활용한 예시는 아래와 같다.

```java
public class OrdererIdSpec implements Specification<OrderSummary> {
  private String ordererId;

  public OrdererIdSpec(String ordererId) {
    this.ordererId = ordererId;
  }

  @Override
  public Predicate toPredicate(Root<T> root, 
                        CriteriaQuery query, 
                        CriteriaBuilder cb) {
    return cb.equals(root.get(OrderSummary_.ordererId), ordererId);
  }
}
```

- OrdererSummary 엔티티에 대한 검색 조건을 표현한다.
- ordererId 프로퍼티 값이 생성자로 전달받은 값과 동일한지 비교하는 Predicate 를 생성한다.
- 스펙 구현 클래스를 개별적으로 만들지 않고 별도 클래스에 스펙 생성 기능을 모아도 된다.
  
```java
public class OrderSummarySpec {
  public static Specification<OrderSummary> ordererId(String ordererId) {
    return (Root<OrderSummary> root, CriteriaQuery<?> query, CriteriaBuilder cb) ->
      cb.equals(root.<String>get("ordererId"), ordererId);
  }

  public static Specification<OrderSummary> orderDateBetween(LocalDateTime from, LocalDateTime to) {
    return (Root<OrderSummary> root, CriteriaQuery<?> query, CriteriaBuilder cb) ->
      cb.equals(root.get(OrderSummary_.orderDate), from, to);
  }
}

// 스펙 생성이 필요한 코드는 스펙 생성 기능을 제공하는 클래스를 이용해서 조금 더 간결하게 스펙을 생성 가능하다.
Specification<OrderSummary> betweenSpec = OrderSummarySpecs.orderDateBetween(from, to);
```

#### JPA 정적 메타 모델
- 위 예제 코드에서 OrderSummary_.ordererId 로 사용되는 부분이 있는데, OrderSummary_ 클래스는 JPA정적 메타 모델을 정의한 코드이다.
- 정적 메타 모델 클래스는 다음과 같이 구현 가능하다.

```java
@StaticMetamoidel(OrderSummary.class)
public class OrderSummary_ {
  public static volatile SingularAttribute<OrderSummary, String> number;
  public static volatile SingularAttribute<OrderSummary, Long> version;
  public static volatile SingularAttribute<OrderSummary, String> ordererId;
  public static volatile SingularAttribute<OrderSummary, String> ordererName;
  ... 생략
}
```

- 정적 메타 모델은 `@StaticMetamodel` 애너테이션을 이용해서 관련 모델을 지정한다.
- 메타 모델 클래스는 모델 클래스의 이름 뒤에 '_'을 붙인 이름을 갖는다.
- 정적 메타 모델 클래스는 대상 모델의 각 프로퍼티와 동일한 이름을 갖는 정적 필드를 정의한다.
- 이 정적 필드는 프로퍼티에 대한 메타 모델로서 프로퍼티 타입에 따라 SingularAttribute, ListAttribute 등의 타입을 사용해서 메타 모델을 정의한다.
- 정적 메타 모델을 사용하는 대신 문자열로 프로퍼티를 지정할 수도 있다.

```java
cb.equals(root.<String>get("ordererId"), ordererId);
```

- 하지만 문자열은 오타 가능성이 있고, 실행전까진 오타가 있다는 것을 놓치기 쉽다.
- 게다가 IDE의 코드 자동 완성 기능을 사용할 수 없어 입력할 코드도 많아진다.
- 이런 이유로 Criteria를 사용할땐 정적 메타 모델 클래스를 사용하는 것이 코드 안정성이나 생산성 측면에서 유리하다.
- 정적 메타 모델 클래스를 직접 작성할 수 있지만 하이버네이트와 같은 JPA 프로바이더는 정적 메타 모델을 생성하는 도구를 제공하고 있으므로 이들 도구를 사용하면 편리하다.

### 리포지터리/DAO에서 스펙 사용하기
- 스펙을 충족하는 엔티티를 검색하고 싶다면 findAll() 메서드를 사용하면 된다.
- 스프링 데이터 JPA 사용시 JpaSpecificationExecutor 를 상속받아서 사용 가능하다.

```java
public interface JpaSpecificationExecutor<T> {
  // 생략...
  
  List<T> findAll(@Nullable Specification<T> spec);

  // 생략...
}
```

### 스펙 조합
- 스프링 데이터 JPA가 제공하는 스펙 인터페잇느느 스펙을 조합할 수 있는 두 메서드 `and()`, `or()` 를 제공하다.
  - and(): 두 스펙을 모두 충족하는 조건을 표현하는 스펙 생성
  - or(): 두 스펙 중 하나 이상 충족하는 조건을 표현하는 스펙을 생성

```java
public interface Specification<T> extends Serializable {

  static <T> Specification<T> not(@Nullable Specification<T> spec) { ... }
  static <T> Specification<T> where(@Nullable Specification<T> spec) { ... }
  default Specification<T> and(@Nullable Specification<T> other) { ... }
  default Specification<T> or(@Nullable Specification<T> other) { ... }
  
  @Nullable
  Predicate toPredicate(Root<T> root, CriteriaQuery query, CriteriaBuilder cb); 
}
```

- 아래 코드는 spec1.and(spec2) 는 spec1 과 spec2 를 모두 충족하는 조건을 표현하는 spec3를 생성한다.

```java
Specification<OrderSummary> spec1 = OrderSummarySpecs.ordererId("user1");
Specification<OrderSummary> spec2 = OrderSummarySpecs.orderDateBetween(LocalDateTime.of(2022, 1, 1, 0, 0, 0),
 LocalDateTime.of(2022, 1, 2, 0, 0, 0));

Specification<OrderSummary> spec3 = spec1.and(spec2);
```

- 아래와 같은 체이닝 기법으로 사용가능하여 불필요한 변수를 선언하지 않아도 된다.

```java
Specification<OrderSummary> spec = OrderSummarySpecs.ordererId("user1")
.and(OrderSummarySpecs.orderDateBetween(from, to));
```

- not() 메서드는 정적 메서드로 조건을 반대로 적용할때 사용한다.

```java
Specification<OrderSummary> spec1 = Specification.not(OrderSummarySpecs.ordererId("user1"));
```

- null 가능성이 있는 스펙 객체와 다른 스펙을 조합해야 할 때 Null 체크를 매번 하려면 다소 귀찮다.

```java
Specification<OrderSummary> nullableSpec = createNullableSpec(); // null일 수 있음
Specification<OrderSummary> oterSpec = createOtherSpec();

Specification<OrderSummary> spec = nullable == null ? otherSpec : nullableSpec.and(otherSpec);
```

- where() 메서드를 사용면 이런 귀찮음을 줄일 수 있다.
  - null을 전달하면 아무 조건도 생성하지 않는 스펙 객체를 리턴하고 null이 아니면 인자로 받은 스펙 객체를 그대로 리턴하다.

```java
Specification<OrderSummary> spec = Specification.where(createNulalbleSpec()).and(createOtherSpec());
```

### 정렬 지정하기
- 스프링 데이터 JPA는 두 가지 방법을 사용해서 정렬을 지정 가능하다.

### 1) 메서드 이름에 OrderBy를 사용해서 정렬 기준 지정

```java
public interface OrderSummaryRepository extends JpaRepository<OrderSummary, Integer> {
  
  // 메서드 이름
  List<OrderSummary> findAllByOrderByOrderDateDescNumberAsc(String ordererId); // OrderDate 프로퍼티 기준으로 내림차순 정렬 후 Number 프로퍼티 기준으로 오름차순으로 정렬
}
```

- 위 방법의 단점은 정렬 조건이 많아질수록 메서드명이 길어진다는 것과 메서드 일므으로 정렬 순서가 정해지기 때문에 상황에 따라 정렬순서를 변경할 수도 없다.
- 이땐 아래 Sort 타입을 사용하면 된다.

### 2) Sort를 인자로 전달

```java
public interface OrderSummaryRepository extends JpaRepository<OrderSummary, Integer> {
   
  // Sort
  List<OrderSummary> findAllByOrdererId(String ordererId, Sort sort);
}
```

- Sort 객체를 생성해서 인자로 넘겨주면 스프링 데이터 JPA 내부적으로 알아서 정렬 쿼리를 적용한다.

```java
Sort sort = Sort.by("number").ascending();
List<OrderSummary> results = orderSummaryDao.findByOrdererId("user1", sort);

// 두 개 이상의 정렬 순서를 지정하고 싶다면 Sort#and() 메서드를 사용해서 두 sort 객체를 연결하면 된다.
Sort sort1 = Sort.by("number").ascending();
Sort sort2 = Sort.by("orderDate").descending();

Sort sort sort1.and(sort2);

// 메서드 체이닝도 가능하다.
Sort sort = Sort.by("number").ascending().and(Sort.by("orderDate").descending());
```

### 페이징 처리하기
- 스프링 데이터 JPA 는 페이징 처리를 위해 Pageable 인터페이스 타입을 이용한다.
- Sort 타입과 마찬가지로 find 메서드에 Pageable 타입 파라미터를 사용하면 페이징을 자동으로 처리해준다.

```java
public interface OrderSummaryRepository extends JpaRepository<OrderSummary, Integer> {
  
  List<OrderSummary> findByOrderByNumberDesc(String ordererId, Pagable pagable);

  // 목록뿐 아니라 조건에 해당하는 전체 개수 및 페이징 처리에 필요한 데이터도 함께 제공
  Page<OrderSummary> findByOrderByNumberDesc(String ordererId, Pagable pagable);
  
}
```

- 이를 실제 호출할땐 Pageable 의 구현체인 PageRequest 객체를 생성해야 한다.

```java
PageRequest pageReq = PageRequest.of(1, 10); // 페이지 번호, 한 페이지의 개수 (페이지 번호는 0부터 시작)
List<MemberData> user = memberDataDao.findbyNameLike("사용자%", pageReq);
```

- PageRequest 와 Sort 를 사용하면 정렬 순서도 지정가능하다.

```java
Sort sort = Sort.by("name").descending();
PageRequest pageReq = PageRequest.of(1, 2, sort);
List<MemberData> user = memberDataDao.findbyNameLike("사용자%", pageReq);
```

- Pageable 을 사용하는 메서드의 리턴타입이 Page 일 경우 스프링 데이터 JPA는 목록 조회 쿼리와 함께 COUNT 쿼리도 실행해서 조건에 해당하는 데이터 갯수를 구한다.
- Page 는 전체 개수, 페이지 개수 등 페이징 처리에 필요한 데이터도 함께 제공한다.
- 다음은 Page 가 제공하는 메서드의 일부를 보여준다.

```java
PageRequest pageReq = PageRequest.of(2, 3);
Page<MemberData> user = memberDataDao.findbyBlocked(false, pageReq);

List<MemberData> content = page.getContent(); // 조회 결과 목록
long totalElements = page.getTotalElements(); // 조건에 해당하는 전체 개수
int totalPages = page.getTotalPages(); // 전체 페이지 번호
int number = page.getNumber(); // 현재 페이지 번호
int numberOfElements = page.getNumberOfElements(); // 조회 결과 개수
int size = page.getSize(); // 페이지 크기
```

- 스펙을 사용하는 findAll() 메서드도 Pageable 을 사용할 수 있다.

```java
public interface MemberDataDao extends Repository<MemberData, String> {
  Page<MemberData> findAll(Specification<MemberData> spec, Pageable pageable);
}
```

> **Note**: 프로퍼티를 비교하는 findBy프로퍼티 형식의 메서드는 Pageable 타입을 사용하더라도 리턴타입이 List 면 COUNT 쿼리를 실행하지 않는다. 반면 스펙을 사용한 findAll 메서드에 Pageable 타입을 사용하면 리턴타입이 Page 가 아니어도 COUNT 쿼리를 실행한다. 만약 스펙을 사용하고 페이징 처리를 하면서 COUNT 쿼리는 실행하고 싶지 않다면 스프링 데이터 JPA가 제공하는 커스텀 리포지터리 기능을 이용해서 직접 구현해야 한다. 구현 방법이 궁금하면 https://javacan.tistory.com/entry/spring-data-jpa-range-query 문서를 참고하면 좋다.

- 쳐음부터 N개의 데이터가 필요하다면 Pageable 을 사용하지 않고 findFirstN형식의 메서드를 사용할 수도 있다.
- 예를 들어 다음 메서드는 name 프로퍼티 기준으로 like 검색한 결과를 name 프로퍼티 기준으로 오름차순 정렬해서 처음 3개를 조회한다. First 대신 Top을 사용해도 된다.
  - 만약 First나 Top 뒤에 숫자가 없으면 한 개 결과만 리턴한다.

```java
List<MemberData> findFirst3ByNameLikeOrderByName(String name);
List<MemberData> findTop3ByNameLikeOrderByName(String name);

MemberData findFirstByBlockedOrderById(boolean blocked);
```

### 스펙 조합을 위한 스펙 빌더 클래스
- 스펙을 생성하다보면 다음 코드처럼 조건에 따라 스펙을 조합해야 할 때가 있다.

```java
Specification<MemberData> spec = Specification.where(null);

if (searchReq.isOnleyNotBlocked()) {
  spec = spec.and(MemberDataSpecs.nonBlocked());
}

if (StringUtils.hasText(searchRequest.getName())) {
  spec = spec.and(MemberDataSpecs.nameLike(searchRequest.getName()));
}

List<MemberData> results = memberDataDao.findAll(Spec, PageRequest.of(0, 5));
```

- 위 코드는 if와 각 스펙을 조합하는 코드가 섞여 있어 실수하기 좋고 복잡한 구조를 갖는다.
- 이 점을 보완하기 위해 필자는 스펙 빌더를 만들어 사용한다.

```java
Specification<MemberData> spec = SpecBuilder.builder(MemberData.class)
        .ifTrue(searchRequest.isOnlyNotBlocked(),
        () -> MemberDataSpecs.nonBlocked())
        .ifHasText(searchReq.getName(),
        name -> MemberDataSpecs.nameLike(searchReq.getName()))
        .toSpec();

List<MemberData> result = memberDataDao.findAll(spec, PageRequest.of(0, 5));
```

- if 블록을 사용할 때와 비교하면 코드양은 비슷하지만 메서드를 사용해서 조건을 표현하고 메서드 호출 체인으로 연속된 변수할당을 줄여 코드 가독성을 높이고 구조가 단순해진다.
- 스펙 빌더 코드는 아래와 같다. and(), ifHasText(), ifTrue() 메서드 외에는 직접 추가해서 사용하면 된다.

```java
public class SpecBuilder {
    public static <T> Builder<T> builder(Class<T> type) {
        return new Builder<T>();
    }

    public static class Builder<T> {
        private List<Specification<T>> specs = new ArrayList<>();

        private void addSpec(Specification<T> spec) {
            if (spec != null) {
                specs.add(spec);
            }
        }

        public Builder<T> and(Specification<T> spec) {
            addSpec(spec);
            return this;
        }

        public Builder<T> ifHasText(String str,
                                    Function<String, Specification<T>> specSupplier) {
            if (StringUtils.hasText(str)) {
                addSpec(specSupplier.apply(str));
            }
            return this;
        }

        public Builder<T> ifTrue(Boolean cond,
                                 Supplier<Specification<T>> specSupplier) {
            if (cond != null && cond.booleanValue()) {
                addSpec(specSupplier.get());
            }
            return this;
        }

        public <V> Builder<T> ifNotNull(V value,
                                        Function<V, Specification<T>> specSupplier) {
            if (value != null) {
                addSpec(specSupplier.apply(value));
            }
            return this;
        }

        public Specification<T> toSpec() {
            Specification<T> spec = Specification.where(null);
            for (Specification<T> s : specs) {
                spec = spec.and(s);
            }
            return spec;
        }
    }
}
```

### 동적 인스턴스 생성
- JPA 는 쿼리 결과에서 임의의 객체를 동적으로 생성할 수 있는 기능을 제공하고 있다.
  - Projection 을 의미한다.
- 조회 전용 모델을 만드는 이유는 표현 영역을 통해 사용자에게
데이터를 보여주기 위함이다.
- 동적 인스턴스의 장점은 JPQL 을 그대로 사용하므로 객체 기준 쿼리를 사용하면서도 지연/즉시 로딩과 같은 고민이 필요없이 데이터를 조회할 수 있다는 점이다.

```java
public interface OrderSummaryDao extends Repository<OrderSummary,String>{
	@Query("select new com.myship.order.query.dto.OrderView(o.number, o.state, m.name, m.id, p.name)
    from Order o join o.orderLines ol, Member m, Product p
    where o.orderer.memberId.id = m.id
    	and index(ol) = 0
        and ol.productId.id = p.id
        order by o.number.number desc")
    List<OrderView> findOrderView(String ordererId);
}
```

```java
public class OrderView {
  private final String number;
  private final OrderState state;
  private final String memberName;
  private final String memberId;
  private final String productName;
  
  public OrderView(OrderNo number, 
                  OrderState state,
                  String memberName, 
                  MemberId memberId, 
                  String productName) {
    this.number = number.getNumeber();
    this.state = state;
    this.memberName = memberName;
    this.memberId = memberId.getId();
    this.productName = productName;
  }

  ... // get 메서드
}
```

### 하이버네이트 @Subselect 사용
- 하이버네이트는 JPA 확장 기능으로 `@Subselect` 를 제공한다.
- `@Subselect` 는 쿼리 결과를 @Entity로 매핑할 수 있는 유용한 기능이다.

```java
import org.hibernate.annotations.Immutable;
import org.hibernate.annotations.Subselect;
import org.hibernate.annotations.Synchronize;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.Id;

@Entity
@Immutable
@Subselect(
        ''''
        select o.order_number as number,
        o.version, o.orderer_id, o.orderer_name,
        o.total_amounts, o.receiver_name, o.state, o.order_date, p.product_id, p.name as product_name
        from purchase_order o inner join order_line ol
            on o.order_number = ol.order_number
            cross join product p
        where ol.line_idx = 0 and ol.product_id = p.product_id
        ''''
)
@Synchronize({"purchase_order", "order_line", "product"})
public class OrderSummary {
    @Id
    private String number;
    private long version;

    @Column(name="orderer_id")
    private String ordererId;

    @Column(name="orderer_name")
    private String ordererName;

    protected OrderSummary(){

    }
}
```


- `@Immutable`, `@Subelect`, `@Synchronize` 는 하이버네이트 전용 애너테이션인데 이 애너테이션을 사용하면 테이블이 아닌 쿼리 결과를 @Entity로 매핑할 수 있다.
- `@Subselect` 는 조회 쿼리를 값으로 갖는다. 하이버네이트는 이 select 쿼리의 결과를 매핑할 테이블처럼 사용한다.
- DBMS가 여러 테이블을 조인ㅇ해서 조회한 결과를 한 테이블처럼 보여주기 위한 용도로 뷰를 사용하는 것처럼 `@Subselect` 를 사용하면 쿼리 실행 결과를 매핑할 테이블처럼 사용한다.
- 뷰를 수정할 수 없듯이 `@Subselect` 로 조회한 `@Entity` 역시 수정할 수 없다.
- 실수로 `@Subselect` 를 이용한 `@Entity`의 매핑 필드를 수정하면 하이버네이트는 변경 내역을 반영하는 update 쿼리를 실행할텐데 매핑 한 테이블이 없으므로 에러가 발생한다.
- 이런 문제를 방지하기 위해 `@Immutable` 을 사용하는데 하이버네이트는 해당 엔티티의 매핑 필드/프로퍼티가 변경되도 DB에 반영하지 않고 무시한다.

```java
// purchase_order 테이블에서 조회
Order order = orderRepository.findById(orderNumber);
order.changeShippingInfo(newInfo); // 상태 변경

// 변경 내역이 DB에 반영되지 않았는데 purchase_order 테이블에서 조회
List<OrderSummary> summaries = orderSummaryRepository.findByOrdererId(userId);
```

- 위 코드는 Order의 상태를 변경한 뒤에 OrderSummary 를 조회하고 있다.
- <b>특별한 이유가 없으면 하이버네이트는 트랜잭션을 커밋하는 시점에 변경사항을 DB에 반영하므로, Order의 변경 내역을 아직 purchase_order 테이블에 반영하지 않은 상태에서 purchase_order 테이블을 사용하는 OrderSummary 를 조회하게 된다. 즉, OrderSummary 에는 최신 값이 아닌 이전 값이 담기게 된다.</b>
- 이런 문제를 해소하기 위한 용도로 사용한 것이 `@Synchronize` 이다.
  - 해당 엔티티와 관련된 테이블 목록을 명시한다.
  - 하이버네이트는 엔티티를 로딩전에 지정한 테이블과 관련된 변경사항이 발생하면 플러시를 먼저 한다.
  - OrderSummary 의 `@Synchronize` 는 'purchase_order' 테이블을 지정하고 있으므로 OrderSummary 를 로딩하기 전에 purchase_order 테이블에 변경이 발생하면 관련 내역을 먼저 플러시 한다.
  - 따라서 OrderSummary를 로딩하는 시점엔 변경 내역이 반영된다.
- `@Subselect` 를 사용해도 일반 Entity 와 같기 때문에 EntityManager#find(), JPQL, Criteria 를 사용해서 조회할 수 있는 것이 @Subselect 의 장점이다. 이것은 초반에 설명한 스펙을 사용할 수 있따는 것도 포함된다.

```java
// @Subselect 를 적용한 @Entity 는 일반 @Entity 와 동일한 방법으로 조회할 수 있다.
Specification<OrderSummary> spec =orderDateBetween(from, to);
Pageable pageable = PageRequest.of(1, 10);
List<OrderSummary> results = orderSummaryDao.findAll(spec, pageable);
```

- `@Subselect`는 이름처럼 @Subselect 의 값으로 지정한 쿼리를 from절의 서브 쿼리로 사용한다. 즉, 실행하는 쿼리는 당므과 같은 형식을 갖는다.

```sql
select osm.number as number1_0_, ... 생략
from  (
    select o.order_number as number,
    o.version, o.orderer_id, o.orderer_name,
    o.total_amounts, o.receiver_name, o.state, o.order_date, p.product_id, p.name as product_name
    from purchase_order o inner join order_line ol
        on o.order_number = ol.order_number
        cross join product p
    where ol.line_idx = 0 and ol.product_id = p.product_id
) osm
where osm.orderer_id = ? order by osm.number desc
```

- `@Subselect` 를 사용할 때는 쿼리가 이러한 형태를 갖는다는 점을 유념해야 한다.
- 서브 쿼리를 사용하고 싶지 않다면 네이티브 SQL 쿼리를 사용하거나 마이바티스와 같은 별도 매퍼를 사용해서 조회 기능을 구현해야 한다.

### Reference
- 예제 코드 및 이미지
  - [https://velog.io/@csh0034/%EB%8F%84%EB%A9%94%EC%9D%B8-%EC%A3%BC%EB%8F%84-%EA%B0%9C%EB%B0%9C-%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0-05.-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%8D%B0%EC%9D%B4%ED%84%B0-JPA%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%A1%B0%ED%9A%8C-%EA%B8%B0%EB%8A%A5](https://velog.io/@csh0034/%EB%8F%84%EB%A9%94%EC%9D%B8-%EC%A3%BC%EB%8F%84-%EA%B0%9C%EB%B0%9C-%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0-05.-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%8D%B0%EC%9D%B4%ED%84%B0-JPA%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%A1%B0%ED%9A%8C-%EA%B8%B0%EB%8A%A5)