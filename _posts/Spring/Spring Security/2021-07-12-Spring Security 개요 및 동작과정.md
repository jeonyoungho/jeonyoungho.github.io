---
title: "[개발자 블로그] Spring Security 개요 및 동작 과정"
date: 2021-07-12 +0800
categories: [Spring]
tags: [spring, springsecurity]
toc: true
comments: true
---

[실습 코드](https://github.com/jeonyoungho/spring-security)

# Spring Security
- Spring은 매우 막강한 인증과 인가 권한을 가진 프레임워크이다.
- Spring Security는 Filter 기반으로 동작한다.

![2](https://user-images.githubusercontent.com/44339530/125277665-b0e8d280-e34c-11eb-8824-49f194546c71.jpeg)<br>

- Spring Security는 상당히 많은 Filter들을 기반으로 Filter체인을 구성한다.
![3](https://user-images.githubusercontent.com/44339530/125278152-53a15100-e34d-11eb-98a2-0e8734674451.jpeg)<br>

## Filter기반이 아닌 Interceptor로는 불가능한가?
- 먼저 Filter와 Interceptor의 차이점으로는 작동 위치가 다르다.
    - Filter는 DispatcherServlet 앞 단에서 작동하고 Interceptor는 DistpatcherServlet과 Controller 사이에서 작동한다.

- Interceptor로도 Controller로 오는 Request에 대해 인증 및 인가를 구현할 수 있다.
- 하지만 Spring Security는 애초에 Spring과 관련없이 독립적으로 활동하던 프로젝트였으며 단순하게 서블릿을 방어하기 위한 목적이였기에 필터 기반으로 구현되어 있다.

## 그럼 Filter기반 인증 방식과 Interceptor기반 인증 방식과 무엇이 더 적절할까?
- <b>Filter 기반 방식이 더 적절하다고 볼 수 있다.</b>
- Filter기반으로 구현되면 스프링 웹플로우도 방어 가능하고, 스프링과 연동된 struts와 같은 관련 프로젝트도 동시에 방어 할 수 있다.
- 또한, 최대한 Spring 내부의 로직과 엮이는 것을 방지하여 더 효율적으로 스프링 프로젝트를 관리할 수 있는 측면도 있다.

# Spring Security의 특징
1) 서블릿 API 통합<br>

2) Spring Web MVC와의 선택적 통합<br>

3) 인증과 권한 부여를 모두 포괄적이고 확장 가능한 지원<br>

4) 세션 고정, clickjacking, 사이트간 요청 위조 등과 같은 공격으로부터 보호<br>
- 세션 고정: 사용자 로그인 시 항상 일정하게 고정된 세션 ID값을 사용하는 취약점
- Clickjacking: 사용자가 클릭하고 있다고 인지하는 것과 다른 어떤 것을 클릭하게 속이는 악의적인 기법
- 사이트간 요청 위조: csrf라 부르며, 사용자가 자신의 의지와는 무관하게 공격자가 의도한 행위를 특정 웹 사이트에 요청하게 하는 악의적인 기법

# Spring Security 동작 과정 
![1](https://user-images.githubusercontent.com/44339530/125274415-e986ad00-e348-11eb-90f7-d53485de0495.png)<br>

1) 사용자가 아이디 비밀번호를 Form을 통해 전송함<br>

2) AuthenticationFilter에서 UsernamePasswordAuthenticationToken을 생성하여 AuthenticationManager에게 전달함<br>

3) AuthenticationManager는 등록된 AuthenticationProvider(들)을 조회하여 인증을 요구함<br>

4) AuthenticationProvider는 UserDetailsService를 통해 입력받은 아이디에 대한 사용자 정보를 DB에서 조회함<br>

5) 입력받은 비밀번호를 암호화하여 DB의 비밀번호와 매칭시켜 일치하는 경우 인증된 UsernamePasswordAuthenticationToken을 생성하여 AuthenticationManager에 전달함<br>

6) AuthenticationManager는 UsernameAuthenticationToken을 AuthenticationFilter로 전달함<br>

7) AuthenticationFilter는 전달받은 UsernameAuthenticationToken을 LoginSuccessHandler로 전송하고, SecurityContextHolder에 저장함<br>

#### 출처
- [https://mangkyu.tistory.com/77](https://mangkyu.tistory.com/77)
- [https://sungminhong.github.io/spring/security/](https://sungminhong.github.io/spring/security/)
- [https://www.youtube.com/watch?v=aEk-7RjBKwQ](https://www.youtube.com/watch?v=aEk-7RjBKwQ)