---
title: "[DDDSTART] Chapter4-리포지터리와 모델구현(JPA 중심)" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2023-05-02 15:10:00 +0800
categories: [DDD, DDDSTART] # categories는 최대 2개까지 가능
tags: [ddd, dddstart] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

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

### 밸류 컬렉션을 @Entity 로 매핑하기

- 개념적으로 밸류인데 구현 기술 한계나 팀 표준으로 @Entity를 사용해야 할 때가 있다. 
  - 예를 들어, 이미지 업로드 방식에 따라 이미지 경로와 썸네일 이미지 제공 여부가 달라진다고 했을 때 아래와 같은 계층 구조로 설계할 수 있다.

![image](https://user-images.githubusercontent.com/44339530/236620469-eb4cadc9-4a34-4bc6-afa8-4da88b0d05b5.png)

- JPA는 `@Embeddable` 타입의 클래스 상속 매핑을 지원하지 않는다.
- 따라서 상속 구조를 갖는 밸류 타입을 사용하려면 `@Embeddable` 대신 `@Entity` 를 이용한 상속 매핑으로 처리해야 한다.
- <b>Image는 엔티티가 아니라 밸류이므로 상태를 변경하는 기능은 추가하지 않는다.</b>

```java
@Entity
@Inheritance(startegy = InheritanceType.SINGLE_TABLE)
@Descriminator(name = "image_type")
@Table(name = "image")
public abstract class Image {
  ...
  // 밸류 타입이므로 상태 변경 기능이 있어선 안된다
}

@Entity
@DiscriminatorValue("II")
public class InternalImage extends Image {
  ...
}

@Entity
@DiscrimnatorValue("EI")
public class ExternalImage extends Image {
  ...
}
```

- Image는 밸류이므로 독자적인 라이프사이클을 갖지 않고 Product에 완전히 의존한다.
- 따라서 `cascade` 속성을 이용해서 Product 를 저장시 함께 저장되고, Product 삭제시 함께 삭제되도록 설정한다.
- 리스트에서 Image 객체를 제거하면 DB에서 함께 삭제되도록 orphanRemoval 을 true로 설정한다.

```java
@Entity
@Table(name = "product")
public class Product {
  ...

  @OneToMany(cascade = {CascadeType.PERSIST, CascadeType.REMOVE}, orphanRemoval = true)
  @JoinColumn(name = "product_id")
  @OrderColumn(name = "list_idx")
  private List<Image> images = new ArrayList<>();
  ...
  public void changeImages(List<Image> newImages) {
    images.clear();
    images.addAll(newImages);
  }
}
```

- 하이버네이트에선 위처럼 엔티티를 위한 컬렉션 객체(images)의 clear 메서드를 호출시 select 쿼리로 대상 엔티티를 로딩 후 각 개별 엔티티에 대해 delete 쿼리를 수행한다.
  - 변경 빈도가 낮으면 괜찮지만 빈도가 높으면 전체 서비스 성능에 문제가 될 수 도 있다.
- 하이버네이트는 위와 반대로 `@Embeddable` 타입에 대한 컬렉션의 clear() 메서드를 호출하면 컬렉션에 속한 객체를 로딩하지 않고 한 번의 delete 쿼리로 삭제 처리를 수행한다,,
  - 이 경우엔 타입에 따라 다른 기능을 구현하려면 다음과 같이 if-else를 써야 하는 단점이 발생한다.

```java
@Embeddable
public class Image {
  @Column(name = "image_type")
  private String imageType;
  @Column(name = "image_path")
  private String path;

  ...
  public boolean hasThumbnail() {
    // 성능을 위해 다형을 포기하고 if-else로 구현
    if (imageType.equals("II")) {
      return true;
    }

    return false;
  }
}
```

- 코드 유지보수와 성능의 두 가지 측면을 고려해서 구현방식을 적절히 선택해야 한다.

### ID 참조와 조인 테이블을 이용한 단방향 M:N 매핑
- 앞서 3장에서 애그리거트 간 집합 연관은 성능상의 이유로 피해야 한다고 했다.
- 그럼에도 불구하고 필요하다면 ID 참조를 이용한 단방향 집합 연관을 적용해 볼 수 있다.

```java
@Entity
@Table(name = "product")
public class Product {
  @EmbeddedId
  private ProductId id;
    
  @ElementCollection 
  @CollectionTable(name = "product_category", joinColumns = @JoinColumn(name = "product_id"))
  private Set<CategoryId> categoryIds;

  ...
}
```

- ID 참조를 이용한 애그리거트 간 단방향 M:N 연관은 밸류 컬렉션 매핑과 동일한 방식으로 설정한 것을 알 수 있다.
- `@ElementCollection` 을 이용하기 때문에 Product 를 삭제할 때 매핑에 사용한 조인 테이블의 데이터도 함께 삭제된다.
- 애그리거트를 직접 참조하는 방식을 사용했다면 영속성 전파나 로딩 전략을 고민해야 하는데 ID 참조방식을 사용함으로써 이런 고민을 할 필요가 사라지게 된다.

### 애그리거트 로딩 전략
- JPA 매핑을 설정할 때 항상 기억해야 할 점은 애그리거트에 속한 객체가 모두 모여야 완전한 하나가 된다는 것이다.
-  즉, 다음과 같이 애그리거트 루트를 로딩시 루트에 속한 모든 객체가 완전한 상태여야 함을 의미한다.

```java
// product 는 완전한 하나여야 한다.
Prodcut product = productRepository.findById(id);
```

- 엔티티에 대한 매핑의 fetch 속성을 즉시 로딩(FetchType,EAGER)로 설정하면 find() 메서드로 애그리거트 루트를 구할 때 연관된 구성요소를 DB에서 함께 읽어온다.
- 하지만 컬렉션에 대해 로딩 전략을 EAGER 로 설정시엔 오히려 즉시 로딩 방식이 문제가 될 수 있다.

```java
@Entity
public class Product {
  ...
  @OneToMany(
    cascade = {CascadeType.PERSIST, CascadeType.REMOVE},
    orphanRemoval = true,
    fetch = FetchType.Eager)
  @JoineColumn(name = "product_id")
  @OrderColumn(name = "list_idx")
  private List<Image> images = new ArrayList<>();

  @ElementCollection(fetch = FetchType.EAGER)
  @CollectionTable(name = "product_option", joinColumns = @JoinColumn(name = "product_id"))
  @OrderColumn(name = "list_idx")
  private List<Option> options = new ArrayList<>();
  
  ...
}
```

- 위와 같이 images 와 options 모두 EAGER 로 지정되어있을때 카타시안 조인을 사용해 불러오는데 이는 쿼리 결과에 중복을 발생한다.
  - Proudct의 image 가 2개이고 option이 2개이면 쿼리 결과로 구해지는 행 개수는 4개일 것이다.
  - 만약 데이터가 많다고하면 성능 문제가 될 수도 있다.
- <b>애그리거트는 개념적으로 하나여야 하지만, 루트 엔티티를 로딩하는 시점에 애그리거트에 속한 객체를 모두 로딩해야 하는 것은 아니다.</b>
- 애그리거트가 완전해야 하는 이유는 두 가지다.
  - 1)상태를 변경하는 기능 실행시 애그리거트 상태가 완전해야 하기 때문에
  - 2)표현 영역에서 애그리거트의 상태 정보를 보여줄 때 필요하기 때문에
