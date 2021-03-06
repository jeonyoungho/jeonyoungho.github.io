---
title: "[Backend] 동기&비동기&block&non-block" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-06-30 +0800
categories: [Backend] # categories는 최대 2개까지 가능
tags: [synchronous, asynchronous, block, non-block] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

## 동기 & 비동기(데이터를 받는 방식)

### 동기(synchronous : 동시에 일어나는)
- 말 그대로 동시에 일어난다는 뜻
- 요청과 그 결과가 동시에 일어난다는 약속
- 바로 요청을 하면 시간이 얼마가 걸리던지 요청한 자리에서 결과가 주어져야 함
- 시간을 맞추는 것, 즉 요청을 하면 응답 데이터가 올때가지 기다리며 시간을 맞춤
    - 요청과 결과가 한 자리에서 동시에 일어남
    - A노드와 B노드 사이의 작업 처리 단위(transaction)를 동시에 맞추겠다는 뜻

### 비동기(Asynchronous : 동시에 일어나지 않는)
- 동시에 일어나지 않는다를 의미
- 요청과 결과가 동시에 일어나지 않을거라는 약속
- 시간을 안 맞추는 것, 즉 요청을 해도 응답 데이터가 올때까지 기다리지 않고 시간을 안맞춤
    - 요청한 그 자리에서 결과가 주어지지 않음
    - 노드 사이의 작업 처리 단위를 동시에 맞추지 않아도 된다.

### 동기와 비동기는 상황에 따라서 각각의 장단점이 존재함
- 동기 방식은 설계가 매우 간단하고 직관적이지만 결과가 주어질 때까지 아무것도 못하고 대기해야 하는 단점이 있음
- 비동기 방식은 동기보다 복잡하지만 결과가 주어지는데 시간이 걸리더라도 그 시간 동안 다른 작업을 할 수 있으므로 자원을 효율적으로 사용할 수 있는 장점이 있음

### 동기방식의 예(A가 B에게 10,000원을 송금)
<img width="589" alt="스크린샷 2021-03-18 오후 5 50 59" src="https://user-images.githubusercontent.com/44339530/111598368-8067b080-8812-11eb-8b0a-c803883c0429.png"><br>

- A의 계좌와 B의 계좌는 서로 요청과 응답을 확인한 후 같은 일을 동시에 진행하였음
- 이처럼 '계좌이체'같은 작업은 동기방식으로 처리해야 A에서 보냈는데 B에서 못 받는 상황이 없음

### 비동기방식의 예(시험날의 학생과 선생)
<img width="584" alt="스크린샷 2021-03-18 오후 5 55 00" src="https://user-images.githubusercontent.com/44339530/111598924-100d5f00-8813-11eb-9708-c16b45c0cdfa.png"><br>

- 학생과 선생은 시험지라는 연결고리가 있지만 시험지에 행하는 행위(목적)은 서로 다름
- 학생은 시험지를 푸는 역할을 하고 선생은 시험지를 채점하는 역할을 함
- 서로의 행위(목적)가 다르기 때문에 둘의 작업 처리 시간은 일치하지 않아도 됨
- 만약 학생이 시험지를 제출하고 아무것도 안하고 기다린다면 Blocking상태이다.
- 만약 학생이 시험지를 제출하고 다른 과목을 공부한다거나 게임을 하는 등 다른 작업을 수행한다면 Non-Blocking상태이다.

### 어떤 상황에 동기 방식을 사용하고 어떤 상황에 비동기 방식을 사용해야 하는가?
- 동기와 비동기는 어떤 작업 혹은 그와 연관된 작업을 처리하고자 하는 시각의 차이
- 동기는 추구하는 같은 행위(목적)가 동시에 이루어지며, 비동기는 추구하는 행위(목적)가 다를 수도 있고, 동시에 이루어지지도 않음

## blocking & non-blocking

### blocking
- 요청한 작업을 마칠 때까지 계속 대기
- 즉시 return하여 return값을 받아야만 끝남
- Thread 관점으로 본다면, 요청한 작업을 마칠 때까지 계속 대기하며 return 값을 받을 때까지 한 Thread를 계속 사용 및 대기함

### non-blocking
- 요청한 작업을 즉시 마칠 수 없다면 즉시 return 후 다른 작업을 수행함
- Thread 관점으로 본다면, 하나의 Thread가 여러 개의 IO를 처리 가능함

## 동기/비동기, blocking/non-blocking의 차이
- 동기/ 비동기, blocking/non-blocking의 차이는 관심사가 다르다는 것임

### 동기/비동기
- <b>호출되는 함수의 작업 완료 여부를 누가 신경쓰느냐</b>가 관심사
- 비동기(호출된 함수가 신경씀)
    - 호출되는 함수에게 callback을 전달해서 호출되는 함수의 작업이 완료되면 호출되는 함수가 전달받은 callback을 실행하고, <b>호출한 함수는 작업 완료 여부를 신경쓰지 않는다.</b>
- 동기(호출한 함수가 신경씀)
    - 호출되는 함수로부터 바로 return 받더라도 <b>작업 완료 여부를 호출한 함수 스스로 확인</b>하며 신경 쓴다.

### blocking/non-blocking
- <b>호출된 함수가 바로 return을 하느냐 마느냐</b>가 관심사이다.
- blocking(바로 리턴 X)
    - 호출된 함수가 <b>바로 return하지 않고 </b> 자신의 작업을 수행하면서 호출한 함수에게 제어권을 넘겨주지 않고 대기하게 만든다.

- non-blocking(바로 리턴 O)
    - 호출된 함수가 <b>바로 return해서</b> 호출한 함수에게 제어권을 넘겨주고 호출한 함수가 다른 일을 할 수 있는 기회를 준다.

## 동기/비동기, blocking/non-blocking 조합
<img width="787" alt="스크린샷 2021-03-24 오전 11 10 10" src="https://user-images.githubusercontent.com/44339530/112243779-81289880-8c91-11eb-8853-02be707628fa.png"><br>

### blocking + Synchronous
- 호출되는 함수는 바로 return하지 않고, 호출한 함수는 작업 완료 여부를 직접 신경씀
- 결과가 처리되어 나올때까지 기다렸다가 return 값으로 결과를 전달한다.

### non-blocking + Asynchronous
- 호출되는 함수는 바로 return하고, 호출한 함수는 작업 완료 여부를 신경 쓰지 않음
- 호출한 함수는 callback메소드와 함께 다른 함수를 호출하고 return될 때까지 다른 작업을 수행한다.
- 호출된 함수는 별도의 프로세스로 작업을 처리하고 난 뒤 직접 callback메소드를 처리한다.

<img width="798" alt="스크린샷 2021-03-24 오전 11 15 14" src="https://user-images.githubusercontent.com/44339530/112244170-35c2ba00-8c92-11eb-9d44-28c94d24a258.png"><br>

### blocking + Asynchronous
- 호출되는 함수가 바로 return하지 않고, 호출하는 함수는 작업 완료 여부를 신경쓰지 않음(이 조합은 사실 이점이 없어서 일부러 이 방식을 사용하진 않는다고 함)

### non-blocking + Synchronous
- 호출된 함수가 바로 return하고, 호출하는 함수는 작업 완료 여부를 직접 신경 씀
- 호출한 함수가 결과가 생길때까지 계속 완료 되었는지 확인함

#### 출처
- [https://private.tistory.com/24](https://private.tistory.com/24)
- [https://www.youtube.com/watch?v=IdpkfygWIMk](https://www.youtube.com/watch?v=IdpkfygWIMk)
- [https://siyoon210.tistory.com/147](https://siyoon210.tistory.com/147)