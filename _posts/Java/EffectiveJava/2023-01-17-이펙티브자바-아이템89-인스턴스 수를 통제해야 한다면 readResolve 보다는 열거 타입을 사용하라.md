---
title: "[이펙티브자바] 아이템89-인스턴스 수를 통제해야 한다면 readResolve 보다는 열거 타입을 사용하라"
# post의 layout이 기본적으로 post으로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 된다.
date: 2023-01-17 +0800
categories: [Java, EffectiveJava] # categories는 최대 2개까지 가능
tags: [java, effectivejava] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
---

> **핵심 정리**: 불변식을 지키기 위해 인스턴스를 통제해야 한다면 가능한 한 열거 타입을 사용하자. 여의치 않은 상황에서 직렬화와 인스턴스 통제가 모두 필요하다면 readResolve 메서드를 작성해 넣어야 하고, 그 클래스에서 모든 참조 타입 인스턴스 필드를 transient 로 선언해야 한다.

### 싱글턴 인스턴스의 직렬화

- 앞선 아이템 3에서는 아래와 같은 싱글턴 패턴 예제를 보았다. `public static final` 필드를 사용하는 방식이다. 
- 생성자는 private 접근 지정자로 선언하여 외부로부터 감추고 INSTANCE를 초기화할 때 딱 한 번만 호출된다.

```java
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() { }

    ...
}
```

- 이클래스는 `implements Serializable`을 추가하는 순간 더 이상 싱글턴이 아니게 된다.
  - 기본 직렬화를 쓰지 않거나 명시적인 readObject 메서드를 제공하더라도 소용이 없다. 
  - 어떤 ReadObject 메서드를 사용하더라도 초기화될 때 만들어진 인스턴스와 다른 인스턴스를 반환하게 된다.


### readResolve
- `readResolve` 기능을 이용하면 readObject 메서드가 만든 인스턴스를 다른 것으로 대체할 수 있다.
  - readResolve에서 역직렬화로 새로 생성된 객체의 참조를 반환하는게 아닌 기존 싱글턴 인스턴스의 참조를 반환하는 것이다.
  - 이때 readObject 가 만들어낸 인스턴스는 가비지 컬렉션의 대상이 된다.
- 앞의 Elvis 클래스가 Serializable을 구현한다면 다음의 readResolve 메서드를 추가해 싱글턴이라는 손성을 유지할 수 있다.

```java
// 인스턴스 통제를 위한 readResolve - 개선의 여지가 있다!
private Object readResolve() {
    // 기존에 생성된 인스턴스를 반환한다.
    return INSTANCE;
}
```

- 위 메서드는 역직렬화한 객체는 무시하고 클래스 초기화시 만들어진 Elvis 인스턴스를 반환한다.
  - 따라서 Elvis 인스턴스의 직렬화 형태는 아무런 실 데이터를 가질 이유가 없으니 모든 인스턴스 필드를 transient로 선언해야 한다.
- <b>사실, readResolve를 인스턴스 통제 목적으로 사용한다면 객체 참조 타입 인스턴스 필드는 모두 transient로 선언해야 한다.</b>
  - 그렇지 않으면 아이템88에서 살펴본 MutablePeriod 공격과 비슷한 방식으로 readResolve 메서드가 수행되기 전에 역직렬화된 객체의 참조를 공격할 여지가 남는다.


### 공격 방법
- 다소 복잡한 공격 방법이지만 기본 아이디어는 간단하다. 싱글턴이 transient 가 아닌(non-transient) 참조 필드를 가지고 있다면, 그 필드의 내용은 readResolve 메서드가 실행되기 전에 역직렬화된다. 
  - 그렇다면 잘 조작된 스트림을 써서 해당 참조 필드의 내용이 역직렬화되는 시점에 그 역직렬화된 인스턴스의 참조를 훔쳐올 수 있다.
- 더 자세한 방법은 아래와 같다.

1) readResolve 메서드와 인스턴스 필드 하나를 포함한 도둑(stealer) 클래스를 작성한다.
=> 해당 클래스의 인스턴스 필드는 도둑이 숨길 직렬화된 싱글턴을 참조하는 역할을 한다.

2) <b>직렬화된 스트림에서 싱글턴의 비휘발성 필드를 이 도둑의 인스턴스로 교체한다.</b>
=> 이제 싱글턴은 도둑을 참조하고 도둑은 싱글턴을 참조하는 순환고리가 만들어졌다.

4) 싱글턴이 도둑을 포함하므로 싱글턴이 역직렬화될 때 도둑의 readResolve 메서드가 먼저 호출된다.
=> 그 결과, 도둑의 readResolve 메서드가 수행될 때 도둑의 인스턴스 필드에는 역직렬화 도중인(그리고 readResolve가 수행되기 전인) 싱글턴의 참조가 담겨 있게 된다.

5) 도둑의 readResolve 메서드는 이 인스턴스 필드가 참조한 값을 정적 필드로 복사하여 readResolve가 끝난 후에도 참조할 수 있도록 한다.

2) 그런 다음 이 메서드는 도둑이 숨긴 transient가 아닌 필드의 원래 타입에 맞는 값을 반환한다.

3) 이 과정을 생략하면 직렬화 시스템이 도둑의 참조를 이 필드에 저장하려 할 때 VM이 ClassCastException을 던진다.

### 잘못된 싱글턴 예시

#### `transient`가 아닌 참조 필드를 가지고 있는 경우

```java
// 코드 89-1 잘못된 싱글턴 - transient가 아닌 참조 필드를 가지고 있다!
public class Elvis implements Serializable {
    public static final Elvis INSTANCE = newElvis();

    private Elvis() {
    }

    private String[] favoriteSongs = {"Hound Dog", "Heartbreak Hotel"};

    public void printFavorites() {
        System.out.println(Arrays.toString(favoriteSongs));
    }

    private Object readResolve() {
        return INSTANCE;
    }
}
```

