---
title: "[이펙티브자바] 아이템11-equals를 재정의하려거든 hashcode도 재정의하라"
# post의 layout이 기본적으로 post으로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 된다.
date: 2022-06-05 +0800
categories: [Java, EffectiveJava] # categories는 최대 2개까지 가능
tags: [java, effectivejava] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
---

# equals를 재정의한 클래스 모두에서 hashCode도 재정의해야 한다.
- 그렇지 않으면 hashCode 일반 규약을 어기게 되어 해당 클래스의 인스턴스를 `HashMap`이나 `HashSet`과 같은 컬렉션의 원소로 사용할 때 문제를 일으킬 것이다.

# Object 명세에서 언급하는 hashcode 규약

- equals 비교에 사용되는 정보가 변경되지 않았다면, hashCode 도 변하면 안 된다.
    - 애플리케이션을 다시 실행한다면 이 값이 달라져도 상관 없다.
- <b>equals가 두 객체가 같다고 판단했다면, 두 객체의 hashCode는 똑같은 값을 반환한다.</b>
    - 논리적으로 같은 객체는 같은 해시코드를 반환해야 한다.
- equals가 두 객체를 다르다고 판단했더라도, hashCode는 꼭 다를 필요는 없다.
    - 단, 다른 객체에 대해서는 다른 값을 반환해야 해시테이블의 성능이 좋아진다.
    - => <b>equals가 false로 리턴하는 두 객체는 다른 hashcode를 가져야만 하는것이 아닌가?</b>

<b>hashCode 재정의를 잘못했을 때 크게 문제가 되는 조항은 두 번째다. 즉 논리적으로 같은 객체는 같은 해시코드를 반환해야 한다.</b>
equals는 물리적으로 다른 두 객체를 논리적으로 같다고 할 수 있지만, Object의 기본 hashcode 메서드는 이 둘이 전혀 다르다고 판단하여 규약과 달리 서로 다른 값을 반환한다.

```java
// equals를 재정의하면 hashCode로 재정의해야 함을 보여준다. (70-71쪽)
public final class PhoneNumber {
    private final short areaCode, prefix, lineNum;

    public PhoneNumber(int areaCode, int prefix, int lineNum) {
        this.areaCode = rangeCheck(areaCode, 999, "area code");
        this.prefix   = rangeCheck(prefix,   999, "prefix");
        this.lineNum  = rangeCheck(lineNum, 9999, "line num");
    }

    private static short rangeCheck(int val, int max, String arg) {
        if (val < 0 || val > max)
            throw new IllegalArgumentException(arg + ": " + val);
        return (short) val;
    }

    @Override public boolean equals(Object o) {
        if (o == this)
            return true;
        if (!(o instanceof PhoneNumber))
            return false;
        PhoneNumber pn = (PhoneNumber)o;
        return pn.lineNum == lineNum && pn.prefix == prefix
                && pn.areaCode == areaCode;
    }

    ...
}
```

```java
public static void main(String[] args) {
        Map<PhoneNumber, String> m = new HashMap<>();
        m.put(new PhoneNumber(707, 867, 5309), "제니");
        System.out.println(m.get(new PhoneNumber(707, 867, 5309)));
}
```

위 예시 코드에서 Map의 get함수를 호출 할 때 실제로 null이 반환된다. 여기엔 2개의 PhoneNumber 인스턴스가 사용되었고 hascode를 재정의하지 않았기 때문에 처음에 넣었던 인스턴스를 찾을 수 없는 것이다.

즉, 논리적 동치인 두 객체가 서로 다른 해시코드를 반환하여 두 번째 규약을 지키지 못한 것이다. 그 결과 get 메서드는 엉뚱한 해시 버킷에 가서 객체를 찾으려 한 것이다. HashMap은 해시코드가 다른 엔트리끼리는 동치성 비교를 시도조차 하지 않도록 최적화되어 있다.

이에 대한 해법은 `PhoneNumber`클래스에 적절한 hashCode메서드만 작성해주면 해결된다.

## 잘못된 hashCode 구현의 예

- 동치인 모든 객체에서 똑같은 해시코드를 반환하는 경우이다.

```java
@Override public int hashCode() {
    return 42;
}
```

- 끔찍하게도 모든 객체에게 똑같은 값만 내어주므로 모든 객체가 해시테이블의 버킷 하나에 담겨 마치 연결리스트처럼 동작한다. 
- 그 결과 평균 수행시간이 O(1)에서 O(n)으로 느려져서, 객체가 많아지면 도저히 쓸 수 없게 된다.

