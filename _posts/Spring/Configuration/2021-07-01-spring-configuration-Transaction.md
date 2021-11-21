---
title: "[개발자 블로그] Spring Transaction" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-07-01 +0800
categories: [Spring, Configuration] # categories는 최대 2개까지 가능
tags: [spring, transaction] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# Spring 트랜잭션 처리
- 서비스단 비즈니스 로직에서의 트랜잭션 처리는 굉장히 중요하다.<br>
- 이를 처리하기 위한 트랜잭션은 두 가지 설정 방법이 존재한다.<br>

#### 1) Annotation을 이용한 설정<br>
- context에 다음과 같이 설정

~~~
<tx:annotation-driven transaction-manager="transactionManager" />
~~~

- 트랜잭션을 적용할 클래스, 인터페이스, 메소드에 @Transactional 어노테이션을 선언

~~~
public interface MemberService {
	public String selectUser(String userId);
	@Transactional
	public int inserUser(User user);
}

@Service
@Transactional
public class MemberServiceImpl implements MemberService {
	@Autowired
	MemberDao dao;
	
	public selectUser(String userId) {
		return dao.getUser(userId);
	}
	
	@Transactional
	public int insertUser(User user) {
		user.setUserno(dao.insertUser(user));
		return dao.insertUserIntereset(user);
	}
}
~~~ 

#### 2) &lt;tx:advice&gt; 태그를 이용한 설정

#### 출처
- [https://ssmlim.tistory.com/45](https://ssmlim.tistory.com/45)