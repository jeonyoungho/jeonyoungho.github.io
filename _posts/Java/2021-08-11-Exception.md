---
title: "[개발자 블로그] Exception"
# post의 layout이 기본적으로 post으로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 된다.
date: 2021-08-11 +0800
categories: [Java] # categories는 최대 2개까지 가능
tags: [Java, Exception] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
---

개발하는데 있어 `예외 처리`는 없어서는 안되는 필수불가결한 요소이다. 그러기에 관련 내용을 한 번 정리하고 싶어졌다. 예외 처리와 관련된 다양한 포스팅들이 존재하지만 [여기](https://velog.io/@jsj3282/%EC%9E%90%EB%B0%94%EC%9D%98-%EC%98%88%EC%99%B8%EC%9D%98-%EC%A2%85%EB%A5%98-3%EA%B0%80%EC%A7%80) 블로그 작성자님께서 정리한 내용이 가장 명확하였고, 거의 그대로 정리해보고자 한다.

# Excetpion(예외)
Java에서 예외란 "우리가 예상한, 혹은 예상치도 못한 일이 발생하는 것을 미리 예견하고 안정장치를 하는 것"을 말한다.

Java에서는 세 종류의 예외가 존재한다.
- error
- checked exception
- runtime exception 혹은 unchecked exception

## 1) error (예외)
<b>에러는 Java프로그램 밖에서 발생한 예외를 말한다.</b>

가장 흔한 예가 서버의 디스크 고장이라던지, 메인보드에 문제가 생겨서 Java프로그램이 제대로 동작하지 못하는 경우가 있다.

### error와 exception의 차이
아래에서 설명하겠지만 Exception클래스는 에러가 아니다. Java프로그램에 오류가 발생했을 때, 오류의 이름이 Error로 끝나면 에러이고 Exception으로 끝나면 예외이다.

Error와 Exception으로 끝나는 오류의 가장 큰 차이는 프로그램 안에서 발생했는지, 밖에서 발생했는지의 여부이다. 하지만, 더 큰 차이는 프로그램이 멈추어 버리느냐 계속 실행할 수 잇느냐의 차이이다. <b>더 정확하게 말하면 Error는 프로세스에 영향을 주고, Exception은 쓰레드에만 영향을 준다.</b>

## 2) checked exception
반드시 명시적으로 처리해야 하기 때문에 Checked Exception이라고 하며, try catch를 해서 에러를 잡든 throws를 통해서 호출한 메서드로 예외를 던져야 한다.

## 3) runtime exception (런타임 예외)
<b>런타임 예외는 예외가 발생할 것을 미리 감지하지 못했을 때 발생한다.</b>

이 런타임 예외에 해당하는 모든 예외들은 RuntimeException을 확장한 예외들이다. 이 예외를 묶어주지 않는다고 해서 컴파일 할 때 예외가 발생하지 않는다. 하지만, 실행시에는 발생할 가능성이 있다. 그래서, 이러한 예외들을 런타임예외라고 한다. 컴파일시에 체크 하지 않기 때문에 unchecked exception이라고도 부르는 것이다.

# Throwalbe
![3](https://user-images.githubusercontent.com/44339530/129139071-92e06226-a444-418d-a321-347cd5055184.png)

위에서 설명한 예외들의 상속 관계를 그림이다. 여기서 Exception클래스를 바로 확장한 클래스들이 checked excecption이며, runtime exception밑에 있는 클래스들이 런타임 예외들이다.

Exception과 Error의 공통 부모 클래스는 당연히 Object클래스이다. 그리고 공통 부모 클래스가 또 하나 있는데 java.lang 패키지에 선언된 `Throwable 클래스`다. 다시 말해서 Exception과 Error클래스는 Throwable클래스를 상속받아 처리하도록 되어있다. 그래서 Exception과 Error를 처리할때 Throwable로 처리해도 문제는 없게 된다.

위처럼 Throwable클래스를 모두 공통으로 상속받는 이유는 Exception과 Error의 성격은 모두 다르지만, 모두 동일한 이름의 메소드를 사용하여 처리할 수 있도록 하기 위함이다. 그러면 가장 먼저 Throwable에 어떤 생성자가 선언되어 있는지 살펴보자.

- Throwable()
- Throwalbe(String message)
- Throwable(String message, Throwable cause)
- Throwable(Throwable cause)

아무런 매개 변수 없는 기본 생성자가 존재한다. 그리고 예외 메시지를 String으로 넘겨줄 수 있으며 예외 원인을 Throwable 객체로 넘겨 줄 수도 있다.

## Throwable의 대표 메소드
Throwable 클래스에 선언되어 있고, Exception 클래스에서 Overriding한 메소드는 10개가 넘는다. 그 중 가장 많이 사용되는 메소드는 다음과 같다.

1) getMessage()
예외 메시지를 String 형태로 제공받는다. 예외가 출력되었을 때 어떤 예외가 발생되었는지를 확인할 때 매우 유용하다. 즉, 그 메시지를 활용하여 별도의 예외 메시지를 사용자에게 보여주려고 할 때 좋다.

2) toString()
예외 메시지를 String 형태로 제공받는다. 그런데, getMessage() 메소드보다는 약간 더 자세하게, 예외 클래스 이름도 같이 제공한다.

