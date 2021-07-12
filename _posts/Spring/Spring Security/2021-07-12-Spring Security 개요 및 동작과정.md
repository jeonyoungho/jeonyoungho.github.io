---
title: "[개발자 블로그] Spring Security 개요 및 동작 과정" #### post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-07-12 +0800
categories: [Spring] #### categories는 최대 2개까지 가능
tags: [Spring, SpringSecurity] #### TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true #### Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true #### 댓글 유무 지정
#### image: /assets/img/test.png #### Preview image
#### pin: true #### 홈페이지 메인화면에 특정 게시물 고정
---

[실습 코드](https://github.com/jeonyoungho/spring-security)

# Spring Security
- Spring은 매우 막강한 인증과 인가 권한을 가진 프레임워크이다.
- Spring Security는 Filter 기반으로 동작한다.

## Filter기반이 아닌 Interceptor로는 불가능한가?
- 먼저 Filter와 Interceptor의 차이점으로는 작동 위치가 다르다.
    - Filter는 DispatcherServlet 앞 단에서 작동하고 Interceptor는 DistpatcherServlet와 Controller 사이에서 작동한다.

- Interceptor로도 Controller로 오는 Request에 대해 인증 및 인가를 구현할 수 있다.
- 하지만 Spring Security는 애초에 Spring과 관련없이 독립적으로 활동하던 프로젝트였으며 단순하게 서블릿을 방어하기 위한 목적이였기에 필터 기반으로 구현되어 있다.

## 그럼 Filter기반 인증 방식과 Interceptor기반 인증 방식과 무엇이 더 적절할까?
- <b>Filter 기반 방식이 더 적절하다고 볼 수 있다.</b>
- Filter기반으로 구현되면 스프링 웹플로우도 방어 가능하고, 스프링과 연동된 struts와 같은 관련 프로젝트도 동시에 방어 할 수 있다.
- 또한, 최대한 Spring 내부의 로직과 엮이는 것을 방지하여 더 효과적으로 스프링 프로젝트를 관리할 수 있는 측면도 있다.

# Spring Security 동작 과정 
![1](https://user-images.githubusercontent.com/44339530/125274415-e986ad00-e348-11eb-90f7-d53485de0495.png)<br>

1) 사용자가 아이디 비밀번호를 Form을 통해 전송함<br>

2) AuthenticationFilter에서 UsernamePasswordAuthenticationToken을 생성하여 AuthenticationManager에게 전달함<br>

3) AuthenticationManager는 등록된 AuthenticationProvider(들)을 조회하여 인증을 요구함<br>

4) AuthenticationProvider는 UserDetailsService를 통해 입력받은 아이디에 대한 사용자 정보를 DB에서 조회함<br>

5) 입력받은 비밀번호를 암호화하여 DB의 비밀번호화 매칭시켜 일치하는 경우 인증된 UsernamePasswordAuthenticationToken을 생성하여 AuthenticationManager에 전달함<br>

6) AuthenticationManager는 UsernameAuthenticationToken을 AuthenticationFilter로 전달함<br>

7) AuthenticationFilter는 전달받은 UsernameAuthenticationToken을 LoginSuccessHandler로 전송하고, SecurityContextHolder에 저장함<br>

#### 출처
- [https://mangkyu.tistory.com/77](https://mangkyu.tistory.com/77)
- [https://sungminhong.github.io/spring/security/](https://sungminhong.github.io/spring/security/)