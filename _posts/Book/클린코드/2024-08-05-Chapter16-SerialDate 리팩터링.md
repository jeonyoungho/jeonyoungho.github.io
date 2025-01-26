---
title: "[클린코드] Chapter16-SerialDate 리팩터링"
date: 2024-08-05 +0800
categories: [책, 클린코드]
tags: [cleancode]
toc: true
comments: true
published: false
---

- 일반적으로 기반 클래스 (base class, 부모 클래스)는 파생 클래스(derivative, 자식 클래스)를 몰라야 바람직하다. (p.351)
- 저자는 실질적인 가치가 없으면서 코드만 복잡하게 만드는 인수와 변수 선언된 final 키워드를 제거하는게 좋다고 한다. (p.356)
  - final을 제거하는것은 일부 기존 관례에 어긋난다. 예를 들어, 로버트 시몬스는 "코드 전체에 final을 사용하라..."고 강력히 권장한다.
  - 저자는 final 키워드는 final 상수 등 몇 군데를 제외하면 별다른 가치가 없으며 코드만 복잡하게 만든다고 한다.
  - 어쩌만 저자가 만든 테스트 케이스가 final 키워드로 잡아낼 오류를 모두 잡아내기 때문일지도 모른다고 한다.
- 일반적으로 메서드 인수로 플래그는 바람직하지 못하다. 특히 출력 형식을 선택하는 플래그는 가급적 피하는 편이 좋다.(p. 358)
- addDays 메서드는 온갖 DayDate 변수를 사용하므로 static 이어선 안된다. 그래서 인스턴스 변수로 변경하는게 좋다.(p. 360)
- addDays 메서드를 정적 메서드에서 인스턴스 메서드로 바꾸면서 뭔가 꺼림칙했다. date.addDays(5)라는 표현이 date 객체를 변경하지 않고 새 DayDate 인스턴스를 반환한다는 사실이 분명하게 드러나지 않기 떄문이다.(p.360)
  - 그래서 저자는 메서드의 원래 의도를 잘 반영하는 네이밍인 plusDays와 plusMonths라는 이름을 선택했다. **개인적으론 크게 공감되진 않는다....ㅎㅎ**

```java
DayDate date = oldDate.plusDays(5);
```

#### Reference
- 예제 코드 및 이미지 출처
  - [https://haeng-on.tistory.com/79](https://haeng-on.tistory.com/79)