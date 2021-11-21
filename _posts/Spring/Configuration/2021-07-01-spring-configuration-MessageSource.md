---
title: "[개발자 블로그] MessageSource" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-07-01 +0800
categories: [Spring, Configuration] # categories는 최대 2개까지 가능
tags: [spring, messagesource] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# 다국어 처리를 위한 MessageSource
- 'messageSource'가 이름인 빈 객체 정의

~~~
<bean id="messageSource"
     class="org.springframework.context.support.ResourceBundleMessageSource">
    <property name="basename">
        <value>message.label</value>
    </property>
</bean>
~~~
 
- basename 프로퍼티의 값은 메시지를 로딩할 때 사용할 ResourceBundle 의 베이스 이름 (패키지를 포함한 전체 이름이어야 함.)
- 위와 같은 설정은 message.label 값은 message 패키지에 있는 label 프로퍼티 파일로부터 메시지를 가져옴

- message패키지 밑에 label_ko.Properties와 label_en.Properties파일을 생성 후 다음과 같이 입력

~~~
< label_ko.Properties >
hi=안녕하세요
~~~

~~~
< label_ko.Properties >
hi=Hello
~~~

- 그리고 입력한 메시지의 Locale에 따라 다음과 같이 출력

~~~
messageSource.getMessage("hi", "default message", Locale.KOREA )); // 안녕하세요 출력
messageSource.getMessage("hi", "default message", Locale.US)); // hi 출력
~~~

#### 출처
- [http://egloos.zum.com/ultteky/v/10520923](http://egloos.zum.com/ultteky/v/10520923)
- [https://javaslave.tistory.com/69](https://javaslave.tistory.com/69)