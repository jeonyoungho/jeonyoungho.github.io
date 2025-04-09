---
title: "[이펙티브자바] 아이템16-public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라"
date: 2022-07-12 +0800
categories: [책, EffectiveJava]
tags: [java, effectivejava]
toc: true
comments: true
---

# 객체 지향 프로그래머는 필드를을 모두 private으로 바꾸고, public 접근 제어자(getter)를 추가한다.

```java
// 이처럼 툅환 클래스는 public이어선 안된다.
class Point {
  public double x;
  public double y;
}
```

위와 같은 클래스는 데이터 필드에 직접 접근할 수 있으니 캡슐화의 이점을 제공하지 못한다.(아이템15)

```java
// 코드 16-2 접근자와 변경자(mutator) 메서드를 활용해 데이터를 캡슐화한다. (102쪽)
class Point {
    private double x;
    private double y;

    public Point(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double getX() { return x; }
    public double getY() { return y; }

    public void setX(double x) { this.x = x; }
    public void setY(double y) { this.y = y; }
}
```

위의 개선된 코드처럼 <b>패키지 바깥에서 접근할 수 있는 클래스라면 접근자를 제공함으로써 클래스 내부 표현 방식을 언제든 바꿀 수 있는 유연성을 얻을 수 있다.</b> 만약 public 클래스가 필드를 공개하면 이를 사용하는 클라이언트가 생겨날 것이므로 추후에 내부 표현 방식을 마음대로 바꾸기 힘들어지게 된다.


# 하지만 package-private 클래스 혹은 private 중첩 클래스라면 데이터 필드를 노출한다 해도 하등의 문제가 없다.
- 이 방식은 클래스 선언명네서나 이를 사용하는 클라이언트 코드면에서나 접근자 방식보다 훨씬 깔끔하다.
- private 중첩 클래스의 경우라면 수정 범위가 더 좁아져서 이 클래스를 포함하는 외부 클래스까지로 제한된다.

# 자바 플랫폼 라이브러리에도 public 클래스의 필드를 직접 노출하는 사례(Point, Dimenstion 클래스)가 있지만 절대 흉내내면 안된다.

# public 클래스의 필드가 불변이라면 직접 노출할때의 단점이 조금은 줄어들지만, 여전히 결코 좋은 생각이 아니다.
- API를 변경하지 않고는 표현 방식을 바꿀 수 없고, 필드를 읽을 때 부수 작업을 수행할 수 없다는 단점은 여전하다.
  - `API를 변경하지 않고는 표현 방식을 바꿀 수 없고` 가 어떤 것을 의도하는지 이해하기 어렵다.
  - `필드를 읽을 때 부수 작업을 수행할 수 없다` 는 점도 직관적으로 받아들여지지 않는다.

```java
// 코드 16-3 불변 필드를 노출한 public 클래스 - 과연 좋은가? (103-104쪽)
public final class Time {
    private static final int HOURS_PER_DAY    = 24;
    private static final int MINUTES_PER_HOUR = 60;

    public final int hour;
    public final int minute;

    public Time(int hour, int minute) {
        if (hour < 0 || hour >= HOURS_PER_DAY)
            throw new IllegalArgumentException("Hour: " + hour);
        if (minute < 0 || minute >= MINUTES_PER_HOUR)
            throw new IllegalArgumentException("Min: " + minute);
        this.hour = hour;
        this.minute = minute;
    }

    // 나머지 코드 생략
}
```

> **핵심 정리**: public 클래스는 절대 가변 필드를 직접 노출해서는 안 된다. 불변 필드라면 노출해도 덜 위험하지만 완전히 안심할 수는 없다. 하지만 package-private 클래스나 private 중첩 클래스에서는 종종 (불변이든 가변이든) 필드를 노출하는 편이 나을 때도 있다.