3) printStackTrace()
가장 첫 줄에는 예외 메시지를 출력하고, 두 번째 줄부터는 예외가 발생하게 된 메소드들의 호출 관계(스택 트레이스)를 출력해준다.

### 예제
- 클라이언트 코드

~~~
public class ThrowableSample {

    public static void main(String[] args) {
        ThrowableSample sample = new ThrowableSample();
        sample.throwable();
    }

    public void throwable(){
        int[] intArray=new int[5];
        try{
            intArray=null;
            System.out.println(intArray[5]);
        }catch(Throwable t){
            System.out.println(t.getMessage());
            System.out.println(t.toString());
            t.printStackTrace();
        }
    }
}
~~~

- 실행 결과
![4](https://user-images.githubusercontent.com/44339530/129139531-2f4682ee-8673-4ed3-b6d7-68defa2c636c.png)

# throws와 throw
Java에서는 예외를 발생시킬 수 있다.

~~~
public class ThrowSample {

    public static void main(String[] args) {
        ThrowSample sample = new ThrowSample();
        sample.throwException(13);
    }

    public void throwException(int number) {
        try{
            if(number>12){
                throw new Exception("Number is over than 12");
            }
            System.out.println("Number is " + number);
        }catch(Exception e){
            e.printStackTrace();
        }
    }
}
~~~

1년 열두 달을 처리하는 로직이 필요하다고 하자. 메소드의 매개 변수로 13월이 넘어왔다면, 정상적인 처리가 불가능할 것이다. 이러한 상황에서 `try 블록 내에서 throw라고 명시한 후 개발자가 예외 클래스의 객체를 생성하면 된다`. 그러면 다른 예외가 발생한 상황과 동일하게 throw한 문장 이후에 있는 모든 try블록 내의 문장들은 수행이 되지 않고, catch블록으로 이동한다.

catch 블록 중에 throw한 예외와 동일하거나 상속 관계에 있는 예외가 있다면 그 블록에서 예외를 처리할 수 있다. 여기서는 e.printStackTrace() 메소드를 호출하기 때문에 예외 스택 정보가 출력될 것이다.

만약 해당하는 예외가 없다면 발생된 예외는 메소드 밖으로 던져버린다. `즉, 다시 말해서 예외가 발생한 메소드를 호출한 메소드로 던진다는 의미`이다. 이럴 때 사용하는 것이 `throws 구문`이다. throws 구문은 아래와 같이 메소드 선언할 때 사용하면 된다.

~~~
public void throwsException(int number) throws Exception {
        if(number>12){
            throw new Exception("Number is over than 12");
        }
        system.out.println("Number is " + number);
}
~~~

이렇게 메소드 선언에 해놓으면, 예외가 발생했을 때 try~catch로 묶어주지 않아도 그 메소드를 호출한 메소드로 예외 처리를 위임하는 것이기 때문에 전혀 문제가 되지 않는다.

이렇게 try~catch 블록으로 묶지 않고 예외를 throw한다고 할지라도, throws가 선언되어 잇기 때문에 전혀 문제 없이 컴파일 및 실행이 가능하다. <b>하지만 이렇게 throws로 메소드를 선언하면 개발이 어려워진다. 왜냐하면 이 throwsException() 이라는 메소드는 Exception을 던진다고 메소드 선언부에 throws 선언을 해놓았기 때문에 throwsException() 메소드를 호출한 메소드에서는 반드시 try~catch 블록으로 throwsException()메소드 호출 부분을 감싸주어야 한다.</b>

따라서 try~catch 블록으로 묶지 않으면 컴파일 에러가 발생할 것이다. 그러므로 이러한 throws 문장으로 인한 컴파일 오류가 생겼을 경우에는 두 가지 방법이 있다.

1) try ~ catch로 묵는 것
~~~
public static void main(String[] args) {
    ThrowSample sample = new ThrowSample();
    sample.throwException(13);
    try {
        sample.throwsException(13);
    }catch(Exception e){
        
    }
}
~~~

2) 호출한 메소드(여기서는 main()메소드)에서 다시 throws로 던지는 것
~~~
public static void main(String[] args) throws Exception {
    ThrowSample sample = new ThrowSample();
    sample.throwException(13);
    sample.throwsException(13);
}
~~~

