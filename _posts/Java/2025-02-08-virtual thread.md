---
title: "[Java] virtual thread"
date: 2025-02-08 +0800
categories: [Java]
tags: [java]
toc: true
comments: true
---

> [‘Java의 미래, Virtual Thread’ 우아한 테크 세미나](https://www.youtube.com/watch?v=BZMZIM-n4C0) 학습 내용을 정리하기 위한 포스팅입니다. 자세한 내용은 영상을 통해 참고해주세요😀

# 1. 일반 Thread 모델
일반 스레드 모델은 다음과 같은 특징을 가진다.

- 플랫폼 스레드
- OS에 의해 스케줄링
- 커널 스레드와 1대1 매핑
- 작업 단위 Runnable

![Image](https://github.com/user-attachments/assets/a94d62e2-5963-48e8-b52c-3bcbf0497e85)

애플리케이션은 커널 영역(OS), 유저 영역(JVM) 두 가지 영역을 통해 실행된다.

커널 스레드와 플랫폼 스레드는 1대1로 매핑되며 이 두 사이를 통신하는 과정(유저 영역에서 커널 영역의 기능을 사용)은 JNI(Java Native Interface)를 통한 시스템콜(system call)을 통해 이뤄진다. 

플랫폼 스레드를 생성하는 과정은 다음과 같다.

**1. 유저 영역(JVM)에 플랫폼 스레드 객체가 생성된다.**

![Image](https://github.com/user-attachments/assets/157143b7-c4a5-4357-a6af-64edb2f79553)

**2. 스레드 실행(Thread.start 메서드)시 JNI를 통해 커널 영역에 커널 스레드 생성을 요청한다.**

![Image](https://github.com/user-attachments/assets/3ec86494-5093-4e01-b7dc-1d07a4e47a7e)

생성된 커널 스레드는 OS의 스케줄러에 의해 스케줄링되는 방식으로 동작한다.

이를 실제 자바 코드로 살펴보면 다음과 같다.

![Image](https://github.com/user-attachments/assets/48260f0f-73c6-451f-b6a0-0befb346790a)

Thread.start 메서드를 호출하면 내부 start0 메서드를 통해 커널 스레드 생성 요청을 JNI를 통해 실행한다.

추가적으로 톰캣과 같은 웹서버가 스레드풀을 사용하는 이유는 JVM 플랫폼 스레드를 생성할때 JNI 를 통해 시스템콜 하는 과정에서 오버헤드가 발생하기에 미리 생성해놓고 재사용하는 방식을 적용한것이다.

# 2. Virtual Thread 등장 배경(일반 Thread 모델의 문제점)

## 2-1. 일반 Thread 모델의 한계
기본적인 요청 처리방식은 Thread per request(하나의 요청당 하나의 스레드로 처리하는 방식)이다. 요청 처리량을 높이려면 스레드를 늘려야하나 OS 커널단의 스레드 제약으로 무한히 늘릴 수 없다.

## 1-2. Blocking I/O로 인한 대기 시간 증가
Thread에서 I/O작업을 처리할때 Blocking이 발생하여 대기 시간이 증가하게 된다.

## 1-3. Reactive Programming 의 한계
Thread 대기 시간을 줄이기 위해 Webflux 같은 개념이 등장했지만 코드를 작성하고 이해하기 어렵다.

# 2. Virtual Thread 모델
Virtual Thread는 JDK21에 추가된 경량 스레드 모델이다. OS 커널단의 스레드를 사용하지 않고 JVM 내부 스케줄링을 통해 스레드를 생성하는 방식으로 수십만 ~ 수백만개의 스레드를 동시에 사용할 수 있다.

Virtual Thread의 특징은 다음과 같다.

## 가상 스레드
JVM 내부에 생성되는 가상 스레드이다.  

## JVM 내부 스케줄링

<img width="481" alt="Image" src="https://github.com/user-attachments/assets/2e841d42-18ee-4c8a-acc9-a6f412f8eaae" />

VirtualThread 위의 주석을 살펴보면 OS가 아닌 JVM에 의해 스케줄링되는 스레드라는 내용을 확인할 수 있다.

JVM 내부 스케줄링은 다음과 같이 동작한다.

**1. Virtual Thread 생성시 유저 영역에 생성된다.**

![Image](https://github.com/user-attachments/assets/51a99782-9c9c-4fd1-8432-d30008e0b764)

**2. Virtual Thread 실행시 JVM 내부 스케줄러인 ForkJoinPool 에 의해 실행된다.**

![Image](https://github.com/user-attachments/assets/bae283d9-865d-48ba-9055-889a7d264a77)

이를 실제 자바 코드로 살펴보면 다음과 같다.

![Image](https://github.com/user-attachments/assets/6b092ad0-b1f3-417c-bfdc-fb5f82ca7340)

![Image](https://github.com/user-attachments/assets/2b10a65e-7d00-4fc6-8087-9e65c32e9474)

VirtualThread의 start 함수를 실행하면 내부 submitRunContinuation 메서드를 실행하여 JVM 내 가상스레드 스케줄러에 runConfiguration을 등록한다.

![Image](https://github.com/user-attachments/assets/aacc1a26-7b27-4287-85d2-4b7afe734731)

![Image](https://github.com/user-attachments/assets/64b60c31-e72b-4b53-9507-4157a8c58bf4)

![Image](https://github.com/user-attachments/assets/f6cc0206-c269-4e0a-acf7-bcf0aef457f5)

VirtualThread의 scheduler는 Executor 타입이다. 디폴트 스케줄러는 FokrJoinPool 타입이며 ForkJoinPool 메커니즘으로 스케줄링된다. 또한, static 으로 선언되어 있기에 모든 VirtualThread가 동일한 스케줄러를 공유한다는 것을 알 수 있다. 

**3. ForkJoinPool은 Carrier Thread 를 OS단 물리 CPU 코어수만큼 생성한다.**

![Image](https://github.com/user-attachments/assets/5e21d828-b19d-4da2-83e8-04f0ba63cd40)

이를 코드로 확인하면 다음과 같다.

![Image](https://github.com/user-attachments/assets/4c83b785-8fd7-4c38-9587-0e531f82446f)

디폴트 스케줄러(ForkJoinPool)의 생성 메서드를 확인해보면 Carrier Thread(워커 스레드, 일반 스레드)를 이용 가능한 OS단 프로세서 수만큼 생성해놓는 것을 확인할 수 있다.

**4. ForkJoinPool은 Work Stealing 방식으로 동작하며 작업을 처리한다.**

![Image](https://github.com/user-attachments/assets/17df49e5-3035-4032-a80e-73311173d7f3)

Work Stealing 메커니즘이란 Worker 스레드 각각 Work 큐를 가지고 있고 Work 큐에 태스크를 담아 순차적으로 처리하는 방식이다. 다만, Worker 스레드는 자신의 Work 큐에 작업이 비어있으면 다른 Work 큐로부터 작업을 훔쳐와서 처리한다.

### JVM 내부적인 스케줄링을 해야 하는 이유
- 일반 스레드는 생성과 스케줄링시 커널 영역과 시스템콜을 통해 계속 통신해야되고 이 과정에서 오버헤드가 발생하게 된다.
- Virtual 스레드는 커널 영역 접근 없이 단순 Java 객체를 생성하므로 시스템콜이 발생하지 않기에 시스템콜로 인한 오버헤드가 발생하지 않게 된다.

## 캐리어 스레드와 1대N 매핑
하나의 Carrier Thread 의 작업큐에 여러 태스크들을 넣고 순차적으로 처리할 수 있다.

## 작업 단위 Continuation
오래전부터 사용되던 프로그래밍 패러다임이다.

![Image](https://github.com/user-attachments/assets/8bd7add1-5e2b-4d5b-ba2f-0c52bcc23ee5)

코틀린의 코루틴도 Continuation을 사용하여 동작한다. 일반적인 Function 은 왼쪽 이미지처럼 Caller가 Function을 실행하면 한 번에 실행 완료후 리턴하게 된다. 서스펜드 Function은 중단이 가능하다. 오른쪽 이미지처럼 코틀린 컴파일러가 코루틴이라는 개념을 적용하여 Caller가 호출하게 되면 어느 정도 실행되다 중단후 다시 실행하는 메커니즘이 가능해진다.

Continuation은 다음과 같은 특징을 가진다.
- 실행가능한 작업흐름
- 중단가능
- 중단 지점부터 재실행 가능

![Image](https://github.com/user-attachments/assets/e6092da1-cfbc-4a2b-a331-dbc09bf051a2)

Continuation의 작업 단위는 다음과 같이 처리된다. Cont1 이 실행되다 중단(yield)되면 중단 지점이 메모리에 기록되어 힙으로 이동하게 되고, 스택엔 그 다음 실행되는 Cont2가 올라와서 실행되게 된다.

실제 자바 코드를 확인해보면

![Image](https://github.com/user-attachments/assets/fb554e57-8cca-453c-a0b5-0959ce8cbfe0)

![Image](https://github.com/user-attachments/assets/996b1445-80fc-4ac5-bd74-434d06bff902)

![Image](https://github.com/user-attachments/assets/005983ac-e51c-401c-9a04-995150c04948)

runContinuation은 Continuation 실행 람다이다. VirtualThread를 start 할때 실행되는 submitRunContinuation 메서드는 JVM 스케줄러에 실행 작업을 등록하는 것이다.

![Image](https://github.com/user-attachments/assets/86fc47c8-d1cd-45b9-b7ad-952783b60e03)

즉, 위 이미지와 같이 ForkJoinPool에 실행 작업을 등록하게 되는 것이다.

![Image](https://github.com/user-attachments/assets/d0a2285a-5239-41b3-b83e-b9a9b3ed541a)

만약 Cont1이 yield 되어 작업이 중단되면 힙메모리로 넘어가게 되고 work 큐에서 제거된다.

![Image](https://github.com/user-attachments/assets/e7dc8f4c-ffbd-4f64-8fe9-6450ef062721)

그러면 Cont2가 이어서 작업을 실행하게 된다.

VirtualThread는 처리되던 runContinuation들이 I/O, Sleep으로 인한 interrupt나 작업 완료시 yield 된다.

자바 코드단에서는 VirtualThread 클래스의 park 메서드가 실행될때 yield 된다.

![Image](https://github.com/user-attachments/assets/02207d50-39fb-4ba6-9e8e-a4b6d6210bbf)

패키지-프라이빗으로 되어 있는데 LockSupport.park 메서드를 통해 실행시킬수 있으며 스레드가 블락킹된다.

![Image](https://github.com/user-attachments/assets/2fec74a8-415c-4dbd-9801-50fa29a3cca7)

### Continuation 사용 이유
- 일반 Thread 모델은 작업 중단을 위해 커널 스레드를 중단시킨다.
- Virtual Thread는 작업 중단을 위해 Continuation yield 시킨다. 작업이 block 되어도 실제 스레드는 중단되지 않고 다른 작업을 처리한다.
- **커널 스레드의 중단이 없으므로 시스템 콜이 발생하지 않게 되며 컨텍스트 스위칭 비용이 낮아지게 된다.**

# 3. 성능 테스트

<img width="684" alt="Image" src="https://github.com/user-attachments/assets/4564b056-c972-4801-b6f0-9c108ca5ba01" />

일반 Thread 방식에 비해
- I/O Bound 작업은 50% 향상된 처리량 -> NIO로 동작하기에
- CPU Bound 작업은 7% 낮된 처리량 -> CPU 연산 처리는 플랫폼 스레드 위에서 동작해야 하는데 Virtual Thread를 생성하고 스케줄링하는 비용이 낭비되기 때문에

자세한 내용은 [여기](https://techblog.woowahan.com/15398)를 참고하면 좋다.

# 4. Virtual Thread 주의사항

## 1. Carrier Thread 블로킹 현상(pin)
Carrier Thread가 block 되면 Virtual Thread를 활용 불가하다. Continuation가 yield 되고 작업큐에서 빠져나오고 다시 스케줄링되는 과정들이 불가능해진다.

대표적으로 두 케이스에 대해 Virtual Thread는 Carrier Thread로부터 분리되지 않고 고정(pin)되어 위 현상이 발생하게 된다.
- synchronized
- parrallelStream (병렬 스트림)

Virtual Thread의 synchronized, parrallelStream 을 사용하는 부분을 ReentrantLock으로 바꾸면 위 현상을 개선할 수 있다. 스프링이나 몽고DB 는 지원을 하도록 새로운 버전에 적용되어 있지만, MySQL 은 synchronized가 많이 사용되고 있어서 pin 이슈가 많이 발생한다고 한다.

위 현상은 `-Djdk.tracePinnedThreads=short, full` VM Option 으로 감지 가능하다.

## 2. No Pooling
Virtual Thread 생성비용이 저렴하기에 풀방식을 사용하지않고 사용할때마다 생성하고 GC처리하면 된다.

오히려 풀방식을 사용하면 병목이 발생할 수 있다.

## 3. CPU bound task
결국 Carrier Thread 위에서 CPU 연산이 수행되므로 성능을 효율적으로 사용하지 못하게 되며 nonblocking의 장점을 활용하지 못하게 된다.

## 4. 경량 스레드
ThreadLocal에 무거운 객체를 넣게 되면 Virtual Thread의 이점을 살릴수 없게 된다. 매번 생성하고 매번 파괴하기에 Memory를 사용하는 부분이 계속 늘어나게 된다. 

JDK21의 preview 피처로 ScopedValue 라는 ThreadLocal을 대체하는 개념이 나왔다.

## 5. 배압
배압 조절 기능이 없다. 

Virtual Thread 는 무제한으로 생성하여 무제한으로 처리하기에 서버가 가질수 있는 최대치를 내려고 할것이다. 그 과정에서 하드웨어 성능이 부족할 수 있기에 충분한 하드웨어 성능테스트가 필요하다.

그리고 유한 리소스(DB 커넥션 등)의 경우 배압을 조절하도록 설정이 필요하다. DB 커넥션이 부족해서 문제 되는 경우가 존재할 수 있다.

# 결론

![Image](https://github.com/user-attachments/assets/0167b493-09af-4744-ac49-875331d64a5e)

# Reference
- [https://techblog.woowahan.com/15398/](https://techblog.woowahan.com/15398/)