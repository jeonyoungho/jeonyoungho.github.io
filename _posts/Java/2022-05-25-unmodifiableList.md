---
title: "[Java] unmodifiableList"
# post의 layout이 기본적으로 post으로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 된다.
date: 2022-05-25 +0800
categories: [Java] # categories는 최대 2개까지 가능
tags: [java, unmodifiablelist] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
---

# Unmodifiable
`Collection.unmodifiableList()` 같은 메소드에서 리턴되는 레퍼런스는 수정 메소드를 호출 할 수 없다.
여기서 `수정 메서드` 라 함은 <b>add(), set(), addAll()</b> 등을 의미한다. 만약 호출하게 된다면 `UnsupportedOperationException`이 발생한다.

하지만 원본 리스트 자체가 수정되지 않도록 보장해주진 않는다. 또한 여기서 원본 리스트를 수정하면 `unmodifiableList` 자체도 동일하게 수정된다.

예제를 살펴보자.

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class Test {

	public static void main(String []args) {
		List<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(2);
        list.add(3);

        List<Integer> unmodifialbeList = Collections.unmodifiableList(list);

        try {
            unmodifialbeList.add(4);
            System.out.println("cannot be reached here");
        } catch (UnsupportedOperationException e) {
            System.out.println("Cannot modify unmodifialbe list");
        }

        list.add(4);

        System.out.println(unmodifialbeList.get(3));
	}
}
```

결과는 아래 이미지와 같이 `unmodifialbeList.add()`를 호출할 경우 `UnsupportedOperationException` 이 발생하는 것을 확인하실 수 있다.
그리고 원본 list에는 정상적으로 `list.add()` 가 동작한 것을 확인하실 수 있으며 `unmodifialbeList` 자체도 동일하게 4가 추가된 것을 확인하실 수 있다.

![image](https://user-images.githubusercontent.com/44339530/170145092-f33432b3-b16b-4ebc-8788-48f38ef41ee3.png)

![image](https://user-images.githubusercontent.com/44339530/170145296-bede57d8-6c06-40e9-b04e-70028c93fa66.png)

# Immutable(불변) vs Unmodifiable Collection
만약 객체의 상태가 Immutable이라 한다면 무조건 수정할 수 없어야 한다.
unmodifiableList는 원본 리스트를 변경함으로써 `unmodifialbeList`를 수정할 수 있기 때문에 Immutable을 만족하지 않는다. 

기존에 존재하는 컬렉션을 Immutable로 만들기 위해서는 기존 컬렉션의 데이터를 새로운 컬렉션으로 복사한 다음, 새로운 컬렉션으로의 수정(modify) 접근을 제한하는게 일반적이다.

예를들어

```java
List<String> immutableList = Collections.unmodifiableList(new ArrayList<String>(list));
```

원본 컬렉션인 list의 내용을 복사해서 unmodifiableList를 만들기 때문에 여기서 리턴되는 List는 `immutable` 이다. 

#### 출처
- [https://soft.plusblog.co.kr/71](https://soft.plusblog.co.kr/71)