이렇게 예외를 처리하면 컴파일하고 실행하는데 전혀 문제가 없다. <b>하지만 이미 throws 한 것을 다시 throws하는 방법은 그리 좋은 습관이 아니다. 가장 좋은 방법은 throws 하는 메소드를 호출하는 메소드에서 try~catch로 처리하는 것이다.</b> 이 클래스를 컴파일하고 실행하면 throwsException() 메소드를 호출할 경우 다음과 같이 예외 메시지가 나타난다.

![5](https://user-images.githubusercontent.com/44339530/129140316-6de5ea41-0015-448f-9d73-fdf153e1b8ee.png)

## throws와 throw 정리
1) 메소드를 선언할 떄 매개 변수 소괄호 뒤에 throws라는 예약어를 적어준 뒤 예외를 선언하면, 해당 메소드에서 선언한 예외가 발생했을 때 호출한 메소드로 예외가 전달된다. 만약 메소드에서 두 가지 이상의 예외를 던질 수 있다면, implements처럼 콤마(,)로 구분하여 예외 클래스 이름을 적어주면 된다.

2) try 블록 내에서 예외를 발생시킬 경우에는 throw라는 예약어를 적어준 뒤 예외 객체를 생성하거나, 생성되어 잇는 객체를 명시해준다. throw한 예외 클래스가 catch 블록에 선언되어 있지 않거나 throws 선언에 포함되어 있지 않으면 컴파일 에러가 발생한다.

3) catch 블록에서 예외를 throw할 경우에도 메소드 선언의 throws구문에 해당 예외가 정의되어 있다.

> **Note**: 예외를 throw하는 이유는 해당 메소드에서 예외를 처리하지 못하는 상황이거나, 미처 처리하지 못한 예외가 있을 경우에 대비하기 위함이다.

# Java의 예외 처리 전략
![3](https://user-images.githubusercontent.com/44339530/129139071-92e06226-a444-418d-a321-347cd5055184.png)

여기서 Exception을 바로 확장한 오른쪽에 있는 예외들이 Checked 예외이고, 왼쪽 하단에 Runtime Exception을 확장한 예외들이 런타임 예외(혹은 Unchecked 예외)이다.

예외를 직접 만들 때 Exception 클래스를 확장하여 나만의 예외 클래스를 만들었다. 그런데 이 예외가 항상 발생하지 않고 실행시에 발생할 확률이 매우 높은 경우에는 런타임 예외로 ㅁ나드는 것이 나을 수도 있다. 즉, 클래스 선언시 extends Exception 대신에 `extends RuntimeException`으로 선언하는 것이 낫다. 이렇게 되면 해당 예외를 던지는 메소드를 사용하더라도 try~catch로 묶지 않아도 컴파일시에 예외가 발생하지 않는다. 하지만 예이가 발생할 경우 해당 클래스를 호출하는 다른 클래스에서 예외를 처리하도록 구조적인 안전 장치가 되어 있어야만 한다. 여기서의 안전 장치는 try~catch로 묶지 않은 메소드를 호출하는 메소드에서 예외를 처리하는 try~catch가 되어 있는 것을 의미한다.

Runtime Exception이 발생하는 메소드가 있다면 그 메소드를 호출하는 메소드는 try~catch로 묶어지 않아도 컴파일할 때 문제가 발생하지 않는다. 하지만 예외가 발생할 확률은 높으므로 try~catch로 묶어주는 것이 좋다.

또한 개발 표준을 잡을 때 catch문 내에서 어떻게 처리할지를 명시적으로 선언해 두어야만 한다. 다시 말해서 catch문에서 로그를 남기는 등의 작업을 하고 예외를 throw를 사용해서 던져 주어야 문제가 발생한 정확한 원인을 알 수 있다.

## Java의 예외 처리 전략의 정리
1) 임의의 예외 클래스를 만들 때에는 반드시 try~catch로 묶어줄 필요가 잇을 때에만 Exception클래스를 확장한다. 일반적으로 실행시에 예외를 처리할 수 있을 경우에는 RuntimeException클래스를 확장하는 것을 권장한다.

2) catch문 내에 아무런 작업 없이 공백을 놔두면 예외 분석이 어려워지므로 꼭 로그 처리와 같은 예외 처리를 해줘야만 한다.

#### 출처
- [https://velog.io/@jsj3282/%EC%9E%90%EB%B0%94%EC%9D%98-%EC%98%88%EC%99%B8%EC%9D%98-%EC%A2%85%EB%A5%98-3%EA%B0%80%EC%A7%80](https://velog.io/@jsj3282/%EC%9E%90%EB%B0%94%EC%9D%98-%EC%98%88%EC%99%B8%EC%9D%98-%EC%A2%85%EB%A5%98-3%EA%B0%80%EC%A7%80)
- [https://cheese10yun.github.io/checked-exception/](https://cheese10yun.github.io/checked-exception/)