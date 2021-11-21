---
title: "[개발자 블로그] Spring BasicDataSource configuration" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-07-01 +0800
categories: [Spring, Configuration] # categories는 최대 2개까지 가능
tags: [spring, datasource] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# BasicDataSource

~~~
<bean id="dataSource" class="org.apache.commons.dbcp2.BasicDataSource" destroy-method="close">
	<property name="driverClassName" value="${jdbc.driver}" />
	<property name="url" value="${jdbc.url}" />
	<property name="username" value="${jdbc.username}" />
	<property name="password" value="${jdbc.password}" />
	<property name="initialSize" value="5"/> 
	<property name="maxTotal" value="5" />
	<property name="maxIdle" value="5" />
	<property name="maxWaitMillis" value="50000" />
	<property name="timeBetweenEvictionRunsMillis" value="500000"/>
	<property name="minEvictableIdleTimeMillis" value="50000" />
	<property name="defaultAutoCommit" value="false" />
	<property name="testWhileIdle" value="true" />
	<property name="validationQuery" value="SELECT 1" /> <!-- For MsSql, PostgreSQL -->
</bean>
~~~

- Property
	- driverClassName: 해당 DBMS에 해당하는 driverClassName지정
	- url: jdbc URL설정
	- username: username 설정
	- password: password 설정
	- initialSize: BasicDataSource 클래스 생성 후 최초로 getConnection() 메서드를 호출할 때 커넥션 풀에 채워 넣을 커넥션 개수
	- maxTotal: 동시에 사용할 수 있는 최대 커넥션 개수(기본값: 8)
	- maxIdle: 커넥션 풀에 반납할 때 최대로 유지될 수 있는 커넥션 개수(기본값: 8)
	- maxWaitMillis: 커넥션 풀 안의 커넥션이 고갈됐을 때 커넥션 반납을 대기하는 시간(밀리초)
	- timeBetweenEvictionRunsMillis: Evictor 스레드가 동작하는 간격. 기본값은 -1이며 Evictor 스레드의 실행이 비활성화돼 있음
	- minEvictableIdleTimeMillis: Evictor 스레드 동작 시 커넥션의 유휴 시간을 확인해 설정 값 이상일 경우 커넥션을 제거한다(기본값: 30분)
	- defaultAutoCommit: defaultAutoCommit 속성을 false로 설정하면 애플리케이션에서 트랜잭션 처리가 되어 있지 않은 경우에는 INSERT 쿼리나 UPDATE 쿼리가 제대로 반영되지 않음
	- testWhileIdle: Evictor 스레드가 실행될 때 (timeBetweenEvictionRunMillis > 0) 커넥션 풀 안에 있는 유휴 상태의 커넥션을 대상으로 테스트 실행(기본값: false), 만약 오랫동안 대기 상태였던 커넥션이 끊어지는 현상을 방지하려면 true로 설정해야 함
	- validationQuery: JDBC 커넥션의 유효성 확인

# SqlSessionFactoryBean

~~~
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
	<property name="dataSource" ref="dataSource"></property>
	<property name="configLocation" value="classpath:/mybatis-config.xml" />
	<property name="mapperLocations" value="classpath:mapper/${jdbc.type}/**/*.xml" />
</bean>
~~~

- Property<br>
	- dataSource: dataSource지정<br>
	- configLocation: mybatis의 config파일 설정<br>
	- mapperLocations: 매퍼의 위치를 설정 (예시에서는 jdbc의 타입을 통해 매퍼의 위치를 달리 설정하고 있음)<br>

#### 출처
- [https://d2.naver.com/helloworld/5102792](https://d2.naver.com/helloworld/5102792)
- [https://kookyungmin.github.io/server/2018/08/13/spring_06/](https://kookyungmin.github.io/server/2018/08/13/spring_06/)
- [https://lemontia.tistory.com/406](https://lemontia.tistory.com/406)