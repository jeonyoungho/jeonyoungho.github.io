---
title: "[Java] WeakHashMap"
date: 2022-05-23 +0800
categories: [Backend, Java]
tags: [java, weakhashmap]
toc: true
comments: true
---

# Java에서는 세 가지 주요 유형의 참조(Reference) 방식

## 강한 참조 (Strong Reference)
- `Integer prime = 1;` 와 같은 가장 일반적인 참조 유형이다. 
- `prime 변수` 는 값이 1 인 Integer 객체에 대한 강한 참조를 가진다. 
- 이 객체를 가리키는 강한 참조가 있는 객체는 GC대상이 되지않는다.
 

## 부드러운 참조 (Soft Reference)
- `SoftReference<Integer> soft = new SoftReference<Integer>(prime);` 와 같이 SoftReference Class를 이용하여 생성이 가능하다. 
- 만약 `prime == null` 상태가 되어 더이상 원본(최초 생성 시점에 이용 대상이 되었던 Strong Reference) 은 없고 대상을 참조하는 객체가 SoftReference만 존재할 경우 GC대상으로 들어가도록 JVM은 동작한다. 
- <b>다만 WeakReference 와의 차이점은 메모리가 부족하지 않으면 굳이 GC하지 않는 점이다.</b>
- 때문에 조금은 엄격하지 않은 Cache Library들에서 널리 사용되는 것으로 알려져있다.
 

## 약한 참조 (Weak Reference)
- `WeakReference<Integer> soft = new WeakReference<Integer>(prime);` 와 같이 WeakReference Class를 이용하여 생성이 가능하다. 
- `prime == null` 되면 (해당 객체를 가리키는 참조가 WeakReference 뿐일 경우) GC 대상이 된다. 
- 앞서 이야기 한 내용과 같이 <b>SoftReference와 차이점은 메모리가 부족하지 않더라도 GC 대상이 된다는 것이다.</b>
- 그리고 나서 다음 GC가 발생하는 시점에 무조건 없어진다.

# WeakHashMap
- 일반적인 HashMap의 경우 일단 Map안에 Key와 Value가 put되면 사용여부와 관계없이 해당 내용은 삭제되지 않는다. 
- Map안의 Element들이 일부는 사용되고 일부는 사용되지 않을 수 있는 경우도 있으나, 그것의 구현은 전적으로 프로그래머에 달려있게 된다. 
- 예를 들면 Key에 해당하는 객체가 더이상 존재하지 않게 되는 경우이다.  그래서 만약 어떤 객체가 null이 되어 버리면 해당 객체를 key로 하는 HashMap의 Element도 더이상 꺼낼 일이 없는 경우가 발생하는 것을 가정해볼 수 있다.
- `WeakHashMap`은 WeakReference의 특성을 이용하여 HashMap의 Element를 자동으로 제거, GC 해버린다.   Key에 해당하는 객체가 더이상 사용되지 않는다고 판단되면 제거한다는 의미이다. 아래의 사용예를 보자.

```java
public class WeakHashMapTest {
    public static void main(String[] args) {
        WeakHashMap<Integer, String> map = new WeakHashMap<>();
        Integer key1 = 1000;
        Integer key2 = 2000;

        map.put(key1, "test a");
        map.put(key2, "test b");

        key1 = null;

        System.gc();  //강제 Garbage Collection
        map.entrySet().stream().forEach(el -> System.out.println(el));
    }
}
```

- 결과  (null 로 할당된 key1이 Map 내에서 사라졌다.)

```
2000=test b

Process finished with exit code 0
```

- 이 클래스는 주로 equals 메소드가 == 연산자를 사용하는 Key를 사용할 경우 유용하다.
  - 즉 객체의 주소값 비교를 하는 경우라 볼 수 있다.
- 그런 Key가 버려지면 결코 동일한 Key는 생성되지 않으므로, 나중에 WeakHashMap 에서 해당 키의 조회를 수행하는 것은 불가능하게 되기 때문이다. 
- <b>그러나 Key가 String 과 같이 단일 값을 가지는 Class고 == 연산자 외에 equals를 사용하여 서로 다른 객체라도 동일성을 체크하여 유지 하고 싶은 경우 적절하지 않다.</b>
  - 주소값을 비교하는게 아닌 객체의 실제 내부를 비교하는 경우라 볼 수 있다.
- WeakHashMap은 HashMap과 마찬가지로 <b>Thread-safe하지 않으며 필요할 경우 Collections.synchronizedMap 을 이용할 수 있다.</b>

> **Note**: String은 new가 아닌 리터럴 방식으로 생성될 경우 (String a = “abc”; 와 같이)  intern()에 의해 `String pool`을 JVM은 사용하므로 key가 null이 된다고 하도 WeakHashMap은 자동으로 삭제되지 않는다.  이는 Integer 와 같은 Class의 (-127 ~128) 값들도 마찬가지 이다.  JVM은 미리 불변 객체로 해당 값을 보관하고 있다. (Strong 참조)

#### 출처
- [https://blog.breakingthat.com/2018/08/26/java-collection-map-weakhashmap/](https://blog.breakingthat.com/2018/08/26/java-collection-map-weakhashmap/)