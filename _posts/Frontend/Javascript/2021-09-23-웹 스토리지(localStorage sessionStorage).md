---
title: "[Javascript] 웹 스토리지(localStorage, sessionStorage)" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-09-23 +0800
categories: [Frontend, Javascript] # categories는 최대 2개까지 가능
tags: [javscript, localstorage, sessionstorage] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

자바스크립트로 웹 개발을 하다보면 아무리 간단한 애플리케이션이라도 데이터를 어딘가에 저장해야 할 일이 생긴다. 보통 이럴 때 데이터베이스(DB)서버나 클라우드(Cloud)플랫폼에 데이터를 저장하는 경우가 많을 것이다.

하지만 저장해야할 데이터가 별로 중요하지 않거나,유실되도 무방할 데이터라면 서버 단에 데이터를 저장하는 것이 낭비일 수가 있다. 이때 사용되는 것이 `웹 스토리지`이다.클라이언트 단, 즉 브라우저 상에 데이터를 저장할 수 있는 기술이 `웹 스토리지`이다.

# 로컬 스토리지 vs 세션 스토리지
`웹 스토리지(web storage)`에는 `로컬 스토리지(localStorage)`와 `세션 스토리지(sessionStorage)`가 있다. 이 두 개의 차이점은 데이터가 어떤 범위 내에서 얼마나 오래 보존되느냐에 있다.
- 세션 스토리지: 웹 페이지의 세션이 끝날 때 저장된 데이터가 전부 소멸된다.
- 로컬 스토리지: 세션이 끝나더라도 데이터가 지워지지 않는다.

