---
title: "[Java] String & StringBuffer & StringBuilder"
# post의 layout이 기본적으로 post으로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 된다.
date: 2021-07-11 +0800
categories: [Java] # categories는 최대 2개까지 가능
tags: [java, string, stringbuffer, stringbuilder] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
---

Java에서 문자열을 다루는 대표적인 클랫스로 String, StringBuffer, StringBuilder가 있습니다. 연산이 많지 않을때는 어떤 클래스를 사용하더라도 이슈가 발생할 가능성은 거의 없지만 연산 횟수가 많아지거나 멀티쓰레드를 사용하는 경우라면 <b>각 클래스의 특징을 이해하고 적절한 클래스를 사용해야 합니다.</b>

# String vs StringBuffer, StringBuilder

String과 StringBuffer/StringBuilder의 가장 큰 차이점은 `String은 불변(immutable)의 속성`을 갖는다는 것 입니다.

~~~
String str = "hello"; // String str = new String("hello"); 
str = str + " world"; // [ hello world ]
~~~

위의 예제에서 "hello"값을 가지고 있던 String 클래스의 참조변수 str이 가리키고 있는 곳에 "world"를 더해 "hello world"로 변경한 것으로 착각할 수 있습니다.

하지만 기존에 "hello"값이 들어가 있던 String클래스의 참조변수 str이 "hello world"라는 값을 가지고 있는 새 메모리 영역을 가리키게 되는 것이고, 처음 선언했던 처음 선언했던 "hello"로 할당되어 있던 메모리 영역은 GC에 의해 더 이상 참조되지 않아 사라지게 되는 것입니다. <b>String 클래스는 불변하기 때문에 문자열을 수정하는 시점에 새로운 String 인스턴스가 생성되는 것 입니다.</b>

![1](https://user-images.githubusercontent.com/44339530/126058173-7503c864-492a-49b6-8ef0-b61da6641a71.png)

위와 같이 String은 immutable한 속성 때문에 <b>변하지 않는 문자열을 자주 읽어들이는 경우 String을 사용한다면 좋은 성능을 기대할 수 있습니다. 그러나 <b>문자열 추가, 수정, 삭제 등의 연산이 빈번하게 발생</b> 할 경우 String을 사용한다면 <b>힙 메모리(Heap)에 많은 임시 가비지가 계속 생성되어</b> 힙 메모리 부족으로 애플리케이션 성능에 치명적인 영향을 끼치게 됩니다.

이를 해결 하기 위해 Java에서는 `가변(mutable)성`을 가지는 `StringBuffer/StringBuilder` 클래스를 도입했습니다. 이로 인해, StringBuffer/StringBuilder를 사용하면 append(), delete() 등의 API를 이용하여 <b>동일 객체내에서 문자열을 변경하는 것이 가능해집니다.</b> 따라서 문자열의 추가, 수정, 삭제가 빈번하게 발생할 경우라면 String클래스가 아닌 StringBuffer/StringBuilder를 사용해야 합니다.

~~~
StringBuffer sb= new StringBuffer("hello"); 
sb.append(" world");
~~~

![2](https://user-images.githubusercontent.com/44339530/126058308-28ad7470-4a5e-4a2c-babb-aaff55ae1175.png)

# StringBuffer vs StringBuilder

StringBuffer와 StringBuilder의 가장 큰 차이점은 `동기화의 유무`입니다.
- StringBuffer: 동기화 지원, 멀티쓰레드 환경에서 안전(thread-safe), StringBuilder에 비해 느린 처리 속도
    - <b>String도 불변성을 가지기때문에 마찬가지로 멀티쓰레드 환경에서의 안전성을 가지고 있음</b>
- StringBuilder: 동기화 미지원, 멀티쓰레드 환경에서 안전하지 않음(non-thread-safe), StringBuffer에 비해 빠른 처리 속도

# 정리
- String: 문자열 연산이 적고 멀티쓰레드 환경일 경우
- StringBuffer: 문자열 연산이 많고 멀티쓰레드 환경일 경우
- StringBuilder: 문자열 연산이 많고 단일쓰레드이거나 동기화를 고려하지 않아도 되는 경우

![3](https://user-images.githubusercontent.com/44339530/126058408-363e7adc-32b6-4ad2-a747-d1867d65416b.png)
_출처 - [tuandevnotes.com](https://tuandevnotes.com)_

#### 출처
- [https://ifuwanna.tistory.com/221](https://ifuwanna.tistory.com/221)