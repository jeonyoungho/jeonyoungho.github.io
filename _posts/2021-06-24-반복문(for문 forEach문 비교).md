---
title: "[개발자 블로그] 반복문(for문 forEach문 비교)"
# post의 layout이 기본적으로 post으로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 된다.
date: 2021-06-24 +0800
categories: [Java] # categories는 최대 2개까지 가능
tags: [Java, 반복문, forloop, forEach] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
---

프로그래밍을 배우는데 있어서 기본 중의 하나는 반복문이다. 하지만 반복문을 어떻게 사용하느냐가 애플리케이션 성능에 얼마나 영향을 끼치는지 생각을 해보게되었다. Java에서 Collection을 순환할 때 forEach문을 자연스레 사용하고 있었고, 일반 for문하고 가독성 외에는 성능적으로 차이가 없을거라 생각했다. 하지만 for문과 forEach문은 완전 다른 방식으로 작동하고 있으며, 성능적으로 큰 차이가 있었다.

### for-each(Enhanced for)
JDK5.0부터 지원하며 아래 코드와 같이 형변환이나 get메소드 호출 없이 순서에 따라 순회할 수 있다. 
~~~
for (int number : numbers) {
    System.out.println(number + " ");
}
~~~

for-each문의 특징은 아래와 같다.
- 별도의 형변환이나 get() 메서드를 호출할 필요가 없어 boiler plate코드(기본 코드)를 줄일 수 있다.
- 순서를 보장하며 순회할 때 index관련 Exception을 신경쓸 필요가 없다.
- 하지만, 역순으로 순회하거나 특정 값부터 순회하는 경우에는 적절하지 않다.

for-each문이 내부적으로 동작하는 방식을 Collection타입과 Array타입으로 살펴볼 수 있다.
#### Collection타입
~~~
Iterator iter = numbers.iterator();

while(iter.hasNext()) {
    Integer number = (Integer)iter.next();
    System.out.println(number + " ");
}
~~~
위의 코드와 같이 내부적으로 Iterator객체를 얻어온 다음에 hasNext()메서드와 next()메서드를 사용하여 동작한다. for-each문은 Iterable 인터페이스의 iterator() 메서드가 필요하고 Collection인터페이스도 Iteable인터페이스를 구현하고 있기에 위와 같이 동작한다.

#### Array타입
~~~
Integer[] var1 = numbers;
int var2 = numbers.length;

for(int var3 = 0; var3 < var2; ++var3) {
    Integer number = var1[var3];
    System.out.println(number);
}
~~~
Array타입인 경우엔 위의 코드와 같이 일반 for문으로 적절하게 변환 후 실행되는 것을 볼 수 있다. 컴파일한 클래스 파일을 디컴파일하여 확인할 수 있다.

### Array, ArrayList, LinkedList의 for문 및 forEach문 비교
Array, ArrayList, LinkedList를 각 엘리먼트 갯수를 달리하여 테스트를 진행해보았다.
- [실습 코드](https://github.com/jeonyoungho/TIL/tree/master/Java/workspace/forloop%26foreach)
- 1)1,000개일 때
    - 결과: 세개의 타입 모두 for문과 forEach문에 따른 영향이 거의 없다.

- 2)10,000개일 때
    - 결과: Array는 for문과 forEach문에 따른 영향이 거의 없었고, ArrayList와 LinkedList는 forEach를 사용했을 때 더 빠르거나 비슷하다.


- 3)100,000개일 때
    - 결과: 세개의 타입 모두 forEach를 사용했을 때 확연히 더 빠르다.

#### 정리
<b>데이터의 양이 10,000개 이상이라면 순서 대로 순회하는 경우엔 forEach문을 사용하자! 만약 10,000개 미만이라면 상황에 따라 적절하게 판단해서 사용하자!</b>

#### 출처
- https://woowacourse.github.io/javable/post/2020-08-31-java-loop/