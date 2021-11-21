---
title: "[개발자 블로그] MyBatis TypeAlias" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-07-01 +0800
categories: [Spring, Configuration] # categories는 최대 2개까지 가능
tags: [spring, mybatis, typealias] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# TypeAlias 설정
- TypeAlias는 자바타입에 대한 간단하고 짧은 이름이며 XML설정에만 관계된다. 이를 통해 클래스 풀네임 대신 지정한 alias로 대신 명시해줄수 있다.
- 클래스 별로 설정

~~~
<typeAliases>
	<typeAlias type="com.example.vo.MemberVO" alias="MemberVO" />
	<typeAlias type="com.example.vo.SampleVO" alias="SampleVO"/>
</typeAliases>
~~~

- 패키지로 설정

~~~
<typeAliases>
	<package name="com.example.vo"/> 
</typeAliases>
~~~

- annotation으로 설정

~~~
@Alias("alias name")
~~~

- mapper.xml에서 parameterType이나 resultType으로 해당 alias를 사용하면 된다.

~~~
<select id="getLikeHate" parameterType="SampleVO" resultType="java.util.HashMap">
	select * from tbl_member where userid = #{userid}
</select>
~~~

#### 출처
- [https://sas-study.tistory.com/193](https://sas-study.tistory.com/193)
- [https://wondongho.tistory.com/72](https://sas-study.tistory.com/193)