즉 브라우저에서 같은 웹 사이트를 여러 탭이나 창에 띄우면, 여러 개의 세션 스토리지에 데이터가 서로 격리되어 저장되며 각 탭이나 창이 닫힐 때 저장해 둔 데이터도 함께 소멸한다. 반면에, 로컬 스토리지의 경우 여러 탭이나 창 간에 데이터가 서로 공유되며 탭이나 창을 닫아도 데이터는 브라우저에 그대로 남아 있다. [todomvc.com](https://todomvc.com/examples/vanillajs/#/) 은 해야 되는 일들에 대한 데이터들을 로컬 스토리지에 저장한다. 그러므로 여러 개의 창을 띄워도 동일한 todo 항목들이 보이게 되는 것이다. 로컬 스토리지의 데이터들은 도메인(www.xxx.xxxx)에 종속적이다.

하지만 이러한 로컬 스토리지의 데이터 영속성(persistence) 어디까지나 계속해서 동일한 컴퓨터에서 동일한 브라우저를 사용할 때만 해당한다. 즉, 같은 컴퓨터에서 다른 브라우저를 사용하거나 (e.g. 크롬을 쓰다가 사파리를 쓰면), 또는 다른 컴퓨터에서 같은 브라우저를 사용하는 경우에는 (e.g. 집에서 크롬을 쓰다가 회사에서 크롬을 쓰면), 엄연히 다른 브라우저이므로 서로 다른 두 개의 로컬 스토리지에 데이터가 저장될 것이다. <b>(다른 기기나 브라우저 간에 데이터가 공유되고 영속되야 한다면 당연히 클라우드(Cloud) 플랫폼이나 데이터베이스(DB) 서버를 사용해야 한다!)</b>

<b>로컬 스토리지와 세션 스토리지의 공통점은 두 기술 모두 데이터를 브라우저 상에 저장한다는 것이며, 자바스크립트 API가 완전히 동일한 형태이다.</b> 따라서, 로컬 스토리지 기준으로만 사용 예시를 설명하고 세션 스토리지를 사용하실 때는 예제 코드의 localStorage 부분을 sessionStorage로 대체하기만 하면 된다.

# 기본 API
웹 스토리지는 기본적으로 키(key)와 값(value)으로 이루어진 데이터를 저장할 수 있다. 개념적으로 해시 테이블 자료 구조를 생각하시면 이해가 쉽다. 자바스크립트 API의 기본적인 사용 방법은 아래와 같다.

~~~
// 키에 데이터 쓰기
localStorage.setItem("key", value);

// 키로 부터 데이터 읽기
localStorage.getItem("key");

// 키의 데이터 삭제
localStorage.removeItem("key");

// 모든 키의 데이터 삭제
localStorage.clear();

// 저장된 키/값 쌍의 개수
localStorage.length;
~~~

엄밀하게는 `window.localStorage`를 사용해야하지만, `window` 객체의 대부분의 속성이 그러하듯, 줄여서 `localStorage`로 로컬 스토리지 객체에 접근할 수 있다. 브라우저의 콘솔 창을 열고 아래와 같이 테스트를 해보면 대강 어떻게 사용하는지 금방 감이 잡힌다.

~~~
> localStorage.getItem('name')
null
> localStorage.getItem('email')
null
> localStorage.setItem('email', 'test@user.com')
undefined
> localStorage.getItem('email')
"test@user.com"
> localStorage.setItem('email', 'test@admin.com')
undefined
> localStorage.getItem('email')
"test@admin.com"
> localStorage.removeItem('email')
undefined
> localStorage.getItem('email')
null
~~~

## 주의 사항
웹 스토리지를 사용할 때 주의해야 할 부분이 하나 있다.` 오직 문자형(string) 데이터 타입만 지원`한다는 것입니다. 예를 들어, 숫자형 데이터를 로컬 스토리지에 쓰고 다시 다시 읽어보면 아래과 같이 본래 숫자가 아닌 문자가 나오는 것을 알 수 있다.

~~~
> localStorage.setItem('num', 1)
undefined
> localStorage.getItem('num') === 1
false
> localStorage.getItem('num')
"1"
> typeof localStorage.getItem('num')
"string"
~~~

<b>이러한 웹 스토리지의 성질 때문에 객체형 데이터를 저장할 때 다음과 같이 큰 낭패를 보게 될 수도 있다.</b>

~~~
> localStorage.setItem('obj', {a: 1, b: 2})
undefined
> localStorage.getItem('obj')
"[object Object]"
~~~

이러한 문제가 발생하는 이유는 웹 스토리지는 문자열 데이터 밖에 저장할 수 없기 때문에, 다른 타입의 데이터를 저장하려고 할 때 문자형으로 변환을 하기 때문이다.

~~~
> String(1)
"1"
> String({a: 1, b: 2})
"[object Object]"
~~~

## 해결 방법
웹 스토리지를 사용할 때 위와 같은 문제를 피하기 위해서 많이 사용하는 방법이 JSON 형태로 데이터를 읽고 쓰는 것이다.

~~~
> localStorage.setItem('json', JSON.stringify({a: 1, b: 2}))
undefined
> JSON.parse(localStorage.getItem('json'))
{a: 1, b: 2}
~~~

위와 같이 로컬 스토리지에 쓸 데이터를 JSON 형태로 직렬화(serialization)하고, 읽은 데이터를 JSON 형태로 역직렬화(deserialization)해주면 원본의 데이터를 그대로 얻을 수 있다.

배열형 데이터를 로컬 스토리지에 저장할 때도 동일한 방법으로 문제를 예방할 수 있다.

~~~
> localStorage.setItem('nums', JSON.stringify([1, 2, 3]))
undefined
> JSON.parse(localStorage.getItem('nums'))
[1, 2, 3]
~~~

## 데이터 청소
로컬 스토리지에 저장된 데이터는 웹페이지를 닫는다고 해서 사라지지 않으므로 불필요한 데이터가 남지 않도록 직접 청소해주는 것이 좋다.

~~~
> localStorage.length
5
> localStorage.key(0)
"email"
> localStorage.removeItem('obj')
undefined
> localStorage.length
4
> localStorage.clear()
undefined
> localStorage.length
0
~~~

참고로 브라우저의 개발자 도구를 통해서 웹 스토리지에 어떤 데이터가 저장되어 있는지를 쉽게 확인하고 삭제할 수 있다. 예를 들어, 크롬의 경우 Application 탭에 들어가면 왼편에 Storage 영역이 보일 것이다.

![image](https://user-images.githubusercontent.com/44339530/134492082-c1a1695c-84ff-4103-ae50-a5860a70f2d0.png)

> **Note**: 웹 스토리지로 데이터베이스 서버나 클라우드 서비스를 대체할 수는 없겠지만, 시제품을 프로토타이핑(prototyping)하거나 데이터가 중요하지 않은 개인 프로젝트에서는 유용하게 사용할 수 있을 것이다.

#### 출처
- [https://www.daleseo.com/js-web-storage/](https://www.daleseo.com/js-web-storage/)
- [https://dev-syhy.tistory.com/39](https://dev-syhy.tistory.com/39)