## 좋은 해시 함수의 예
- 서로 다른 인스턴스에 다른 해시코드를 반환한다.
- hashCode의 세번쨰 규약이 요구하는 속성이다.
- 좋은 hashCode를 작성하는 간단한 요령은 아래와 같다.


### 1) int 변수인 result를 선언한 후 값을 첫번째 핵심 필드로 초기화한다.
- 해당 객체의 첫번째 핵심 필드를 아래에서 설명하는 `필드의 해시코드 계산 방식`으로 계산한 값이다.
- 여기서 핵심 필드는 equals 비교에 사용되는 필드를 말한다.(아이템10 참조)

### 2) 해당 객체의 나머지 핵심 필드인 f 각각에 대해 다음 작업을 수행한다.
- 1)해당 필드의 해시코드 c 를 계산한다.
    - 기본 타입 필드라면, Type.hashCode(f)를 수행한다. 여기서 Type은 해당 기본타입의 박싱 클래스다.
    - 참조 타입 필드면서, 이 클래스의 equals 메소드가 이 필드의 equals를 재귀적으로 호출하여 비교한다면, 이 필드의 hashCode를 재귀적으로 호출한다.
    - 필드가 배열이라면, 핵심 원소 각각을 별도 필드처럼 다룬다. 모든 원소가 핵심 원소라면 Arrays.hashCode를 사용한다.

### 3) 단계2에서 계산한 해시코드로 result를 갱신한다.
- `result = 31 * result + c;`
- 곱셉 `31 * result`는 필드를 곱하는 순서에 따라 result 값이 달라지게 한다.
    - 예컨데 String의 hashCode를 곱셈 없이 구현한다면 모든 아나그램(anagram, 구성하는 철자가 같고 그 순서만 다른 문자열)의 해시코드가 같아진다.
    - ex. 'abc' 와 'cba'
- 곱할 숫자를 31로 정한 이유는 31이 홀수이면서 소수(prime)이기 떄문이다.
    - 만약 이 숫자가 짝수이고 오버플로가 발생한다면 정보를 잃게 된다.
    - 결과적으로 31을 이용한다면, 이 곱셈을 시프트 연산과 뺄셈으로 대체해 최적화 할 수 있다.

### 4) result를 반환한다.


# 올바른 hashCode메서드

```java
// 코드 11-2 전형적인 hashCode 메서드 (70쪽)
@Override public int hashCode() {
    int result = Short.hashCode(areaCode);
    result = 31 * result + Short.hashCode(prefix);
    result = 31 * result + Short.hashCode(lineNum);
    return result;
}
```

- 이 메서드는 PhoneNumber 인스턴스의 핵심 필드 3개만을 사용해 간단한 계싼만 수행하고 동치인 인스턴스들은 같은 해시코드를 가질 것이 확실하다.
- 단, 해시 충돌이 더욱 적은 방법을 꼭 써야 한다면 구아바의 `com.google.common.hash.Hashing`을 참고하자[Guava]


# hashCode를 다 구현했다면 이 메서드가 동치인 인스턴스에 대해 똑같은 해시코드를 반환할지 자문해보자.
- 단위 테스트를 작성하자.(equals와 hashCode 메서드를 AutoValue로 생성했따면 건너뛰어도 좋다)
- 동치인 인스턴스가 서로 다른 해시코드를 반환한다면 원인을 찾아 해결하자.


# hashCode를 작성할 떄 주의사항

## 1) 파생 필드는 <b>해쉬 코드 계산에서 제외해도 된다.</b>
- 즉, 다른 필드로부터 계산해낼 수 있는 필드는 모두 무시해도 된다.

## 2) equals 비교에 사용되지 않은 필드는 반드시 제외해야 한다.
- 그렇지 않으면 hashCode 두번쨰 규약을 어기게 될 위험이 있다.

## 3) Objects 클래스는 임의의 개수만큼 객체를 받아 해시코드를 계산해주는 정적 메서드인 hash를 제공한다.

<img width="707" alt="스크린샷 2022-06-05 오후 8 21 20" src="https://user-images.githubusercontent.com/44339530/172048106-ec160c9c-4ba6-4f4c-85cd-356894183e01.png">