- 두 번째 이유는 별도의 조회 전용 기능을 구현하는 방식을 사용하는 것이 유리할 때가 많기에 애그리거트의 완전한 로딩과 관련된 문제는 상태 변경과 더 관련이 있다.
- 하지만 상태 변경 기능을 실행하기 위해 조회 시점에 즉시 로딩을 이용해서 애그리거트를 완전한 상태로 로딩할 필욘 없다.
- JPA는 트랜잭션 범위 내에서 지연 로딩을 허용하기 때문에 다음 코드처럼 실제로 상태를 변경하는 시점에 필요한 구성요소만 로딩해도 문제가 되지 않는다.

```java
@Transactional
public void removeOptions(ProductId id, int optIdxToBeDeleted) {
  // Product 를 로딩. 컬렉션은 지연 로딩으로 설정했다면, Option은 로딩하지 않음
  Prodcut product = productRepository.findById(id);
  // 트랜잭션 범위이므로 지연 로딩으로 설정한 연관 로딩 가능
  product.removeOption(optIdxToBeDeleted);
}

@Entity
public class Product {
  @ElementCollection(fetch = FetchType.LAZY)
  @CollectionTable(name = "product_option", joinColumns = @JoinColumn(name = "product_id"))
  @OrderColumn(name = "list_idx")
  private List<Option> options = new ArrayList<>();
  
  public void removeOption(int optIdx) {
    // 실제 컬렉션에 접근할 때 로딩
    this.options.remove(optIdx);
  }
}
```

