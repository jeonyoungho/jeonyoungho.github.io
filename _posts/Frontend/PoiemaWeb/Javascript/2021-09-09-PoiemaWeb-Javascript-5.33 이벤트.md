---
title: "[PoiemaWeb] 5.33 이벤트"
date: 2021-09-09 +0800
categories: [Frontend, PoiemaWeb, Javascript]
tags: [poiemaweb, javascript, es6]
toc: true
comments: true
---

# [2. 이벤트 루프(Event Loop)와 동시성(Concurrency)](https://poiemaweb.com/js-event#2-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%A3%A8%ED%94%84event-loop%EC%99%80-%EB%8F%99%EC%8B%9C%EC%84%B1concurrency)

브라우저는 단일 `쓰레드`(single-thread)에서 이벤트 드리븐(event-driven) 방식으로 동작한다.

단일 쓰레드는 쓰레드가 하나뿐이라는 의미이며 이말은 곧 하나의 작업(task)만을 처리할 수 있다는 것을 의미한다. 하지만 실제로 동작하는 웹 애플리케이셩ㄴ은 많은 task가 동시에 처리되는 것처럼 느껴진다. 이철머 자바스크립트의 동시성(Concurrency)을 지원하는 것이 바로 `이벤트 루프(Event Loop)`이다.

브라우저의 환경을 그림으로 표현하면 아래와 같다.

<img width="494" alt="스크린샷 2021-09-10 오후 7 53 31" src="https://user-images.githubusercontent.com/44339530/132842981-f9ebffb1-53ae-4077-b974-1e74f40e5201.png">

구글의 V8을 비롯한 대부분의 자바스크립트 엔진은 크게 2개의 영역으로 나뉜다.

#### Call Stack(호출 스택)
> **Note**: 작업이 요청되면(함수가 호출되면) 요청된 작업은 순차적으로 Call Stack에 쌓이게 되고 순차적으로 실행된다. 자바스크립트는 단 하나의 Call Stack을 사용하기 때문에 해당 task가 종료하기 전까지는 다른 어떤 task도 수행될 수 없다.

#### Heap
> **Note**: 동적으로 생성된 객체 인스턴스가 할당되는 영역이다. 

이와 같이 자바스크립트 엔진은 단순히 작업이 요청되면 Call Stack을 사용하여 요청된 작업을 순차적으로 실행할 뿐이다. 앞에서 언급한 동시성(Concurrency)을 지원하기 위해 필요한 비동기 요청(이벤트를 포함) 처리는 자바스크립트 엔진을 구동하는 환경 즉 브라우저(또는 Node.js)가 담당한다.

#### Event Queue(Task Queue)
> **Note**: 비동기 처리 함수의 콜백 함수, 비동기식 이벤트 핸들러, Timer 함수(setTimeout(), setInterval())의 콜백 함수가 보관되는 영역으로 이벤트 루프(Event Loop)에 의해 특정 시점(Call Stack이 비어졌을 때)에 순차적으로 Call Stack으로 이동되어 실행된다.

#### Event Loop(이벤트 루프)
> **Note**: Call Stack 내에서 현재 실행중인 task가 있는지 그리고 Event Queue에 task가 있는지 반복하여 확인한다. 만약 Call Stack이 비어있다면 Event Queue 내의 task가 Call Stack으로 이동하고 실행된다.

아래의 예제가 어떻게 동작할지 살펴보자.

~~~
function func1() {
  console.log('func1');
  func2();
}

function func2() {
  setTimeout(function () {
    console.log('func2');
  }, 0);

  func3();
}

function func3() {
  console.log('func3');
}

func1();
~~~

함수 func1이 호출되면 함수 func1은 Call Stack에 쌓인다. 그리고 함수 func1은 함수 func2을 호출하므로 함수 func2가 Call Stack에 쌓이고 setTimeout가 호출된다. <b>setTimeout의 콜백함수는 즉시 실행되지 않고 지정 대기 시간만큼 기다리다가 “tick” 이벤트가 발생하면 태스크 큐로 이동한 후 Call Stack이 비어졌을 때 Call Stack으로 이동되어 실행된다.</b>


<img width="494" alt="이벤트 루프(Event Loop)에 의한 setTimeout 콜백함수의 실행" src="https://poiemaweb.com/img/event-loop.gif">

DOM 이벤트 핸들러도 이와 같이 동작한다.

~~~
function func1() {
  console.log('func1');
  func2();
}

function func2() {
  // <button class="foo">foo</button>
  const elem = document.querySelector('.foo');

  elem.addEventListener('click', function () {
    this.style.backgroundColor = 'indigo';
    console.log('func2');
  });

  func3();
}

function func3() {
  console.log('func3');
}

func1();
~~~

함수 func1이 호출되면 함수 func1은 Call Stack에 쌓인다. 그리고 함수 func1은 함수 func2을 호출하므로 함수 func2가 Call Stack에 쌓이고 addEventListener가 호출된다. <b>addEventListener의 콜백함수는 foo 버튼이 클릭되어 click 이벤트가 발생하면 태스크 큐로 이동한 후 Call Stack이 비어졌을 때 Call Stack으로 이동되어 실행된다.</b>

# [6. 이벤트의 흐름](https://poiemaweb.com/js-event#6-%EC%9D%B4%EB%B2%A4%ED%8A%B8%EC%9D%98-%ED%9D%90%EB%A6%84)
계층적 구조에 포함되어 있는 HTML 요소에 이벤트가 발생할 경우 연쇄적 반응이 일어난다. 즉, 이벤트가 전파(Event Propagation)되는데 전파 방향에 따라 버블링(Event Bubbling)과 캡처링(Event Capturing)으로 구분할 수 있다.

자식 요소에서 발생한 이벤트가 부모 요소로 전파되는 것을 버블링이라 하고, 자식 요소에서 발생한 이벤트가 부모 요소부터 시작하여 이벤트를 발생시킨 자식 요소까지 도달하는 것을 캡처링이라 한다. <b>주의할 것은 버블링과 캡처링은 둘 중에 하나만 발생하는 것이 아니라 캡처링부터 시작하여 버블링으로 종료한다는 것이다.</b> 즉, 이벤트가 발생했을 때 캡처링과 버블링은 순차적으로 발생한다.

캡처링은 IE8 이하에서 지원되지 않는다.

<img width="480" alt="스크린샷 2021-09-10 오후 7 59 42" src="https://user-images.githubusercontent.com/44339530/132843644-7c0d0712-95f1-4192-9bb2-935595984717.png">

<b>addEventListener 메소드의 세번째 매개변수에 true를 설정하면 캡처링으로 전파되는 이벤트를 캐치하고 false 또는 미설정하면 버블링으로 전파되는 이벤트를 캐치한다.</b>

관련 예제는 [PoiemaWeb 예제](https://poiemaweb.com/js-event#6-%EC%9D%B4%EB%B2%A4%ED%8A%B8%EC%9D%98-%ED%9D%90%EB%A6%84)를 참고하자.

#### 출처
- [https://poiemaweb.com/js-event](https://poiemaweb.com/js-event)