- 이 메서드를 활용하면 앞서의 요령대로 구현한 코드와 비슷한 수준의 hashCOde 함수를 단 한 줄로 작성할 수 있다.
- <b>하지만 아쉽게도 속도는 더 느리다.</b>
    - 인수를 담기위한 배열이 만들어지고, 입력 중 기본 타입이 있다면 박싱과 언박싱도 거쳐야 하기 때문이다.
    - 그러니 성능에 민감하지 않은 상황에서만 사용하자.
    - `Objects.hash()`를 사용한 예제 코드는 아래와 같다.

```java
// 코드 11-3 한 줄짜리 hashCode 메서드 - 성능이 살짝 아쉽다. (71쪽)
@Override public int hashCode() {
    return Objects.hash(lineNum, prefix, areaCode);
}
```

## 4) 클래스가 불변이고 해시코드를 계산하는 비용이 크다면, 매번 새로 계산하기 보단 캐싱하는 방식을 고려하자.
- 이 타입의 객체가 주로 해시의 키로 사용될 것 같다면 인스턴스가 만들어질때 해시코드를 계산해둬야 한다.
- 해시의 키로 사용되지 않는 경우라면 hashCode가 처음 불릴 때 계산하는 지연 초기화(lazy initialization) 전략을 고려하자.
    - 필드를 지연 초기화하려면 그 클래스를 스레드 안전하게 만들도록 신경써야 한다.(아이템 83)

```java
// 해시코드를 지연 초기화하는 hashCode 메서드 - 스레드 안정성까지 고려해야 한다. (71쪽)
private int hashCode; // 자동으로 0으로 초기화된다.

@Override public int hashCode() {
    int result = hashCode;
    if (result == 0) {
        result = Short.hashCode(areaCode);
        result = 31 * result + Short.hashCode(prefix);
        result = 31 * result + Short.hashCode(lineNum);
        hashCode = result;
    }
    return result;
}
```

## 5) 성능을 높인답시고, 해시코드를 계산할때 핵심 필드를 생략해선 안된다.
- 해시 품질이 나빠져 해시테이블의 성능을 심각하게 떨어 뜨릴 수 있다.
- 특히 어떤 필드는 특정 영역에 몰린 인스턴스들의 해시코드를 넓은 범위로 고르게 퍼뜨려주는 효과가 있을지도 모른다.
    - 핵심 필드를 빠뜰릴 경우 수 많은 인스턴스가 단 몇개의 해시코드로 집주오디어 해시테이블의 속도가 선형으로 느려질 것이다.
- 이 문제는 이론에 그치지 않다. 실제 자바2 이전의 String은 최대 16개의 문자만으로 해시코드를 계산했는데 URL처럼 계층적인 이름을 대량으로 사용한다면 이런 해쉬함수는 앞서 이야기한 심각한 문제를 고스란히 드러낸다.

## 6) hashCode가 반환하는 값의 생성 규칙을 API사용자에게 자세히 공표하지 말자. 그래야 클라이언트가 이 값에 의지하지 않게 되고, 추후에 계산 방식을 바꿀 수도 있다.
- String과 Integer를 포함해 자바 라이브러리의 많은 클래스에서 hashCode 메서드가 반환하는 정확한 값을 알려주기에 다음 릴리스에서 해시 기능을 개선할 여지가 사라져 버렸다... 더 나은 해시 방식을 알아내더라도 수정 할 수가 없게되어 버린 것이다.

> **핵심 정리**: `equals`를 재정의할 때는 `hashCode`도 반드시 재정의해야 한다. 그렇지 않으면 프로그램이 제대로 동작하지 않을 것이다.
재정의한 hashCode는 Object의 API 문서에 기술된 일반 규약을 따라야 하며, 서로 다른 인스턴스라면 되도록 해시코드도 서로 다르게 구현해야 한다. AutoValue 프레임워클르 사용하면 멋진 equals와 hashCode를 자동으로 만들어준다. IDE들도 이런 기능을 이부 제공한다.


#### 출처
- [https://jisooo.tistory.com/entry/java-hashcode%EC%99%80-equals-%EB%A9%94%EC%84%9C%EB%93%9C%EB%8A%94-%EC%96%B8%EC%A0%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B3%A0-%EC%99%9C-%EC%82%AC%EC%9A%A9%ED%95%A0%EA%B9%8C](https://jisooo.tistory.com/entry/java-hashcode%EC%99%80-equals-%EB%A9%94%EC%84%9C%EB%93%9C%EB%8A%94-%EC%96%B8%EC%A0%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B3%A0-%EC%99%9C-%EC%82%AC%EC%9A%A9%ED%95%A0%EA%B9%8C)