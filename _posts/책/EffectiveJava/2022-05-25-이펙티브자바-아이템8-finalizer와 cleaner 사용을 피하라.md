---
title: "[이펙티브자바] 아이템8-finalizer와 cleaner 사용을 피하라"
date: 2022-05-25 +0800
categories: [Java, EffectiveJava]
tags: [java, effectivejava]
toc: true
comments: true
---

이펙티브 자바 아이템8 finalizer에 관련되어 [이해하기 쉽게 잘 정리된 포스팅](https://brunch.co.kr/@oemilk/122)이 있어 참고하여 정리해보려 한다.

java에서의 모든 객체는 Object 객체를 상속받는다. 그리고 Object에는 `finalize()` 라는 메서드가 존재한다.

`finalize()` 메서드는 클래스의 객체가 더 이상 참조되고 있지 않을 경우 GC가 메모리에 정리하기 전에 자동으로 호출시킨다.

그리고 현재 자바9부터 deprecated로 지정된 것을 확인하실 수 있다.

![image](https://user-images.githubusercontent.com/44339530/170152296-360a0d2b-9a5b-413a-926d-57d42305119a.png)

자바9에서 depreacted 로 지정된 이유는 몇 가지 문제점들이 있기 떄문이다.

# finalize의 문제점

## 문제점1) 신속하게 실행된다는 보장이 없다.

![image](https://user-images.githubusercontent.com/44339530/170152500-418ed6a4-7318-4369-af90-932ad6c73ea1.png)

- 객체를 이용할 수 없게 되는 시점부터 Finalizer가 실행되는 시점까지는 긴 시간이 소요될 수 있으며, 예측할 수 없다.
- 이 Finalizer가 얼마나 빨리 실행되는지는 주로 GC 알고리즘에 의존하며, 이 알고리즘은 JVM 종류에 따라 다양하다.
- 그리고 어떤 스레드가 Finalier를 실행할지 자바 언어 명세에서는 보장되어 있지 않으므로, Finalizer 스레드가 실행되지 않아 다른 문제가 발생 할 수 도 있다.

## 문제점2) 반드시 실행된다는 보장이 없다.

![image](https://user-images.githubusercontent.com/44339530/170152688-d17fe3f5-4908-4668-b959-17a1cfadc7b2.png)

- 객체가 더 이상 참조되고 있지 않을 경우 언제 실행한다는 보장이 없기에 치명적인 문제를 범할 수 있다.
  - <b>만약 상태를 영구적으로 수정하는 작업에서는 절대 finaler나 cleaner에 의존해선 안된다.
  - 예를 들어, DB 같은 공유 자원의 영구 락(lock) 해제를 finaler나 cleaner에 맡겨 놓으면 분산 시스템 전체가 서서히 멈출 것이다.</b>
- `System.gc`나 `System.runFinalization` 메서드를 호출하더라도 <b>Finalizer가 실행될 가능성을 높여 줄 뿐 반드시 실행됨을 보장하진 않는다.</b>
  - `Finalizer`를 보장하는 `System.runFinalizersOnExit(boolean) 함수`와 `Runtime.runFinalizersOnExit(boolean) 함수`가 있지만 치명적인 결함을 갖고 있기에 현재는 금지되었다.

## 문제점3) 예외가 발생하면 무시된다.
- <b>Finalize 하는 동안 catch 되지 않는 예외가 발생하면 그 예외는 무시되고 Finalize가 끝나게 된다.</b>
- 일반적으로 catch 되지 않는 예외가 발생하면, 실행이 중단되고 stack trace가 출력된다.
- 하지만 Finalize 내에서 발생하면 에외가 무시되므로 불안정한 상태에서 예측할 수 없는 결과가 발생할 수 있다.

## 문제점4) 성능 저하가 발생한다.
- 클래스의 객체를 생성하고 소멸시키는 시간이 Finalizer를 이용하면 많게는 수백 배 정도 차이가 난다.
- 따라서 가급적으로 Finalizer를 통해 객체를 소멸시키지 않고 다른 방법을 써야 한다.
- 이는 finalizer가 GC의 효율을 떨어뜨리기 때문이다. `cleaner`도 클래스의 모든 인스턴스를 수거하는 형태로 사용하면 성능은 finalizer와 비슷하다.

## 문제점5) 심각한 보안 문제를 일으킬 수 있다.
- finalizer 공격 원리는 간닪다ㅏ. 생성자나 직렬화 과정(readObject와 readResolve 메서드)에서 예외가 발생하면, 이 생성되다만 객체에서 하위 클래스의 finalizer가 수행될 수 있게 된다. 이는 있어서는 안 될 일이다.

# finalize가 적합할 때

## 적합한 case 1) 안정망 역할
- 객체의 종료 메서드를 호출을 빠뜨렸을 경우에 `안정망` 역할을 하는 경우이다.
- <b>즉, 클라이언트가 종료 메서드 호출에 실패하는 경우를 대비해서 이용할 수 있다.</b>
- 물론 Finalizer가 늦게 호출되거나 심지어 호출이 안될 수 있찌만, 이렇게 라도 종료 메서드를 호출하는게 더 낫기 때문이다.

![image](https://user-images.githubusercontent.com/44339530/170154259-7d7bb890-cbaa-4003-aada-d5c8e16c69f4.png)

- 출처 블로그의 예시가 아주 이해하기 쉽고 적합한 것 같다.
- Timer클래스를 보면 finalize() 메섣르롱해 안정망 역할을 구현하였다.
- 만약 자원이 정상적으로 해제되지 않을 경우를 대비해 `finalize()` 메서드를 통해 `TaskQueue`와 `TimerThread` 의 뒷정리 작업을 수행하는 것을 확인할 수 있다.

## 적합한 case 2) Native peer 객체
- Native란 자바 외의 C나 C++ 같은 프로그래밍 언어를 의미하며, Native API를 이용하여 이코드와 연관된 자바 객체(Native peer 객체)를 만든다.
- Native peer 객체는 일반 자바 객체가 아니므로, GC가 되지 않을 수 있다.
- 이런 경우엔 Finalizer를 이용할 수 있다.

# finalize() 이용시 주의 할 점

## 주의할 점1) Finalizer의 chaining 은 자동으로 실행되지 않는다.

> chaining이란 상속 관계에 있는 클래스에서 서브 클래스의 메서드 호출 시, 이와 연관된 슈퍼 클래스의 메서드가 자동으로 호출되는 것을 의미한다.

```java
class ObjA {
	private int id;

	public ObjA(int id) {
		this.id = id;
		System.out.println("Call ObjA Constructor");
	}

	public void finalize() {
		System.out.println("Call ObjA Destructor");
	}
}

class ObjB extends ObjA {
	private String name;

	public ObjB(int id, String name) {
		super(id);
		this.name = name;
		System.out.println("Call ObjB Constructor");
	}

	public void finalize() {
		System.out.println("Call ObjB Destructor");
		super.finalize();
	}
}

public class Test {

	public static void main(String[] args) {
		ObjB obj = new ObjB(3, "madplay");
		obj.finalize();
	}
}
```

- `특정 클래스(ObjA)`가 Finalizer 메서드를 가지고 있고, `서브 클래스(ObjB)`에서 그 메서드를 override한다면, `서브 클래스(ObjB)`의 Finalizer 메서드에서 `슈퍼클래스(ObjA)`의 Finalizer를 반드시 호출해줘야 한다.

## 주의할 점2) Finalizer guardian
- 만약 위에서 처럼 서브 클래스에서 서브 클래스에서 슈퍼 클래스의 Finalizer를 override 하고 호출하지 않는 다면, 당연하게도 슈퍼 클래스의 Finalizer는 절대 실행되지 않을 것이다.
- 이를 대비 `Finalizer guardian`을 이용할 수 있다.
- 아래 예시 코드를 보면 Foo 클래스는 `finalize()` 메서드를 가지고 있지 않다.
- 따라서 서브 클래스의 `finalize() 메서드` 에서 `super.finalize() 메서드` 를 호출하고 있는지 신경 쓸 필요가 없다.


```java
// Finalizer guardian
public class Foo {
    // 이 객체의 목적은 외부 클래스(Foo) 객체의 Finalize를 수행하는 것이다.

    private final Object finalizerGuardian = new Object() {
        @Override
        protected void finalize() throws Throwable {
            ...// 외부 클래스(Foo) 객체의 Finalize를 수행하는 코드
        }
    }
}
```

> **정리**: `finalizer` 또는 `cleaner`의 경우 종료 메서드 호출을 빼먹는 경우를 대비한 안전망 역할이나, Native 자원을 종결하는 경우를 제외하고는 이용하지 말자. 그리고 만약 쓴다면 `super.finalize()` 메서드를 호출하는 것을 잊지 말고, 그도 안된다면 Finalizer guardian을 이용하자.


#### 출처
- [https://brunch.co.kr/@oemilk/122](https://brunch.co.kr/@oemilk/122)