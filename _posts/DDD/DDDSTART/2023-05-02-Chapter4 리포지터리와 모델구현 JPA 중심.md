---
title: "[DDDSTART] Chapter4-리포지터리와 모델구현(JPA 중심)" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2023-05-02 15:10:00 +0800
categories: [DDD, DDDSTART] # categories는 최대 2개까지 가능
tags: [ddd, jpa] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

Chatper4 의 내용은 [인프런 JPA 기본편](https://jeonyoungho.github.io/categories/%EC%9D%B8%ED%94%84%EB%9F%B0jpa-%EA%B8%B0%EB%B3%B8%ED%8E%B8/) 의 내용과 중복되는 부분이 많아 많은 내용이 생략될 예정이다.

### JPA를 이용한 리포지토리 기능 구현

#### 모듈 구현
- 2장에서 언급한 것처럼 리포지토리 인터페이스는 애그리거트와 같이 도메인 영역에 속하고, 리포지터리를 구현한 클래스는 인프라스트럭처 영역에 속한다.

![image](https://user-images.githubusercontent.com/44339530/235642282-e0cc42bc-a3b5-46e6-974f-32b9ba1f53ca.png)

> 팀 표준에 따라 리포지터리 구현 클래스를 domain.impl과 같은 패키지에 위치시키는 경우도 있다. 이는 리포지터리 인터페이스와 구현체를 분리하기 위한 타협안 같은 것이지 좋은 설계 원칙을 따르는 것은 아니다. 가능하면 리포지터리 구현 클래스를 인프라스트럭처 영역에 위치시켜서 인프라스트럭처에 대한 의존을 낮춰야 한다.

#### 리포지터리 기본 기능 구현

> **삭제 기능**: 삭제 요구사항이 있더라도 여러 이유로 데이터를 실제로 삭제하는 경우는 많지 않다. 관리자 기능에서 삭제한 데이터까지 조회해야 하는 경우도 있고 데이터 원복을 위해 일정 기간 동안 보관해야 할 때도 있기 때문이다. 이런 이유로 사용자가 삭제 기능을 실행시 데이터를 바로 삭제하기 보단 삭제 플래그를 사용해서 화면에 보여줄지 여부를 결정하는 방식으로 구현한다.

### 매핑 구현

#### 엔티티와 밸류 기본 매핑 구현


```java
@Embeddable
public class Orderer {
	// MemberId에 정의된 칼럼 이름을 변경하기 위해
  // @AttributeOverride 애노테이션 사용
  @Embedded
  @AttributeOverrides(@AttributeOverride(name = "id", column = @Column(name = "orderer_id")))
  private MemberId memberId;
  
  @Column(name = "orderer_name")
  private String name;
}
```

- `@AttributeOverrides` 어노테이션을 이용해서 매핑할 칼럼 이름을 변경한다.

#### 기본 생성자
- JPA의 `@Entity` 와 `@Embeddable` 로 클래스를 매핑하려면 기본 생성자를 제공해야 한다.
- 하이버네이트와 같은 JPA 프로바이더는 DB에서 데이터를 읽어와 매핑된 객체를 생성시 기본 생성자를 사용해서 객체를 생성한다.
  - 이런 기술적 제약으로 Receiver와 같은 불변 타입은 기본 생성자가 필요 없음에도 불구하고 다음과 같이 기본 생성자를 추가해야 한다.
  - 이때 다른 코드에서 기본 생성자를 사용하지 못하도록 protected 로 선언하는 것이 좋다. 

```java
@Embeddable
public class Receiver {
  protected Receiver() {} 
}
```

> **Note**: 하이버네이트는 클래스를 상속한 프록시 객체를 이용해서 지연 로딩을 구현한다. 이 경우 프록시 클래스에서 상위 클래스의 기본 생성자를 호출할 수 있어야 하므로 지연 로딩 대상이 되는 @Entity 와 @Embeddable의 기본 생성자는 private 이 아닌 protected로 지정해야 한다.

#### 필드 접근 방식 사용
- JPA는 필드와 메서드(get/set)의 두 가지 방식으로 매핑을 처리할 수 있다.
- 엔티티를 객체가 제공할 기능 중심으로 구현하도록 유도하려면 JPA 매핑 처리를 프로퍼티 방식이 아닌 필드 방식으로 선택해서 불필요한 get/set/ 메서드를 구현하지 말아야 한다.

```java
@Entity
// @Access(AccessType.PROPERTY)
@Access(AccessType.FIELD)
public class Order {
  @EmbeddableId
  private OrderNo number;
  
  ...
}
```

> **Note**: JPA 구현체인 하이버네이트는 @Access를 이용해 명시적으로 접근 방식을 지정하지 않으면 @Id나 @EmbeddId가 어디에 위치했느냐에 따라 접근 방식을 결정한다. 즉, 필드에 위치하면 필드 접근 방식을, get 메서드에 위치하면 메서드 접근 방식을 선택한다.

#### AttributeConverter를 이용한 밸류 매핑 처리
- JPA 2.0 버전에선 두 개 이상의 프로퍼티를 가진 밸류 타입을 한개 DB칼럼에 매핑하기 위해 칼럼과 매핑하기 위한 프로퍼티를 따로 추가하고 get/set 메서드에서 실제 밸류 타입과 변환 처리를 해야 했다.

```java
public class Product {
  @Column(name = "WIDTH")
  private String width;

  public Length getWidth() {
    return new Length(width); // DB 칼럼 값을 실제 프로퍼티 타입으로 변환
  }

  void setWidth(Length width) {
    this.width = width.toString(); // 실제 프로퍼티 타입을 DB 칼럼 값으로 변환
  }
}
```

- JPA 2.1에선 `AttributeConverter`를 사용해서 밸류 타입과 칼럼 데이터 간의 변화 처리를 위한 기능을 사용할 수 있다.
- `autoApply`가 true일 경우, 모델에 출현하는 모든 Money 타입의 프로퍼티에 대해 MoneyConverter를 자동으로 적용한다.
  - false 인 경우 프로퍼티 값을 변환할 때 사용할 컨버터를 직접 지정할 수 있다.

```java
public interface AttributeConverter<X, Y> {
    Y convertToDatabaseColumn(X var1);

    X convertToEntityAttribute(Y var1);
}

// autoApply가 true일 경우, 모델에 출현하는 모든 Money 타입의 프로퍼티에 대해 MoneyConverter를 자동으로 적용한다.
// false인 경우, 프로퍼티 값을 변환할 때 사용할 컨버터를 직접 지정할 수 있다.
@Converter(autoApply = true)
public class MoneyConverter implements AttributeConverter<Money, Integer> {
  @Override
  public Integer convertToDatabaseColumn(Money money) {
    if (money == null) {
      return null;
    } else {
      return money.getValue();
    }
  }
  
  @Override
  public Money convertToEntityAttribute(Integer value) {
    if (value == null) return null;
    else return new Money(value);
  }
}

public class Order {
  ...

  @Column(name = "total_amounts")
  @Convert(converter = MoneyConverter.class)
  private Money toalAmounts;
}
```

#### 밸류 컬렉션: 별도 테이블 매핑
- 밸류 타입 컬렉션은 별도 테이블에 보관한다.
  
![image](https://user-images.githubusercontent.com/44339530/235647485-edc2a27c-ba9e-4c90-977a-72b1f9da75da.png)

- 밸류 컬렉션을 저장하는 ORDER_LINE 테이블은 외부키를 이용해서 엔티티에 해당하는 PURCHASE_ORDER 테이블을 참조한다.
- 이 외부키는 컬렉션이 속할 엔티티를 의미한다.
- List 타입의 컬렉션은 인덱스 값이 필요하므로 ORDER_LINE 테이블엔 인덱스 값을 저장하기 위한 칼럼(line_idx)도 존재한다.
- 밸류 컬렉션을 별도 테이블로 매핑할 땐 `@ElementCollection`과 `@CollectionTable` 을 함께 사용한다.

```java
@Entity
@Table(name = "purchase_order")
public class Order {
  ...
  @ElementCollection
  @CollectionTable(name = "order_line", joinColumns = @JoinColumn(name = "order_number"))
  @OrderColumn(name = "line_idx")
  private List<OrderLine> orderLines;
  ...
}

@Embeddable
public class OrderLine {
  @Embedded
  private ProductId productId;
  
  @Column(name = "price")
  private Money price;
  
  ...  
}
```

- `@OrderColumn` 어노테이션으로 지정한 칼럼에 리스트의 인덱스 값을 지정한다.
- `@CollectionTable` 은 밸류를 저장할 테이블을 지정할 때 사용한다.

#### 밸류 컬렉션: 한 개 칼럼 매핑
- 밸류 컬렉션을 별도 테이블이 아닌 한 개 칼럼에 저장해야 할 때가 있다.
- `AttributeConverter` 를 사용하면 쉽게 해결할 수 있는데 밸류 컬렉션을 표현하는 새로운 밸류 타입을 추가해야 한다.(일급 컬렉션)

```java
public class EmailSet {
  private Set<Email> emails = new HashSet<>();
  
  private EmailSet(){}
  private EmailSet(Set<Email> emails) {
    this.emails.addAll(emails);
  }
  
  public Set<Email> getEmails() {
    return Collections.unmodifiableSet(emails);
  }
}
```

```java
@Converter
public class EmailSetConverter implements AttributeConverter<EmailSet, String> {
  @Override
  public String convertToDatabaseColumn(EmailSet attribute) {
    if (attribute == null) return null;
    return attribute.getEmails().stream()
      .map(Email::toString)
      .collect(Collectors.joining(","));
  }
  
  @Override
  public EmailSet convertToEntityAttribute(String dbData) {
    if (dbData == null) return null;
    String emails = dbData.split(",");
    Set<Email> emailSet = Arrays.stream(emails)
      .map(value -> new Email(value))
      .collect(toSet());
    return new EmailSet(emailSet);
  }
}
```

```java
@Column(name = "emails")
@Convert(converter = EmailSetConverter.class)
private EmailSet emailSet;
```

#### 밸류를 이용한 아이디 매핑
- 밸류 타입을 식별자로 매핑하면 `@Id` 대신 `@EmbeddedId` 어노테이션이 필요하다.
- JPA에서 식별자 타입은 Serializable 타입이어야 하므로 Serializable 인터페이스를 상속받아야 한다.
- 밸류 타입의 식별자를 구현할 때 얻을 수 있는 장점은 식별자에 기능을 추가할 수 있다. (is2ndGeneration 메서드 참조)

```java
@Entity
@Table(name = "purchase_order")
public class Order {
  @EmbeddedId
  private OrderNo orderId;
  ...
}

@Embeddable
public class OrderNo implements Serializable {
  @Column(name = "order_number")
  private String number;
  
  // 1세대 시스템의 주문 번호와 2세대 시스템의 주문 번호가 있다고 가정할 때 이를 구분하기 위한 메서드 구현
  public boolean is2ndGeneration() {
    return number.startWith("N");
  }
}
```

#### 별도 테이블에 저장하는 밸류 매핑
- 애그리거트에 속한 객체가 밸류인지 엔티티인지 구분하는 방법은 고유 식별자를 갖는지 여부를 확인하는 것이다.
- 하지만, 식별자를 찾을 때 매핑되는 테이블의 식별자(PK)를 애그리거트 구성요소의 식별자와 동일한 것으로 착각해선 안된다.
- 별도 테이블로 저장되고 테이블에 PK가 있다 해서 테이블과 매핑되는 애그리거트 구성요소가 고유 식별자를 갖는 것은 아니다.

![image](https://user-images.githubusercontent.com/44339530/235649211-20ffc22c-f7bb-4f96-80ad-ae9c279e0ac6.png)

- <b>위 이미지에서 ARTICLE_CONTENT 테이블의 ID 칼럼이 식별자이므로 ARTICLE_CONTENT와 매핑되는 AriticleContent를 엔티티로 생각할 수 있는데, 이것 때문에 Article 과 ArticleContent를 두 엔티티 간의 일대일 연관으로 매핑하는 실수를 할 수 있다.</b>
- ArticleContent를 엔티티로 생각할 수 있지만 ArticleContent 는 Article 의 내용을 담고 있는 밸류로 생각하는 것이 맞다.
  - ARTICLE_CONTENT의 ID 는 식별자이기는 하지만 이 식별자를 사용하는 이유는 ARTICLE 테이블의 데이터와 연결하기 위함이지 ARTICLE_CONTENT를 위한 별도 식별자가 필요하기 때문은 아니다.

![image](https://user-images.githubusercontent.com/44339530/235649714-766a4e39-882d-44de-8741-20549f167a97.png)

- 이때 `@SecondaryTable` 과 `@AttributeOverride`를 사용하여 밸류를 매핑한 테이블을 지정한다.

```java
@Entity
@Table(name = "article")
@SecondaryTable(
	name = "article_content",
  pkJoinColumns = @PrimaryKeyJoinColumn(name = "id")
)
public class Article {
  @Id
  private Long id;
  
  @AttributeOverrides({
    @AttributeOverride(name = "content", column = @Column(table = "article_content")),
    @AttributeOverride(name = "contentType", column = @Column(table = "article_content"))
  })
  private ArticleContent content;

  ...
}
```

- `@SecondaryTable`의 name 속성은 밸류를 저장할 테이블을 지정한다.
  - pkJoinColumns 속성은 밸류 테이블에서 엔티티 테이블로 조인할 때 사용할 칼럼을 지정한다.
  - content 필드에 `@AttributeOverride` 를 사용하여 해당 밸류 데이터가 저장된 테이블 이름을 지정한다.
- `@SecondaryTable`을 이용하면 아래 코드를 실행할 때 두 테이블을 조인해서 데이터를 조회한다

```java
Article article = entityManager.find(Article.class, 1L);
```

- 게시글 목록을 보여주는 화면은 article 테이블의 데이터만 필요하지, article_content 테이블의 데이터는 필요하지 않다.
- 그런데, `@SecondaryTable` 을 사용하면 목록 화면에 보여줄 `Article`을 조회할 때 article_content 테이블까지 조인해서 데이터를 읽어오게 되는 문제가 발생한다.
- <b>이 문제를 해결하기 위해 ArticleContent 를 엔티티로 매핑하고 이를 지연 로딩으로 설정하여 해결할 수도 있다. (둘 다 필요할때만 페치 조인으로 가져오는 방식으로)</b>
- <b>하지만 이 방식은 엔티티가 아닌 모델을 엔티티로 만드는 것이므로 좋은 방법은 아니다.</b>
  - <b>대신 조회 전용 기능을 구현하는 방법을 사용하는 것이 좋다.</b>
  - JPA에서 조회 전용 쿼리를 실행하는 방법은 5장에서 살펴본다.




### Reference
- 예제 코드 및 이미지
  - [https://heeveloper.github.io/2020/07/18/04-%EB%A6%AC%ED%8F%AC%EC%A7%80%ED%84%B0%EC%99%80-%EB%AA%A8%EB%8D%B8-%EA%B5%AC%ED%98%84/](https://heeveloper.github.io/2020/07/18/04-%EB%A6%AC%ED%8F%AC%EC%A7%80%ED%84%B0%EC%99%80-%EB%AA%A8%EB%8D%B8-%EA%B5%AC%ED%98%84/)