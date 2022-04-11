---
title: "React에서의 Virtual DOM" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-09-29 +0800
categories: [React] # categories는 최대 2개까지 가능
tags: [react, virtualdom] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# virutal dom 을 알아보기에 앞서 dom이란 무엇인가?
- `Document Object Model` 의 약자
- 객체로 문서 구조를 표현하는 방법(html 파일로부터 브라우저 화면을 표현하는 방법)
- 트리 형태로 구성되며 javascript로 특정 노드의 CRUD 작업이 가능

<b>DOM 자체는 빠르더라도, 요소의 개수가 수 백개, 수천 개로 늘어날 경우에는 당연히 느려질 수 밖에 없다.</b>

DOM의 변화가 변화가 일어나면 다음과 같은 과정들이 일어나게 된다.

- css 재연산: DOM트리의 모든 노드들이 attach 메소드 실행, 스타일 정보를 계산하여 객체 형태로 반환. 이 과정은 동기적이며 DOM트리에 새로운 노드가 추가되면 그 노드의 attach 메소드가 실행되게 된다.
- 레이아웃 재구성: 각 노드들이 어디에 나타나야할지 스크린 좌표 계산
- 페인트(paint): 렌더링된 요소에 색을 입히는 과정

> **Note**: 더 자세한 브라우저의 작동 방식이 궁금하다면 [여기](https://velog.io/@binheart/Virtual-DOM)를 참고하자.

위와 같은 과정들이 발생하다보니 당연스레 잦은 업데이트는 성능 저하가 당연하게 일어날 수 밖에 없다.

# virtual dom이란?
위와 같은 성능(속도) 이슈에 대한 해결책은 결국 `DOM을 최소한으로 조작`하는 방법 밖에 없다.

리액트에서는 이러한 문제점을 해결하기 위해 `virtual dom` 방식을 사용해 DOM의 업데이트를 추상화하여 처리 횟수를 최소한으로 한다.

<b>virtual dom은 실제 DOM을 조작하는게 아닌, 메모리에 DOM을 추상화한 자바스크립트 객체를 구상해 사용하는 것이다.</b> 쉽게 말하면, 메모리에 실제 DOM트리와 동일한 트리 구조의 javascript 객체를 가지고 있는 것이다.

# 그럼 어떻게 virtual dom으로 DOM 조작을 최소한으로 할까?
각 컴포넌트가 반환하는 엘리먼트를 이전에 반환했던 엘리먼트와 비교하고(`Reconcilation`), 다른 경우에만 해당하는 DOM 노드에 CRUD 작업을 수행한다.

![1_8OCCATi8_5HmWI1QpjrRNA](https://user-images.githubusercontent.com/44339530/162753053-c0b10645-ccc8-4d8b-ad25-e3b9dcaab165.png)

_출처: https://blog.bitsrc.io/incremental-vs-virtual-dom-eb7157e43dca_

위에서 설명했던 것처럼 기본적으로 `virtual dom`은 실제 DOM과 동일한 상태를 가지고 있으며, 표현하는 형식만 다르다.

1-1) 특정 컴포넌트에서 `setState` 호출 등의 이유로 컴포넌트 상태가 변하면 해당 컴포넌트의 `shouldComponentUpdate` 함수를 실행한다. 그리고 이 함수가 `true`를 반환하면 `render` 함수를 실행한다.

> **Note**: `shouleComponentUpdate` 함수가 `false` 를 반환하면 이 컴포넌트의 `render` 함수를 실행하지 않고, 자식 컴포넌트의 `shouldComponentUpdate, render` 함수도 실행하지 않는다.

1-2) 상태가 변한 컴포넌트를 루트 노드로 해서 깊이 우선 탐색 방식(DFS)으로 각 자식 컴포넌트의 `shouldComponentUpdate` 함수와 `render` 함수를 실행한다.

2) 이렇게 `render` 함수를 실행하여 얻은 새로운 `virtual dom`을 실제 dom과 동일한 구조를 가진 이전 `virtual dom`과 비교하여 변경된 부분을 찾는다.([reconcilation: 재조정](https://ko.reactjs.org/docs/reconciliation.html))

3) 실제 변경된 부분만 실제 DOM에 `DOM API`를 호출하여 반영한다. 브라우저가 변경 사항이 반영된 DOM과 CSSOM으로 새로운 Render Tree를 생성해서 화면을 다시 그린다.

> **Note**: 참고로 리액트는 상태가 변경되었을때, 그 상태에 따라 dom을 업데이트하는 규칙을 정하는게 아니라 아예 다 날려버리고 처음부터 모든걸 새로 그린다는 철학을 가지고 있다. 리액트의 탄생 배경에 대하여 더 알고싶다면 [여기](https://jeonyoungho.github.io/posts/01-%EB%A6%AC%EC%95%A1%ED%8A%B8%EB%8A%94-%EC%96%B4%EC%A9%8C%EB%8B%A4-%EB%A7%8C%EB%93%A4%EC%96%B4%EC%A1%8C%EC%9D%84%EA%B9%8C/)를 참고하자.

# 그렇다면 정말 virtual dom은 빠를까?
결론부터 말하자면 `아니오` 라 할 수 있다.
리액트 공식 레퍼런스에는 다음과 같은 문장이 있다.

<i>지속적으로 데이터가 변화하는 대규모 애플리케이션 구축하기.</i>

즉, 간단한 작업(예, 단순한 라우팅 작업만 있을 경우) 일 경우에는 리액트를 사용하지 않는 편이 더 효과적일때가 있다는 말이다. 또한 코드 최적화를 열심히 한다면 속도 문제를 개선할 수 있을 것이다.

리액트가 보장하는 성능은 바로 `업데이트 처리 간결성`이다. 무조건적으로 리액트를 사용하는 것이 아닌 항상 `기술적 타당성`을 검토하며 개발하는 자세가 필요하다.



#### 출처
- [https://brunch.co.kr/@eight-two-five/14](https://brunch.co.kr/@eight-two-five/14)
- [https://velog.io/@gwak2837/React-Virtual-DOM-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0](https://velog.io/@gwak2837/React-Virtual-DOM-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0)
- [https://ko.reactjs.org/docs/faq-internals.html](https://ko.reactjs.org/docs/faq-internals.html)
- [https://dev-cini.tistory.com/10](https://dev-cini.tistory.com/10)
- [https://dev-cini.tistory.com/11](https://dev-cini.tistory.com/11)
- [https://hello-bryan.tistory.com/326](https://hello-bryan.tistory.com/326)