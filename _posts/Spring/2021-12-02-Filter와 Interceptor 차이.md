---
title: "[Spring] Filter와 Interceptor 차이"
# post의 layout이 기본적으로 post으로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 된다.
date: 2021-12-02 +0800
categories: [Spring] # categories는 최대 2개까지 가능
tags: [spring, filter, interceptor] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
---

# 필터
- 정확한 명칭은 서블릿 필터(스프링에서 제공하는 기능이 아님)
- <b>필터를 적용할 경우 필터가 호출된 다음에 서블릿이 호출</b>
- 따라서 모든 고객의 요청 로그를 남기는 요구사항이 있을 경우 필터를 사용

## 필터 체인
- 필터는 체인으로 구성되며, 중간에 필터를 자유롭게 추가 및 제거할 수 있음
- 예를 들어 로그를 남기는 필터를 먼저 적용한 후 로그인 여부를 체크하는 필터를 이후에 추가할 수 있음
- ex) HTTP 요청 -> WAS -> 필터 1 -> 필터 2 ->... -> 필터 N -> 디스패처 서블릿 -> 컨트롤러

## 필터 역할
- 필터는 적절하지 않은 요청이 들어올 경우 HTTP 요청이 서블릿까지 도달하지 못하게 하는 역할이 있음
- 따라서 Spring Security를 적용할 때 필터에 적용하기도 함
  - 정상적인 요청: HTTP 요청 -> WAS -> 필터1 -> 필터 2 ->... -> 필터 N -> 디스패처 서블릿 -> 컨트롤러
  - 잘 못된 요청: `HTTP 요청 -> WAS -> 필터1 -> 필터 2 -> 서블릿 호출 X `

## 필터 인터페이스

~~~
public interface Filter {

    public default void init(FilterConfig filterConfig) throws ServletException {}

    public void doFilter(ServletRequest request, ServletResponse response,
            FilterChain chain) throws IOException, ServletException;

    public default void destroy() {}
}
~~~

- 인터페이스에는 3가지 메서드가 있고 init과 destory 메서드 같은 경우 default 키워드가 있기 때문에 필요가 없을 경우 별도로 정의하지 않아도 됨
- 웹 관련 공통 사항을 처리하기 때문에 파라미터로 ServletRequest와 ServletResponse 객체가 제공되는 것을 확인할 수 있음
  - 스프링 프레임워크는 확장성을 위해 ServletRequest와 ServletResponse 객체로 제공했지만 대부분 HttpServletRequest와 HttpServletResponse를 사용하기 때문에 다운 캐스팅하여 사용하면 됨

### Filter 인터페이스 메서드
- init() 메서드: 필터 초기화 메서드이며 서블릿 컨테이너가 생성될 때 호출
- doFilter() 메서드: 고객의 요청이 올 때마다 해당 메서드가 호출되며 메인 로직을 이 메서드에 구현하면 됨
- destory() 메서드: 필터 종료 메서드이며 서블릿 컨테이너가 소멸될 때 호출

# 인터셉터
- 필터는 서블릿이 제공하는 기술이지만 스프링 인터셉터는 스프링이 제공하는 기술
- <b>웹 관련 공통 사항을 처리한다는 점에서는 필터와 비슷</b>
- <b>인터셉터는 필터와 달리 디스패처 서블릿과 컨트롤러 사이에서 컨트롤러 호출 직전에 호출됨</b>

## 스프링 인터셉터 체인
- 스프링 인터셉터 또한 체인으로 구성되며 중간에 자유롭게 인터셉터를 추가 및 제거 가능
- 예를 들어 로그를 남기는 인터셉터를 먼저 적용한 후 로그인 여부를 체크하는 인터셉터를 이후에 추가할 수 있음
- ex) <b>HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 인터셉터 1 -> 인터셉터 2 ->... -> 인터셉터 N -> 컨트롤러</b>

## 스프링 인터셉터의 역할
- 스프링 인터셉터 또한 필터처럼 적절하지 않은 요청이 들어올 경우 HTTP 요청이 서블릿까지 도달하지 못하게 하는 역할이 있음
- 따라서, Spring Security를 적용할 때 스프링 인터셉터에 적용하기도 함
- 단, 필터는 서블릿에 도달하기 전에 호출되고 스프링 인터셉터는 서블릿에서 컨트롤러를 호출하기 전에 호출됨
- <b>정상적인 요청: HTTP 요청 -> WAS -> 필터 -> 디스패처 서블릿 -> 인터셉터 1 -> 인터셉터 2 ->... -> 인터셉터 N -> 컨트롤러</b>
- <b>잘 못된 요청: HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 인터셉터 1 -> 인터셉터 2 -> 컨트롤러 호출 안됨</b> 

## 인터셉터 인터페이스

~~~
public interface HandlerInterceptor {

	default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
			throws Exception {

		return true;
	}

	default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
			@Nullable ModelAndView modelAndView) throws Exception {
	}

	default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler,
			@Nullable Exception ex) throws Exception {
	}

}
~~~