- 상태를 변경하는 기능을 실행하는 빈도보다 조회하는 기능을 실행하는 빈도가 훨씬 높다.
  - 그러므로 상태 변경을 위해 지연 로딩을 사용할 때 발생하는 추가 쿼리로 인한 실행 속도 저하는 문제 되지 않는다.
- 위와 같은 이유로 애그리거트 내의 모든 연관을 즉시 로딩으로 설정할 필욘 없다. 애그리거트에 맞게 즉시 로딩과 지연 로딩을 적절히 선택해야 한다.

### 애그리거트의 영속성 전파
- 애그리거트를 저장하거나 삭제할땐 애그리거트에 속한 모든 객체를 함께 저장하거나, 함께 삭제해야 한다.
- `@Embeddable` 매핑 타입의 경우엔 함께 저장되고 삭제되므로 cascade 속성을 추가로 설정하지 않아도 된다.
- 반면에 @Entity 타입에 대한 매핑은 cascade 속성을 사용해서 저장과 삭제시 함께 처리되도록 설정해야 한다.
- `@OneToOne`, `@OneToMany` 는 cascade 속성의 기본값이 없으므로 다음 코드처럼 cascade 속성 값으로 CascadeType.PERSIST, CascadeType.REMOVE 를 설정해야 한다.

```java
@OneToMany(cascade = {CascadeType.PERSIST, CascadeType.REMOVE},
          orphanRemoval = true)
@JoinColumn(name = "product_id")
@OrderColumn(name = "list_idx")
private List<Image> images = new ArrayList<>();
```

### 식별자 생성 기능
- 식별자는 아래 세 가지 방식 중 하나로 생성한다.
  - 1)사용자가 직접 생성
  - 2)도메인 로직으로 생성
  - 3)DB 를 이용한 일련번호 생성

#### 1) 사용자가 직접 생성
- 식별자 생성주체가 사용자이므로 도메인 영역에 식별자 생성 기능을 구현할 필요 없다.

#### 2) 도메인 로직으로 생성
- 별도 서비스로 식별자 생성 기능을 분리해야 한다.
- 식별자 생성 규칙은 도메인 규칙이므로 도메인 영역에 식별자 생성 기능을 위치시켜야 한다.

```java
public class OrderIdService {
  public OrderId createId(UserId userId) {
    if (userId == null) {
      throw new IllegalArgumentException("invalid userid: " + userId);

      return new OrderId(userId.toString() + "-" + timestamp());
    }
  }

  private String timestamp() {
    return Long.toString(System.currentTimeMillis());
  }
}
```

- 또한 식별자 생성 규칙을 규현하기에 적합한 또 다른 위치는 리포지터리이다.

```java
public interface ProductRepository {
  ...
  ProductId nexxtId();
  ...
}
```

#### 3) DB 를 이용한 일련번호 생성
- JPA는 저장 시점에 생성한 식별자를 @Id로 매핑한 프로퍼티/필드에 할당한다.
- 실제 저장(영속화)된 후에 할당된 식별자를 사용할 수 있다.

### Reference
- 예제 코드 및 이미지
  - [https://heeveloper.github.io/2020/07/18/04-%EB%A6%AC%ED%8F%AC%EC%A7%80%ED%84%B0%EC%99%80-%EB%AA%A8%EB%8D%B8-%EA%B5%AC%ED%98%84/](https://heeveloper.github.io/2020/07/18/04-%EB%A6%AC%ED%8F%AC%EC%A7%80%ED%84%B0%EC%99%80-%EB%AA%A8%EB%8D%B8-%EA%B5%AC%ED%98%84/)