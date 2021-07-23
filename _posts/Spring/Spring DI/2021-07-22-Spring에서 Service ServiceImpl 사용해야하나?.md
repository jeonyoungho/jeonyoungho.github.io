---
title: "[개발자 블로그] Spring에서 Service ServiceImpl 사용해야하나?" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-07-22 +0800
categories: [Spring, Spring DI] # categories는 최대 2개까지 가능
tags: [Spring, SpringDI] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

스프링 코드를 보면 Service인터페이스를 두고 ServiceImpl로 이를 구현하는 경우를 자주 볼 수 있다. 이 전에 정리했던 ['스프링 의존성 주입과 인터페이스'](https://jeonyoungho.github.io/posts/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9D%98%EC%A1%B4%EC%84%B1-%EC%A3%BC%EC%9E%85%EA%B3%BC-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4/)에 이어 다시 한 번 더 정리를 하고 싶어졌다.

# Service인터페이스 ServiceImpl로 나누는 이유
<b>1) Loose Coupling</b>
객체 간의 결합도를 낮추어 변화에 유연한 개발을 하기 위해서이다. 하나의 인터페이스를 구현하는 여러 구현체가 있고 기능에 따라 적절한 구현체가 들어가서 다형성을 주기 위함이다. 또 하나의 인터페이스만 바라보니 의존관계도 줄일 수 있다.

<b>2) Spring 에서 JDK Dynamic Proxy 를 사용하여 AOP Proxy 를 만드는 만들기 위해</b>
JDK Dynamic Proxy는 인터페이스 기반으로 프록시 객체를 만들게 되어 있다. 예를 들어, 인터페이스가 있어야지 @Transactional 어노테이션이 동작하게 된다. 

하지만 특정 버전부터 CGLIB 라이브러리를 사용하여, 클래스 기반으로 AOP Proxy 를 만들도록 지원을 하게 되었다. 그래서 개발자는 AOP Proxy를 만드는 방식을 선택을 할 수 있게 되었다.

SpringBoot 1.4 이후로는 디폴트 클래스 기반(GGLIB)으로 만들도록 아래와 같이 설정되어 있다.

~~~
spring.aop.proxy-target-class=true
~~~

- true : CGLIB를 사용하여, 클래스를 상속받아 AOP Proxy를 만듬
- false : JDK Dynamic Proxy를 사용하여 인터페이스를 데코레이션 해서 AOP Proxy를 만듬

> **Note**: JDK Dynamic Proxy와 CGLIB의 차이점에 대해선 다음 포스팅에 정리 예정 [(참고 블로그)](https://gmoon92.github.io/spring/aop/2019/04/20/jdk-dynamic-proxy-and-cglib.html)

# 그럼 어떨때 전략 패턴을 이용하여 구성해야 하는가?
만약 비밀번호 변경 방식이 두 가지가 있다고 가정해보자.

- 1) 비밀번호 기반으로 비밀번호를 변경하는 기능
- 2) 비밀번호를 잃어버렸을 때 다른 인증 기반으로 비밀번호를 변경하는 기능

비밀번호를 변경하는 방식이 일반적으로 2개 이상이다. 즉 구현체를 2개 이상 갖게 되고 이럴 때 인터페이스를 두는 것이 바람직하다.

~~~
// 비밀번호를 바꾸는 인터페이스
public interface ChangePasswordService {
    public void change(MemberId id, PasswordDto.ChangeRequest dto);
}

// 비밀번호 기반으로 비밀번호를 변경하는 기능
public class ByPasswordChangePasswordService implements ChangePasswordService {
    private MemberFindService memberFindService;
    @Override
    public void change(MemberId id, PasswordDto.ChangeRequest dto) {
        if (dto.getPassword().equals("비밀번호가 일치하는지 판단 로직...")) {
            final Member member = memberFindService.findById(id);
            final String newPassword = dto.getNewPassword().getValue();
            member.changePassword(newPassword);
        }
    }
}

// 비밀번호를 잃어버렸을 때 다른 인증 기반으로 비밀번호를 변경하는 기능
public class ByAuthChangePasswordService implements ChangePasswordService {
    private MemberFindService memberFindService;
    @Override
    public void change(MemberId id, PasswordDto.ChangeRequest dto) {
        if (dto.getAuthCode().equals("인증 코드가 적합한지 로직 추가...")) {
            final Member member = memberFindService.findById(id);
            final String newPassword = dto.getNewPassword().getValue();
            member.changePassword(newPassword);
            // 필요로직...
        }
    }
}
~~~

ChangePasswordService 인터페이스의 책임은 비밀번호를 변경하는 것이다. 해당 구현체들은 비밀번호 변경에 대한 다양한 로직을 구현하게 된다. 이것을 인터페이스를 둘 수 있는 이유는 인터페이스의 책임이 하나이기 때문입니다.

하나의 인터페이스에 너무 많은 책임을 가지게 하는 것은 문제가 되며 그렇다고 하나의 인터페이스가 하나의 메소드만 가지게 되는 것은 아니다. 

~~~
public interface CardPaymentService {
    void pay();
    void cancel();
}
~~~

일반적으로 카드는 결제가 있으면 반드시 취소 기능도 있어야 한다. 다양한 카드사들도 당연히 결제 취소 기능이 있을 것이다. 이처럼 인터페이스가 '카드 결제'라는 하나의 책임을 가질지라도 카드 결제와 관련된 다양한 메소드가 존재할 수 있음을 확인할 수 있다.

# 정리
<b>무조건 Service인터페이스와 ServiceImpl로 나누어 구현하기보단, OCP를 준수하며 전략을 쉽게 바꿀 이유가 있는지에 대한 여부를 판단 후 구현하는게 좋다.</b>

#### 출처
- [https://www.popit.kr/spring-oop-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%EC%98%88%EC%A0%9C1-service-serviceimpl-%EA%B5%AC%EC%A1%B0%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B3%A0%EC%B0%B0/](https://www.popit.kr/spring-oop-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%EC%98%88%EC%A0%9C1-service-serviceimpl-%EA%B5%AC%EC%A1%B0%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B3%A0%EC%B0%B0/)
- [https://hyunsoori.tistory.com/11](https://hyunsoori.tistory.com/11)