- 인터페이스에는 세 가지 메서드가 있고 필터와 달리 세 가지 메서드 모두 default 키워드가 있음
- 서플릿 필터의 경우 단순하게 doFilter 메서드만 제공되었지만 스프링 인터셉터의 경우 컨트롤러 호출 전에 호출되는 preHandle 메서드, 컨트롤러 호출 후 호출되는 postHandle 메서드, 그리고 요청이 완료된 이후 호출되는 afterCompletion 메서드가 단계적으로 잘 세분화되어 필터보다 많은 기능을 제공
- 서블릿 필터의 경우 단순히 request, response만 제공했지만 스프링 인터셉터는 어떤 컨트롤러(handler)가 호출되었는지에 관한 호출 정보도 받을 수 있음
- 또한, 어떤 ModelAndView가 반환되었는지 응답 정보도 받을 수 있음
- <b>정리를 하자면 스프링 인터셉터는 필터와 비슷하게 작동하지만 호출 시점이 다르고 인터셉터가 필터보다 더 많은 기능을 제공</b>

### 메서드 간단 설명
- preHandle() 메서드: 컨트롤러 호출 전, 더 정확히 말하자면 HandlerAdapter 호출 전에 호출
    - preHandle의 반환 값이 true일 경우 다음 체인으로 진행이 되고, false일 경우 더 이상 진행이 되지 않으며 핸들러 어댑터 또한 호출이 되지 않음
- postHandle() 메서드: 컨트롤러 호출 후, 더 정확히 말하자면 HandlerAdapter 호출 후에 호출
- afterCompletion() 메서드: 뷰가 렌더링 된 이후에 호출되며 try catch finally 절에서 finally처럼 무조건 호출됨
 
## 스프링 인터셉터에서 예외 발생할 경우
- 컨트롤러에서 예외가 발생할 경우 postHandle 메서드는 호출되지 않음
- 앞서 언급했듯이 afterCompletion 메서드는 무조건 호출되므로 예외 발생 시 파라미터로 어떤 예외가 발생했는지 알 수 있으며 로그로 출력 가능
- 필터의 경우 예외를 로깅하기 위해서는 톰캣까지 예외를 보내줘야 하지만 인터셉터의 경우 바로 로깅 가능

# 필터와 인터셉터의 공통점 정리
- 둘 다 웹 공통 관심 사항을 처리한다는 점

# 필터와 인터셉터의 차이 정리

![spring-request-lifecycle](https://user-images.githubusercontent.com/44339530/144367286-01555894-b69e-404a-85a6-58e08c4783ac.jpg)
_출처: https://justforchangesake.wordpress.com/2014/05/07/spring-mvc-request-life-cycle/_

## Filter
- 서블릿에서 제공하며 Web Application Context에 등록된다.
- 디스패처 서블릿 앞단에서 실행된다.

# Filter에서만 할 수 있는 것
`ServletRequest` 혹은 `ServletResponse`를 교체할 수 있다. 아래와 같은 일이 가능하다.

~~~
public class SomeFilter implements Filter {
  //...
  
  public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) {
    chain.doFilter(new CustomServletRequest(), new CustomResponse());
  }
}
~~~

설마 저런 일을 할까? 꽤 자주 있는 요구 사항이다. HttpServletRequest의 body(ServletInputStream의 내용)를 로깅하는 것을 예로 들 수 있을 것 같다. HttpServletRequest는 body의 내용을 한 번만 읽을 수 있다. Rest API Application을 작성할 때, 흔히 json 형식으로 요청을 받는다. @Controller(Handler)에 요청이 들어오면서 body를 한 번 읽게 된다. 때문에 Filter나 Interceptor에서는 body를 읽을 수 없다. IOException이 발생한다. body를 로깅하기 위해서는 HttpServletRequest를 감싸서 여러 번 inputStream을 열 수 있도록 커스터마이징 된 ServletRequest를 쓸 수밖에 없다.

~~~
public class SomeFilter implements Filter {
  //...
  
  public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) {
    chain.doFilter(new BodyCachedServletRequestWrapper(request), response);
  }
}
~~~

## Interceptor
- Spring MVC에서 제공하며 Spring Context에 등록된다.
- 디스패처 서블릿과 컨트롤러 사이(정확하게는 HandlerAdapter)에서 실행된다.
- 스프링 인터셉터가 필터보다 개발자 친화적으로 다양한 기능을 제공하므로 서블릿이 호출되기 직전에 처리해야 하는 로직이 아니라면 스프링 인터셉터를 활용하는 것을 추천한다.

### Interceptor에서만 할 수 있는 것
- AOP 흉내를 낼 수 있다. @RequestMapping 선언으로 요청에 대한 HandlerMethod(@Controller의 메서드)가 정해졌다면, handler라는 이름으로 HandlerMethod가 들어온다. HandlerMethod로 메서드 시그니처 등 추가적인 정보를 파악해서 로직 실행 여부를 판단할 수 있다.
- View를 렌더링하기 전에 추가 작업을 할 수 있다. 예를 들어 웹 페이지가 권한에 따라 GNB(Global Navigation Bar)이 항목이 다르게 노출되어야 할 때 등의 처리를 하기 좋다.

#### 출처
- [https://jaimemin.tistory.com/1887](https://jaimemin.tistory.com/1887)
- [https://supawer0728.github.io/2018/04/04/spring-filter-interceptor/](https://supawer0728.github.io/2018/04/04/spring-filter-interceptor/)