#### `non-transient` 참조 필드를 훔쳐오는 도둑(stealer) 클래스

```java
// 코드 89-2 도둑 클래스
public class ElvisStealer implements Serializable {
    static Elvis impersonator;
    private Elvis payload;

    private Object readResolve() {
        // resolve되기 전의 Elvis 인스턴스의 참조를 저장한다. 
        impersonator = payload;
        // favoriteSongs 필드에 맞는 타입의 객체를 반환한다. 
        return new String[]{"A Fool Such as I"};
    }

    private static final long serialVersionUID = 0;
}
```

```java
// 코드89-3직렬화의 허점을 이용해 싱글턴 객체를 2개 생성한다.
public class ElvisImpersonator {
    // 진짜 Elvis 인스턴스로는 만들어질 수 없는 바이트 스트림
    private static final byte[] serializedForm = {
            -84, -19, 0, 5, 115, 114, 0, 20, 107, 114, 46, 115,
            101, 111, 107, 46, 105, 116, 101, 109, 56, 57, 46, 69,
            108, 118, 105, 115, 98, -14, -118, -33, -113, -3, -32,
            70, 2, 0, 1, 91, 0, 13, 102, 97, 118, 111, 114, 105, 116,
            101, 83, 111, 110, 103, 115, 116, 0, 19, 91, 76, 106, 97,
            118, 97, 47, 108, 97, 110, 103, 47, 83, 116, 114, 105, 110,
            103, 59, 120, 112, 117, 114, 0, 19, 91, 76, 106, 97, 118,
            97, 46, 108, 97, 110, 103, 46, 83, 116, 114, 105, 110, 103,
            59, -83, -46, 86, -25, -23, 29, 123, 71, 2, 0, 0, 120, 112,
            0, 0, 0, 2, 116, 0, 9, 72, 111, 117, 110, 100, 32, 68, 111,
            103, 116, 0, 16, 72, 101, 97, 114, 116, 98, 114, 101, 97, 107,
            32, 72, 111, 116, 101, 108
    };

    public static void main(String[] args) {
        // ElvisStealer.impersonator를 초기화한 다음,
        // 진짜 Elvis(즉 Elvis.INSTANCE)를 반환한다.
        Elvis elvis = (Elvis) deserialize(serializedForm);
        Elvis impersonator = ElvisStealer.impersonator;

        elvis.printFavorites();
        impersonator.printFavorites();
    }
}
```

- 위 프로그램을 실행하면 결과는 아래와 같이 출력된다. 이것으로 서로 다른 2개의 Elvis 인스턴스를 생성할 수 있음을 증명했다.

```java
[Hound Dog, Heartbreak Hotel]
[A Fool Such as I]
```

### 인스턴스 수 통제를 위한 열거 타입 사용
- `favoriteSongs` 필드를 `transient로` 선언하여 이 문제를 고칠 수 있지만 Elvis를 원소 하나짜리 열거 타입으로 바꾸는 편이 더 낫다.
  - `readResolve` 메서드를 사용해 '순간적으로' 만들어진 역직렬화된 인스턴스에 접근하지 못하게 하는 방법은 깨지기 쉽고 신경을 많이 써야 하는 작업이다.
- 직렬화 가능한 인스턴스 통제 클래스를 열거 타입을 이용해 구현하면 선언한 상수 외에 다른 객체는 존재하지 않음을 자바가 보장해준다.
    - 공격자가 AccessibleObject.setAccessible 같은 특권(previleged) 메서드를 악용하면 이야기가 달라진다.
    - 임의의 네이티브 코드를 수행할 수 있는 특권을 가로챈 공격자에겐 모든 방어가 무력화된다.

```java
// [코드 89-4] 열거 타입 싱글턴 - 전통적인 싱글턴보다 우수하다.
public enum Elvis {
	INSTANCE;
    private String[] favoriteSongs = { "Hound Dog", "Heartbreak Hotel" };
    public void printFavorites() {
    	System.out.println(Arrays.toString(favoriteSongs));
    }
}
```

- 인스턴스 통제를 위해 readResolve를 사용하는 방식이 완전히 쓸모없는 것은 아니다.
  - 컴파일타임에는 어떤 인스턴스들이 있는지 알 수 없는 상황이라면 열거 타입으로 표현하는 것이 불가능하기 때문이다.


#### readResolve 메서드의 접근성은 매우 중요하다
- final 클래스에서라면 readResolve 메서드는 private이여야 한다.
- final이 아닌 클래스에선 아래의 몇 가지를 주의해서 고려해야 한다.
  - private로 선언하면 하위 클래스에서 사용할 수 없다.
  - package-private으로 선언하면 같은 패키지에 속한 하위 클래스에서만 사용할 수 있다.
  - protected나 public으로 선언하면 이를 재정의하지 않은 모든 하위 클래스에서 사용할 수 있다.
  - protected나 public이면서 하위 클래스에서 재정의하지 않았다면, 하위 클래스의 인스턴스를 역직렬화하면 상위 클래스의 인스턴스를 생성하여 ClassCastException을 일으킬 수 있다.


#### 출처
- [https://velog.io/@yun12343/Java-%EC%A7%81%EB%A0%AC%ED%99%94-5-%EC%95%84%EC%9D%B4%ED%85%9C-89](https://velog.io/@yun12343/Java-%EC%A7%81%EB%A0%AC%ED%99%94-5-%EC%95%84%EC%9D%B4%ED%85%9C-89)