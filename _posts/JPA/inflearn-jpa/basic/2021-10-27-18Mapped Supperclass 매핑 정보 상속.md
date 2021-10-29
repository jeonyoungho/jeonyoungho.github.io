---
title: "[자바 ORM 표준 JPA 프로그래밍-기본편] Mapped Supperclass - 매핑 정보 상속" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-10-27 +0800
categories: [JPA, 인프런JPA-기본편] # categories는 최대 2개까지 가능
tags: [java, spring, jpa] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

본 포스팅은 인프러의 [JPA 기본편](https://www.inflearn.com/course/ORM-JPA-Basic#)을 수강하고 정리하는 내용입니다.

<hr>

# @MappedSuperclass
<b>공통 매핑 정보가 필요할 때 사용(id, name)한다.</b>

![image](https://user-images.githubusercontent.com/44339530/139006517-7aa831ed-2e07-4533-a718-69ba36c91f60.png)

- 상속관계 매핑X
- 엔티티X, 테이블과 매핑X
- 부모 클래스를 상속 받는 <b>자식 클래스에 매핑 정보만 제공</b>
- 조회, 검색 불가(<b>em.find(BaseEntity) 불가</b>)
- <b>직접 생성해서 사용할 일이 없으므로 추상 클래스 권장</b>
- 테이블과 관계 없고, 단순히 엔티티가 공통으로 사용하는 매핑정보를 모으는 역할
- 주로 등록일, 수정일, 등록자, 수정자 같은 전체 엔티티에서 공통으로 적용하는 정보를 모을 때 사용
- 참고: @Entity 클래스는 엔티티나 @MappedSuperclass로 지정한 클래스만 상속 가능

- entity class

~~~
@MappedSuperclass
public abstract class BaseEntity {

    @Column(name = "INSERT_MEMBER")
    private String createdBy;
    private LocalDateTime createdDate;
    @Column(name = "UPDATE_MEMBER")
    private String lastModifiedBy;
    private LocalDateTime lastModifiedDate;

    ...
}

@Entity
public class Member extends BaseEntity {

    @Id
    @GeneratedValue
    @Column(name = "MEMBER_ID")
    private Long id;

    @Column(name = "USERNAME")
    private String username;

    @ManyToOne
    @JoinColumn(name = "TEAM_ID")
    private Team team;

    ...
}
~~~

- main class

~~~
try {
    Member member = new Member();
    member.setUsername("user1");
    member.setCreatedBy("kim");
    member.setCreatedDate(LocalDateTime.now());

    em.persist(member);

    em.flush();
    em.clear();

    tx.commit();
} catch (Exception e) {
    tx.rollback();
} finally {
    em.close();
}
~~~

- DDL

~~~
create table Member (
  MEMBER_ID bigint not null,
  INSERT_MEMBER varchar(255),
  createdDate timestamp,
  UPDATE_MEMBER varchar(255),
  lastModifiedDate timestamp,
  USERNAME varchar(255),
  LOCKER_ID bigint,
  TEAM_ID bigint,
  primary key (MEMBER_ID)
)

create table Team (
  TEAM_ID bigint not null,
  INSERT_MEMBER varchar(255),
  createdDate timestamp,
  UPDATE_MEMBER varchar(255),
  lastModifiedDate timestamp,
  name varchar(255),
  primary key (TEAM_ID)
)
~~~