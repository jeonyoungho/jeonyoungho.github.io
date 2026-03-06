---
title: "자바 개발자를 위한 코틀린 입문(Java to Kotlin Starter Guide) 내용 정리"
date: 2026-02-02 +0800
categories: [Backend]
tags: [kotlin]
toc: true
comments: true
---

# 섹션2. 코틀린에서 변수와 타입, 연산자를 다루는 방법

## 1강 - 코틀린에서 변수를 다루는 방법

### 1) 변수 선언 키워드 - var과 val의 차이점
- var: 불변 타입 변수 선언
- val: 가변 타입 변수 선언
- 모든 변수에 수정 가능 여부(var/val)를 반드시 명시해줘야한다.
- 타입을 컴파일러가 자동으로 추론해주기 때문에 명시적으로 작성해줄 필요없다.(`ex. var number1 = 10L`)
- 변수를 만들고 초기화해주지 않는 경우 타입을 무조건 명시해줘야한다.(`ex. var number1: Long`)
- 초기화되지 않은 변수를 사용시 컴파일 에러 발생한다.
- val 컬렉션이 있더라도 요소를 add로 추가할 수 있다.
- **TIP) 모든 변수는 우선 val로 만들고 꼭 필요한 경우에만 var로 변경할 것**

### 2) kotlin 에서의 Primitive Type
- kotlin에서는 자바처럼 Primitive Type과 Reference Type이 나뉘어져있지 않고, Reference Type만 사용된다.(Long, Double, ...)
- **Reference Type(ex. Long) 타입 하나로 합쳐져있지만, 연산을 할 경우 코틀린이 알아서 내부적으로 Primitive Type으로 변경해서 알아서 처리를 해준다.**
- 즉, 프로그래머가 boxing / unboxing 을 고려하지 않아도 되도록 코틀린이 알아서 처리해준다.

### 3) Kotlin에서의 nullable 변수
- 코틀린은 기본적으로 모든 변수를 널이 들어갈 수 없게끔 설계해놓았다.
- **그러다보니 null 이 들어갈 수 있는 변수는 물음표(?)로 명시적으로 표현해줘야한다.**

```kotlin
var number3 = 1_000L
number3 = null // compile error

var number: Long? = 1_000L
number3 = null // ok
```

### 4) Kotlin에서의 객체 인스턴스화
- 자바에서처럼 new 키워드를 붙이지 않아야한다.

```kotlin
var person = Person("jeonyoungho")
```

## 2강 - 코틀린에서 null을 다루는 방법

### 1) 코틀린에서의 null 체크
- 함수 인자와 반환값에 ? 를 붙여서 Nullable 여부를 제어할 수 있다.
- 코틀린에서는 널이 가능한 탕비을 완전히 다르게 취급한다.
- **null이 가능한 타입만을 위한 기능(키워드)는 없을까? 있다! -> Safe Call**

### 2) Safe Call과 Elvis 연산자
- Safe Call(안전한 호출)은 null이 아닐때만 실행시키는 기능이다.
 
```kotlin
val str: String? = "ABC"
str.length // 불가능, 컴파일 에러
str?.length // 가능, str이 널이면 전체가 널이됨
```

- Elvis 연산자(?:)는 앞의 연산 결과가 null이면 뒤의 값을 사용하는 기능이다.
  - '?:'를 90도 회전하면 엘비스와 닮아서 엘비스 연산자라 불리게 됨

```kotlin
val str: String? = "ABC"
str?.length ?: 0 // str이 널이면 전체 결과가 널이되고, 엘비스 연산자로 인해 0이 된다
```

- Safe Call과 Elvis 연산자 활용 예시

```kotlin
fun startsWithA1(str: String?): Boolean {
  return str?.startsWith("A)
  ?: throw IllegalArgumentException("null이 들어왔습니다")
}

fun startsWithA2(str: String?): Boolean? {
  return str?.startsWith("A")

}

fun startsWithA3(str: String?): Boolean {
  return str?.startsWith("A") ?: false
}
```

- Elvis 연산은 early return에도 사용할 수 있다!

```java
public long calculate(Long number) {
  if (number == null) {
    return 0;
  }

  // ...
}

fun calculate(number: Long?): Long {
  number ?: return ;
  // ...
}
```

### 3) 널 아님 단언!!
- 아무리 생각해도 null이 될 수 없는 경우에 사용(!!)

```kotlin
fun startsWithA2(str: String?): Boolean {
  return str!!.startsWith("A") // 절대 널이 아님을 단언, 만약 널이 들어오게될 경우엔 런타임에서 NPE 발생(컴파일 타임엔 못잡음)
}
```

### 4) 플랫폼 타입
- 코틀린이란게 100프로 자바와 호환가능하다보니, 병행해서 쓰는 경우도 있다.
- 코틀린에서 자바코드를 가져다 사용할때 Null에 대한 어노테이션 정보를 코틀린이 이해한다.
  - org.jetbvrains.annotations 패키지, javax.annotation 패키지의 NotNull, Nullable
  - ex. 자바에서 Nullable 어노테이션이 getter 함수에 붙어잇으면 Nullable한 값으로 알아서 판단함
- 플랫폼 타입이란 코틀린이 null 관련 정보를 알 수 없는 타입으로 런타임시 예외가 발생할 수 있다.

## 3강 - 코틀린에서 Type을 다루는 방법

### 1) 기본 타입
- Byte, Shoort, Int, Long, Float, Double, 부호 없는 정수들
- 코틀린에서는 선언된 기본값을 보고 타입을 추론한다.

```kotlin
val numbwer1 = 3 // Int
val numbwer1 = 3L // Long
val numbwer1 = 3.0f // Float
val numbwer1 = 3.0 // Double
```

- **자바에서는 기본 타입간의 변환이 암시적으로 이뤄질 수 있지만, 코틀린은 명시적으로 이뤄져야 한다.**

```kotlin
== java ==
int nubmer1 = 4;
long nubme2 = number1;
System.out.println(number1 + number); // 자바에서는 더 큰 타입으로 암시적 변경(int 타입의 값이 long 타입으로)

== kotlin ==
val number1 = 4
val number2: Long = number1 // Type mismatch, Koitlin 에서는 암시적 타입 변경이 불가능
val number2: Long = number1.toLong() // to변환타입()을 사용해야함
println(number1 + number2)

== 널 처리 추가시 적절한 처리 필요 ==
val number1: Int? = 3
val number2: Long = number1?.toLong() ?: 0L
```

### 2) 타입 캐스팅
- 자바는 `instanceof`로 변수가 주어진 타입인지 확인, 코틀린은 `is` 키워드 사용
- 자바는 괄호로 타입캐스팅, 코틀린은 as 키워드 사용

```kotlin
fun printAgeIfPerson(obj: Any) {
  if (obj is Person) {
    val person = obj as Person // 생략 가능, 생략해도 Person 타입으로 간주 => 스마트 캐스트!!
    println(person.age)
  }

  if (obj !is Person) { // Person 타입이 아니라면
  }
}

== obj에 null이 들어올 수 있다면? ==
fun printAgeIfPerson(obj: Any?) {
  val person = obj as Person
  println(person.age) // obj가 널이면 NPE 발생
}

fun printAgeIfPerson(obj: Any?) {
  val person = obj as? Person // obj가 널이면 전체가 널이됨
  println(person?.age) // 널 반환
}
```

### 3) Kotlin의 3가지 특이한 타입
- Any
  - Java의 Object 역할(모든 객체의 최상위 타입)
  - 모든 Primitive Type의 최상위 타입도 Any
  - Any 자체로는 널을 포함할 수 없어 널이 포함하고 싶다면, Any?로 표현
  - Any에 equals / hashCode / toString 존재
- Unit
  - Java의 void 와 동일한 역할
  - (살짝 어려운 내용) Void와 다르게 unit은 그 자체로 타입 인자로 사용 가능
  - 함수형 프로그래밍에서 Unit은  단 하나의 인스턴스만 갖는 타입을 의미. 즉, 코틀린은 Unit은 실제 존재하는 타입이라는 것을 표현
- Nothing
  - 함수가 정상적으로 끝나지 않았다는 사실을 표현
  - 무조건 예외 반환하는 함수 / 무한 루프 함수 등
  - 실제 많이 사용되지는 않음

```kotlin
fun fail(message: String): Nothing {
  throw IllegalArgumentException(message)
}
```

### 4) String Interpolation, String indexing

```kotlin
val person = Person("jeon", 100)
println("이름: ${person.name}")

val name = "jeon"
println("이름: $name") // 중괄호 제거 가능
```

- **TIP) 변수 이름만 사용하더라도 ${변수} 를 사용하는 것이 가독성, 일괄 변환, 정규식 활용 측면에서 좋다.**
- 여러줄에 걸친 문자열을 작성해야할때 """ 큰따옴표 세개로 표현 가능

```kotlin
val name = "jeon"
"""
  ABC
  DEF
  ${name}
""".trimIndent()
println(str)
```

- 코틀린에서 문자열의 특정 문자 가져올땐 대괄호([])를 사용한다.

```kotlin
== java ==
str.chatAt(0);

== kotlin ==
var str = "ABC"
println(str[0])
println(str[2])
```

## 4강 - 코틀린에서 연산자를 다루는 방법

### 1) 단한 연산자(++, --) / 산술 연산자(+, -, *, /, %) / 산술대입 연산자(+=, -=, *=, /=, %=)
- 자바와 코틀린 완전 동일

### 2) 비교 연산자와 동등성, 동일성
- 비교 연산자(>, <, >=, <=)는 자바와 코틀린 완전 동일
  - 단, 코틀린은 자바와 다르게 객체 비교시 비교 연산자를 사용하면 자동으로 compareTo를 호출

```kotlin
val money1 = JavaMoney(2_000L)
val money2 = JavaMoney(1_000L)

if (money1 > money2) { // compareTo 자동 호출, 직접 compareTo 를 호출하지 않아도되서 더 직관적
}
```

- 동등성(Equality): 두 객체의 값이 같은가
  - 자바에서는 ==, 코틀린에서는 ===
- 동일성(Identity): 두 객체의 주소 값이 같은가
  - 자바에서는 equals 호출, 코틀린에서는 == 호출(코틀린에서 ==를 사용하면 간접적으로 equals를 호출)

```kotlin
== java ==
JavaMoney money1 = new JavaMoney(1_000L);
JavaMoney money2 = money1;
JavaMoney money3 = new JavaMoney(2_000L);
System.out.println(money1 == money2); // true
System.out.println(money1 == money3); // false
System.out.println(money1.equals(money3)); // true

== kotlin ==
val money1 = JavaMoney(1_000L)
val money2 = money1
val money3 = JavaMoney(1_000L)

println(money1 === money2) // true
println(money1 === money3) // false
println(money1 == money3) // true, 내부적으로 equals 호출
```

### 3) 논리 연산자 / 코틀린에 있는 특이한 연산자
- 논리 연산자(&&, ||, !)는 자바와 코틀린 완전 동일, 자바처럼 Lazy 연산을 수행한다.

```kotlin
== Lazy 연산 ==
fun main() {
  if (fun1() || fun2()) {
    println("fun1만 실행됨")
  }

  if (fun2() && fun1()) {
    println("fun2만 실행됨")
  }
}

fun fun1(): Boolean {
  println("fun 1")
  return true
}

fun fun2(): Boolean {
  println("fun 2")
  return false
}
```

- 코틀린에 있는 특이한 연산자
  - in / !in: 컬렉션이나 범위에 포함되어 있다, 포함되어 있지 않다.(ex. `println(1 in numbers)`)
  - a..b: a부터 b까지의 범위 객체를 생성한다.
  - a[i]: a에서 특정 인덱스 i로 값을 가져온다.
  - a[i] = b: a의 특정 인덱스 i에 b를 할당한다.

### 4) 연산자 오버로딩
- **코틀린에서는 객체마다 직접 플러스 연산, 마이너스 연산, 단항 연산, 비교 연산 등을 직접 정의할 수 있다.**

```kotlin
data class Money(
  val amount: Long
) {
  operator fun plus(ohter: Money): Money { // 연산자 직접 정의
    return Money(this.amount + other.amount)
  }
}

val money1 = Money(1_000L)
val money2 = Money(2_000L)

println(money1.plus(money2)) // Money(amount=3000L)
println(money1 + money2) // Money(amount=3000L)
```

# 섹션3. 코틀린에서 코드를 제어하는 방법

## 5강 - 코틀린에서 제어문을 다루는 방법

### 1) if문
- 자바와 코틀린 동일

### 2) Expression과 Statement
- 자바에서 if-else는 Statement이지만, 코틀린에서는 Expression이다.
  - Statement: 프로그램의 문장, 하나의 값으로 도출되지 않음
  - Expression: 하나의 값으로 도출되는 문장
  - Statement가 Expression을 포괄

```kotlin
== Java ==
ìnt score = 30 + 40; // 30 + 40은 70이라는 하나의 결과가 나오기에 Expression이면서 Statement

String grade = if (score >= 50) { // if 문을 하나의 값으로 취급하지 않으니 에러 발생 => Statement
  "P";
} else {
  "F";
}

String grade = score >= 50 ? "P" : "F"; // 3항 연산자는 하나의 값으로 취급하므로 에러가 없다! Expression이면서 Statement

== Kotlin ==
fun getPassOrFail(score: Int): String { // if-else와 if-elseif-else를 바로 계산해서 리턴할 수 있음, 따라서 3항 연산자가 필요하지 않기때문에 삭제되었다
    return if (score > 50) {
        "P"
    } else {
        "F"
    }
}

fun getPassOrFail(score: Int): String {
    return if (score > 50) {
        "P"
    } else if (score > 30) {
        "C"
    }
    else {
        "F"
    }
}
```

- 간단한 TIP: 어떠한 값이 특정 범위에 포함되어 있는지, 포함되어 있지 않은지

```kotlin
== Java ==
if (0 <= score && score <= 100) {}

== Kotlin ==
if (0 <= score && score <= 100) {} // ok

if (score in 0..100) {} // ok
```

### 3) switch와 when
- java의 swith는 코틀린의 when으로 사용 가능

```kotlin
== java ==
private String getGradeWithSwith(int score) {
  switch(score / 10) {
    case 9:
      return "A";
    case 8:
      return "B";
    default:
      return "D";
  }
}

== kotlin ==
fun getGradeWithSwith(score: Int): String {
  return when(score /10) { // when 역시 하나의 Expression이기 때문에 바로 리턴 가능 
    9 -> "A"
    8 -> "B"
    else -> "D"
  }
}

// 코틀린의 when 은 좀 더 다양한 활용 가능, 다양한 조건으로 분기 처리 가능
fun getGradeWithSwith(score: Int): String {
  return when(score) {
    in 90..99 -> "A"
    in 80..89 -> "B"
    else -> "D"
  }
}

when(값) {
  조건부 -> 어떠한 구문
  조건부 -> 어떠한 구문
  else -> 어떠한 구문
}

// 조건부에는 어떠한 expression 이라도 들어갈 수 있음(ex. is Type)
fun startsWithA(obj: Any): Boolean {
    return when (obj) {
        is String -> obj.startsWith("A")
        else -> false
    }
}

// 여러 조건을 동시에 검사 가능(, 로 구분)
fun judgeNumber(number: Int) {
    when (number) {
        1,0,-1 -> println("어디서 많이 본 숫자입니다)
        else -> println("1, 0, -1이 아닙니다")
    }
}

// when에 들어가는 값 자체가 없을수도 있음(얼리 리턴처럼 동작)
fun earlyReturn(number: Int) {
    when {
        number == 0 -> println("주어진 숫자는 0입니다")
        number % 2 == 0 -> println("주어진 숫자는 짝수입니다")
        else -> println("주어진 숫자는 홀수입니다")
    }
}
```

- 코틀린 when은 Enum Class 혹은 Sealed Class와 함께 사용할 경우, 더욱더 진가를 발휘한다.

## 6강 - 코틀린에서 반복문을 다루는 방법

### 1) for-each 문
- 콜론(:)대신 in 사용
- 자바와 동일하게 Iterable이 구현된 타입이라면 모두 들어갈 수 있다.

```kotlin
val numbers = listof(1L, 2L, 3L)

for (number in numbers) {
  println(number)
}
```

### 2) 전통적인 for문

```kotlin
for (i in 1..3) {
  println(i)
}

for (i in 3 downTo 1) { // 내려가는 경우
  println(i)
}

for (i in 1..5 step 2) { // 1부터 5까지 2씩 올라가는 경우
  println(i)
}
```

- .. 연산자: 범위를 만들어내는 연산자

### 3) Progression과 Range
- Range는 Progression(등차수열,진행의 의미)를 상속받는다.
- ..를 타고 들어가보면 
- `public operator fun rangeTo(other: Int): IntRange`
- 다음과 같이 IntRange를 반환하고, IntRange 클래스를 들어가보면
- `public class IntRange(start: Int, endInclusive: Int) : IntProgression(start, endInclusive, 1), ClosedRange<Int> {`
- IntProgression을 상속받고 있고 기본값으로 step값인 1을 받고있다.
- 그래서 1..5 이라는 의미는 1에서 5까지 1씩 상승하는 등차수열을 만들어달라는 의미가 된다.
- 3 downTo 1은 시작값 3, 끝값 1, 공차가 -1인 등차수열이 만들어지고
- 1..5 step 2는 시작값 1, 끝값 5, 공차가 2인 등차수열이 만들어진다. 
- downTo, step 둘 다 함수이다.(중위 함수)
  - 중위 함수(Infix Function)는 '변수.함수이름(argument)' 대신 '변수 함수이름 argument' 로 호출할 수 있도록 연산자처럼 사용하는 함수를 의미한다.
  - 즉, 피연산자 사이에 함수 이름을 위치시켜 호출할 수 있는 함수이다.
- 3 downTo 1 이라는 코드에서는 3이 downTo라는 함수를 호출했고 그 매개변수가 1인것이다.

```kotlin
public infix fun Int.downTo(to: Int): IntProgression {
```

- **즉, 코틀린에서 for문은 등차수열을 이용한다!**

### 4) while 문
- do-while문을 포함하여 자바와 코틀린 완전 동일

## 7강 - 코틀린에서 예외를 다루는 방법

### 1) try catch finally 구문

```kotlin
fun parseIntOrThrow(str: String): Int {
    try {
        return str.toInt()
    } catch (e: NumberFormatException) {
        throw IllegalArgumentException("주어진 ${str}는 숫자가 아닙니다")
    } finally {
        println("finish")
    }
}

fun parseIntOrThrow(str: String): Int {
    return try { // Expression 으로 간주되어 리턴을 한 번만 쓸 수 있음
        str.toInt()
    } catch (e: NumberFormatException) {
        throw IllegalArgumentException("주어진 ${str}는 숫자가 아닙니다")
    } finally {
        println("finish")
    }
}
```

### 2) Checked Exception과 Unchecked Exception

```kotlin
== 자바 ==
public void readFile() throws IOException {
  File currentFile = new File(".");
  File file = new File(currentFile.getAbsoluteFile() + "/a.txt");
  BufferedReader reader = new BufferedReader(new FileReader(file));
  System.out.println(reader.readLine());
  reader.close();
}

== 코틀린 ==
fun readFile() { // throws 구문이 없다. 코틀린에서는 Checked Exception과 Unchecked Exception 을 구분하지 않는다. 모두 Unchecked Exception이다.
    val currentFile = File(".")
    val file = File(currentFile.absolutePath + "/a.txt")
    val reader = BufferedReader(FileReader(file))
    println(reader.readLine())
    reader.close()
}
```

### 3) try with resources

```kotlin
== 자바 ==
public void readFile(String path) throws IOException {
  try (BufferedReader reader = new BufferedReader(new FileReader(path))) {
    System.out.println(reader.readLine());
  }
}

== 코틀린 ==
fun readfile(path: String) { // try-with-resources 구문 없음, use를 사용
  BufferedReader(FileReader(path)).use { bufferedReader ->
    println(bufferedReader.readLine())
  }
}
```

## 8강 - 코틀린에서 함수를 다루는 방법

### 1) 함수 선언 문법

```kotlin
fun max(a: Int, b: Int): Int { // 접근 제어자 public은 생략 가능
  if (a > b) {
    return a
  }
  return b
}

fun max(a: Int, b: Int): Int = // 함수가 하나의 결과값이면 block 대신 '=' 사용 가능
  if (a > b) {
    a
  } else {
    b
  }

fun max(a: Int, b: Int) = if (a > b) a else b // 함수를 쓸 때 중괄호 대신 = 을 썼기 때문에 반환타입 생략 가능
```

- block { } 을 사용하는 경우에는 반환 타입이 Unit이 아니라면, 명시적으로 작성해주어야 한다!
- 함수는 클래스 안에 있을 수도, 파일 최상단에 있을 수도 있습니다. 또한, 한 파일 안에 여러 함수들이 있을 수도 있습니다.

### 2) default parameter
- 자바에서는 함수의 매개변수에 기본값을 넣고싶을때 오버로딩을 이용하였다.

```java
public void repeat(String str, int number, boolean useNewLine) {
  for (int i = 1; i <= num; i++) {
    if (useNewLine) {
      System.out.println(str);
    } else {
      System.out.print(str);
    }
  }
}

public void repeat(String str, int num) {
  repeat(str, num, true);
}

public void repeat(String str) {
  repeat(str, 3, true);
}
```

- 코틀린에서는 아래와 같이 기본값(default paramter)을 지정할 수 있다.

```kotlin
fun main() {
    repeat("Hello World")
}

fun repeat(
  str: String,
  num: Int = 3,
  useNewLine: Boolean = true
) {
  for (i in 1..num) {
      if (useNewLine) {
          println(str)
      } else {
          print(str)
      }
  }
}
```

- 물론 코틀린에도 Java와 동일하게 오버로드 기능은 있다.

### 3) named argument(parameter)
- 만약 위 예제에서 num은 3을 쓸거고 useNewLine을 false로 하고싶을때는 아래와 같이 매개변수 이름을 이용하여 지정할 수 있다.
  - 호출자가 어떤 파라미터에 이 값을 넣을거야라고 지정하는 기능

```kotlin
fun main() {
    repeat("Hello World", useNewLine = false)
}
```

- **Named Argument로 인해 builder를 직접 만들지 않고 builder의 장점을 가지게 된다**

```kotlin
fun main() {
  printNameAndGender("FEMALE", "jeon")

  printNameAndGender(name = "jeon", gender = "MALE")
}

fun printNameAndGender(name: String, gender: String) {
  println(name)
  println(gender)
}
```

- **Kotlin에서 Java 함수를 가져다 사용할 때는 named argument를 사용할 수 없다.**
  - 자바코드가 byte 코드로 변환될때 파라미터 이름이 보존되지않아서 사용 불가능하다.

### 4) 같은 타입의 여러 파라미터 받기(가변인자)

```kotlin
== Java ==
public static void printAll(String... strings) {
  for (String str : strings) {
    System.out.println(str);
  }
}

== Kotlin ==
fun main() {
  printAll("A", "B", "C")
  val array = arrayOf("A", "B", "C")
  printAll(*array) // 배열을 가변인자에 넣어줄때는 * 를 붙여준다.
  // *는 스프레드 연산자로 배열을 풀어주는 기능
}

fun printAll(vararg strings: String) {
  for (str in strings) {
    println(str)
  }
}
```

# 섹션4. 코틀린에서의 OOP

## 9강 - 코틀린에서 클래스를 다루는 방법

### 1) 클래스와 프로퍼티

```
== Java ==
public class JavaPerson {

  private final String name;
  private int age;

  public JavaPerson(String name, int age) {
    this(name, age);
  }

  public String getName() {
    return name;
  }

  public int getAge() {
    return age;
  }

  public void setAge(int age) {
    this.age = age;
  }
}

== Kotlin ==
class Person constructor(name: String, age: Int){
  val name: String = name
  var age: Int = age
}
```

- 생성자로부터 주입되는 값을 이용하여 타입이 추론 되므로 타입 또한 생략 가능하다.

```kotlin
class Person constructor(name: String, age: Int){
  val name = name 
  var age = age
}
```

- Property(프로퍼티) => 필드 + Getter + Setter 
- 코틀린은 필드만 작성하면 프로퍼티의 나머지인 Getter, Setter를 자동으로 생성해준다.
- constructor 라는 keyword(지시어)는 생략 가능하다.


```kotlin
class Person (name: String, age: Int) {
    val name = name
    var age = age
}
```

- 코틀린에서는 생성자를 만들어줄때 프로퍼티로 선언과 초기화를 동시에 해줄 수 있다.

```kotlin
class Person (val name: String, var age: Int) {

}

class Person (val name: String, var age: Int) // body 가 없으므로 생략 가능
```

- '.필드'를 통해 getter와 setter를 바로 호출한다.(Java 클래스에 대해서도 동일)

```kotlin
fun main() {
  val person = Person("jeon", 100)
  println(person.name) // getter
  person.age = 10 // setter
  println(person.age) // getter

  val person = JavaPerson("jeon", 100)
  println(person.name) // getter
  person.age = 10 // setter
  println(person.age) // getter
}

class Person(
  val name: String,
  var age: Int
)
```

### 2) 생성자와 init
- 자바에서는 다음과 같이 생성자에서 검증을 하고 필드값을 초기화 해줄 수 있었다.

```java
public class JavaPerson {

  private final String name;
  private int age;

  public JavaPerson(String name, int age) {
    if (age <= 0) {
      throw new IllegalArgumentException(String.format("나이는 %s일 수 없습니다", age));
    }
    this.name = name;
    this.age = age;
  }
}
```

- 코틀린에서는 다음과 같이 init (초기화) 블록에 작성할 수 있다.
- init 블록은 생성자가 호출되는 시점에 호출된다.
- 값을 적절히 만들어주거나, validation 하는 로직이 들어감

```kotlin
class Person(
    val name: String,
    var age: Int
) {
  init { // 클래스가 초기화되는 시점에 한 번 호출되는 block (여기에 검증 로직 구현)
    if (age > 0) {
        throw IllegalArgumentException("나이는 ${age}이;ㄹ 수 없습니다")
    }
  }
}
```

**주 생성자(Primary Constructor)**
- Kotlin에서 **주 생성자**는 클래스 정의와 함께 선언되는 생성자이다.
- 반드시 존재해야 하며, 만약 파라미터가 없는 경우 생략 가능하다.
- 주 생성자는 클래스 이름 바로 뒤에 선언되며, **클래스 인스턴스를 생성할 때 반드시 호출**되는 기본 생성자입니다.

**주 생성자의 선언 방법**
- 주 생성자는 class 키워드 뒤에 선언되며, 클래스 이름과 함께 파라미터를 정의한다.
- 이 파라미터들은 클래스의 프로퍼티로 바로 사용할 수 있습니다.

```kotlin
class Person(val name: String, var age: Int)
 ```

**주 생성자의 특징:**
- 클래스 정의와 함께 선언된다.
- 자동으로 프로퍼티가 생성된다.(주 생성자의 파라미터가 자동으로 val 또는 var로 변환)
- init 블록을 통해 초기화 작업을 수행할 수 있다.
 
**부 생성자(Secondary Constructor)**
- Kotlin에서는 부 생성자도 정의할 수 있다.
- 있을수도 있고, 없을수도 있다.
- 부 생성자는 주 생성자 외에 추가로 선언되는 생성자로, 주 생성자에서 할 수 없는 다른 초기화 작업을 추가할 때 사용된다.
- 최종적으로 주생성자를 this로 호출해야 한다.

**부 생성자 선언 방법**
- 부 생성자는 constructor 키워드를 사용하여 선언하며, 주 생성자 없이 사용할 수 있다.(body를 가질 수 있기 때문에)
- 부 생성자는 여러 개를 정의할 수 있습니다.

```kotlin
class Person(
    val name: String,
    var age: Int
) {
  init {
      if (age > 0) {
          throw IllegalArgumentException("나이는 ${age}일 수 없습니다")
      }
  }

  constructor(name: String) : this(name, 1) // this를 이용하여 주 생성자를 호출

  // 두번째 부 생성자에서는 첫번째 부 생성자를 this(name,11)를 통해 부른다.
  // 첫번째 부 생성자가 주 생성자를 호출하므로 두번째 부 생성자도 결론적으로는 주 생성자를 호출하게 된다.
  // 본문은 역순으로 실행된다
  constructor(name: String, gender: String) : this(name,11) { 
      println("부생성자 2")
  }
}
```

- **그런데 사실.. 코틀린에서는 부생성자보다는 default parameter를 권장한다.**

```kotlin
class Person(
    val name: String = "jeon",
    var age: Int = 1
) {
    init {
        println("init!!!")
        if (age < 0) {
            throw IllegalArgumentException("나이는 ${age}일 수 없다.")
        }
    }
}
```

- **Converting과 같은 경우 부생성자를 사용할 수 있지만, 그보다는 정적 팩토리 메소드를 추천한다.**

```kotlin
class Person private constructor(
    val name: String,
    var age: Int
) {
    init {
        println("초기화 블럭 실행")
        if (age < 0) {
            throw IllegalArgumentException("no")
        }
    }

    companion object {
        // 정적 팩토리 메소드
        fun createWithDefaultValues(): Person {
            return Person("gil", 10)
        }

        fun createWithName(name: String): Person {
            return Person(name, 10)
        }
    }
}

fun main() {
    val person1 = Person.createWithDefaultValues()  // 기본값을 사용하는 생성
    val person2 = Person.createWithName("Alice")   // 이름만 지정하는 생성
}
```

- companion object는 Kotlin에서 클래스 내부에 정의할 수 있는 동반 객체를 의미한다.
- companion object는 클래스의 인스턴스 없이도 접근할 수 있는 정적 객체이다.
- 이를 사용하면 자바의 static 메소드처럼, 클래스 수준에서 동작하는 메소드나 속성들을 정의할 수 있다.

### 3) 커스텀 getter, setter

- 나이가 성인인지 확인하는 함수를 구현한다고 할때

```kotlin
== 자바 ==
public boolean isAdult() {
  return this.age >= 20;
}

== 코틀린 ==
class Person(
    val name: String = "gil",
    var age: Int = 10
) {
  init {
      println("초기화 블럭 실행")
      if (age < 0) {
          throw IllegalArgumentException("no")
      }
  }

  fun isAdult(): Boolean {
      return this.age >= 20
  }
}


class Person(
    val name: String = "gil",
    var age: Int = 10
) {
  init {
      println("초기화 블럭 실행")
      if (age < 0) {
          throw IllegalArgumentException("no")
      }
  }

  val isAdult: Boolean // 커스텀 getter: Person 클래스에 프로퍼티가 있는것처럼 보여주는 것
      get() = this.age>=20

  val isAdult: Boolean // 이것도 가능
    get() {
      return this.age>=20
    }
}
```

- 두 표현 방식은 프로퍼티처럼 접근하냐, 함수처럼 접근하냐의 차이가 있음
- 객체의 속성이라면 커스텀 getter, 그렇지 않다면 함수
  - 예를 들어, isAdult 는 이 사람이 성인인가라는 속성을 확인하는 것처럼 보이기에 커스텀 getter 사용

### 4) backing field
- 위에서 커스텀 getter를 사용하면 자기 자신을 변형해 줄 수도 있다!

```kotlin
class Person(
    name: String = "jeon",
    // 여기에 val을 붙여주면 코틀린이 알아서 name을 프로퍼티로 만들어준다.
    // 그러므로 val을 빼서 그냥 생성자의 매개변수로 취급되게 만들어준다.
    var age: Int = 1
) {
  val name = name // 생성자에서 받은 값을 필드에 할당
    get() = field.uppercase() // name으로 적으면 getter -> name -> getter -> name 무한 루프 발생하기 때문에 field 를 사용
    // field를 하면 진짜 name을 가르키는 것을 의미하게 됨
    // field는 무한루프를 막기 위한 예약어로 자기 자신을 가리킴
    // 자기 자신을 가리키는 보이지 않는 field다라고해서 backing field라고 부름   
}
```

- 하지만 커스텀 getter에서 backing field를 사용하는 경우는 드물다. 위 케이스는 아래와 같이도 구현 가능하다.

```kotlin
class Person(
    var name: String = "jeon",
    var age: Int = 1
) {
  // case1
  fun getUppercaseName(): String = this.name.uppercase()

  // case2
  val upperCaseName: String
    get() = this.name.uppercase()
}
```

- name을 set할때 대문자로 바꿔주는 케이스를 구현해보자.(커스텀 Setter)

```kotlin
class Person(
    name: String = "gil",
    var age: Int = 10
) {
  var name = name // setter 써야하니 var
    set(value) {
      field = value.uppercase() // 들어온 value 를 대문자로 바꿔서 실제 필드에 집어넣어준다라는 의미가 됨
    }
}
```

- 사실 Setter 자체를 지양하기 때문에 커스텀 Setter도 잘 안쓴다.
  - => Setter 보다는 update같은 함수를 만들어서 그안에서 값을 업데이트 시켜주는게 훨씬 더 깔끔한 코드를 작성할 수 있다보니, Setter 자체를 잘 안쓰게되고 커스텀 Setter도 잘 안쓰게 된다.

## 10강 - 코틀린에서 상속을 다루는 방법

### 1) 추상 클래스

```kotlin
abstracrt class Animal (
  protected val species: String,
  protected val legCount: Int,
) {
  abstract fun move()
}

class Cat(
  species: String // 콜론 사용, 타입은 콜론을 한 칸 뛰지않는것이 컨벤션
) : Animal(species, 4) { // 상속할때는 : 콜론을 한 칸 뛰는것이 컨벤션
// 괄호, 자식클래스의 주 생성자 매개변수를 통해 부모 클래스의 생성자를 바로 사용가능하다.
  override fun move() {
      println("고양이가 사뿐 사뿐 걸어가")
  }
}

class Penguin(
  private val wingCount: Int = 2,
  species: String
) : Animal(species, 2){

  override fun move() {
      println("펭귄이 움직인다")
  }

  // Animal의 legCount 에 open 키워드를 붙여줘야함
  // 코틀린에서는 프로퍼티를 오버라이드할때 추상 프로퍼티가 아니라면 open 꼭 키워드를 붙여줘야함
  override val legCount: Int 
      get() = super.legCount + this.wingCount

}
```

- 자바와 코틀린 모두 추상 클래스는 인스턴스화할 수 없다.

### 2) 인터페이스

```kotlin
interface Flyable {
  fun act() {
    println("fly~~)
  }

  fun fly()
}

interface Swimable {
  fun act() {
    println("swim~~) // default 키워드 없이 구현 가능
  }

  fun fly() // 추상 메서드
}

class Penguin(
  private val wingCount: Int = 2,
  species: String
) : Animal(species,2), Swimable,Flyable{ // 인터페이스 구현도 콜론 사용

  override fun move() {
      println("penguin move")
  }

  override val legCount: Int
      get() = super.legCount + this.wingCount

  override fun act() {
      super<Swimable>.act() // 상위 인터페이스 함수 호출
      super<Flyable>.act()
  }
}

interface Swimable2 {

    // 구현체에서 getter 를 구현하는걸 기대
    // 코틀린에서는 backing field가 없는 프로퍼티를 인터페이스에 만들 수 있다.
    val swimAbility: Int

    val swimAbility2: Int
      get() = 3

    fun act(){
        println("어푸")
    }
}

class Penguin(
    private val wingCount: Int = 2,
    species: String
) : Animal(species,2), Swimable2 {

    override fun move() {
        println("penguin move")
    }

    override val legCount: Int
        get() = super.legCount + this.wingCount

    override fun act() {
        super<Swimable>.act()
        super<Flyable>.act()
    }

    // getter 오버라이드
    override val swimAbility: Int
        get() = 3

}
```

### 3) 클래스를 상속할 때 주의할 점

```kotlin
open class Base( // 상속 가능하도록 open 설정
  open val number: Int = 100 // 해당 프로퍼티를 상속할 수 있도록 open 설정
){
  init {
      println("Base Class")
      println(number) 
      // 상위 클래스에서 하위 클래스가 override 하고 있는 프로퍼티를 생성자 block이나 init 블락에서 참조하게 되면 하위 클래스는 초기화전 상태이므로 초기값인 0이 출력된다.
      // 상위 클래스를 설계할 때 생성자 또는 초기화 블록에 사용되는 프로퍼티에는 open 을 꼭 피해야 한다.
  }
}

class Derived(
  override val number: Int
) : Base(number) {

  init {
      println("Derived Class")
  }
}

fun main() {
  Derived(500)
  // Base Class
  // 0
  // Derived class
}
```

### 4) 상속 관련 지시어 정리
**final**
- override를 할 수 없게 한다. default로 보이지 않게 존재한다.
- 여기서 final은 오버라이드 불가능을 뜻한다. 
- 자바에서 final은 불변을 의미하고 코틀린에서 불변을 의미하는건 val이다.
- 코틀린에서 모든 프로퍼티와 메서드는 암묵적으로 final이다. 즉 override가 기본적으로 막혀있다.

**open** 
- override를 열어 준다.
- 기본적으로 override가 막혀있으니 override를 열어주는 키워드는 open이다. 

**abstract**
- 반드시 override 해야 한다.

**override**
- 상위 타입을 오버라이드 하고 있다.

### 10강 정리
- 상속 또는 구현을 할 때에 : 을 사용해야 한다.
- 상위 클래스 상속을 구현할 때 생성자를 반드시 호출해야 한다.
- override를 필수로 붙여야 한다.
- 추상 멤버가 아니면 기본적으로 오버라이드가 불가능하다.
  - open을 사용해주어야 한다.
- 상위 클래스의 생성자 또는 초기화 블록에서 open 프로퍼티를 사용하면 얘기치 못한 버그가 생길 수 있다.

## 11강 - 코틀린에서 접근 제어를 다루는 방법

### 1) 자바와 코틀린의 가시성 제어

| 접근제어자 | Java 접근 범위 | Kotlin 접근 범위 |
|------------|----------------|------------------|
| public | 모든 곳에서 접근 가능 | 모든 곳에서 접근 가능 |
| protected | 같은 패키지 또는 하위 클래스에서만 접근 가능 | 선언된 클래스 또는 하위 클래스에서만 접근 가능 |
| default / internal | 같은 패키지에서만 접근 가능 | 같은 모듈에서만 접근 가능 |
| private | 선언된 클래스 내에서만 접근 가능 | 선언된 클래스 내에서만 접근 가능 |

- 코틀린은 패키지라는 개념을 접근 제어 단위로 취급하지 않음
- 코틀린에서의 모듈은 한 번에 컴파일 되는 Kotlin 코드
  - IDEA Module, Maven artifact, Gradle Module 등 컴파일 파일의 집합
  - 의존하는 외부 모듈에서 internal 키워드가 붙게되면 접근 불가능
- 자바의 기본 접근 지시어는 default, 코틀린은 public

### 2) 코틀린 파일의 접근 제어
- 코틀린은 .kt 파일에 변수, 함수, 클래스 여러 개를 바로 만들 수 있다.

```kotlin
package lannstark.lec01

val a = 3 // 변수도 가능

fun add(num1: Int, num2: Int): Int { // 함수도 가능
    return num1 + num2
}

class memo( // 클래스도 가능
    var text:String
)
```

- public: 기본값, 어디서든 접근 가능
- protected: 파일(최상단)에는 사용 불가능
- internal: 같은 모듈에서만 접근 가능
- private: 같은 파일 내에서만 접근 가능

### 3) 다양한 구성요소의 접근 제어

**클래스 안의 멤버/생성자**
- public: 기본값, 어디서든 접근 가능
- protected: 선언된 클래스 또는 하위 클래스에서만 접근 가능
- internal: 같은 모듈에서만 접근 가능
- private: 선언된 클래스내에서만 접근 가능

> 단, 생성자에 접근 제어자를 붙이려면 constructor 키워드를 붙여야한다.

```kotlin
class Bus internal constructor (
  val price: Int
)
```

**유틸 클래스**
- Java에서 Util 클래스 구현시 abstract + private constructor 를 사용해서 인스턴스화를 막았다.

```java
public abstract class StringUtils {
  private StringUtils() {}

  public boolean isDirectorPath(String path) {
    return path.endsWith("/);
  }
}
```

- 코틀린에서도 위와 비슷하게 가능하지만, 파일 최상단에 바로 유틸 함수를 작성하면 편하다.

```kotlin
== StringUtils.kt ==
// 자바로 디컴파일해보면 StringUtilsKt 라는 유틸성 클래스가 동일하게 구현된걸 확인할 수 있다.
// 자바 코드에서 StringUtilsKt.isDirectorPath 정적 메서드 형태로 사용 가능하다.
fun isDirectorPath(path: String): Boolean {
  return path.endsWith("/")
}
```

**프로퍼티**

```kotlin
class Car(
  internal name: String, // internal 키워드를 프로퍼티에 붙여 getter를 private 선언
  internal var owner: String, // internal 키워드를 프로퍼티에 붙여 getter, setter 전부 private 선언
  _price: Int
) {
  var price = _price // var price == public var price
    private set // setter 만 private 선언
}
```

### 4) Java와 Kotlin을 함께 사용할 경우 주의할 점
- Internal은 바이트 코드 상 public이 된다.
  - 그래서 Java 코드에서는 코틀린 모듈의 internal 코드를 가져 올 수 있다.
- Kotlin의 protected와 Java의 protected는 다르다.
  - Java는 같은 패키지의 Kotlin protected 멤버에 접근할 수 있다.

### 11강 정리
- Kotlin에서 패키지는 namespace 관리용이기 때문에 protected는 의미가 달라졌다.
- Kotlin에서는 default가 사라지고, 모듈간의 접근을 통제하는 internal이 새로 생겼다.
- 생성자에 접근 지시어를 붙일 때는 constructor를 명시적으로 써주어야 한다.
- 유틸성 함수를 만들 때는 파일 최상단을 이용하면 편리하다
- 프로퍼티의 custom setter에 접근 지시어를 붙일 수 있다.
- Java에서 Kotlin 코드를 사용할 때 internal과 protected는 주의해야 한다.


## 12강 - 코틀린에서 object 키워드를 다루는 방법
> object는 코틀린에서 신규 추가된 키워드이다

### 1) static 함수와 변수

```kotlin
== Java ==
public class JavaPerson {

  private static final int MIN_AGE = 1;

  public static JavaPerson newBaby(String name) {
    return new JavaPerson(name, MIN_AGE);
  }

  private String name;

  private int age;

  private JavaPerson(String name, int age) {
    this.name = name;
    this.age = age;
  }
}

== Kotlin ==
class Person private constructor(
  val name:String,
  val age:Int
){
  /*
  코틀린은 static 키워드가 없다.
  static 대신 companion object(동반 객체)를 이용한다.
  companion object 블록 안에 넣어둔 변수와 함수가 Java의 static 변수/함수인것처럼 사용된다.
  */
  companion object {
      // const 키워드를 붙이면 컴파일시에 0이라는 값이 할당됨
      // const 키워드가 없으면 런타임 시에 0이라는 값이 런타임에 할당됨
      // const 는 진짜 상수에 붙이기 위한 용도
      // 기본 타입과 String에 붙일 수 있음
      private const val MIN_AGE: Int = 1

      fun newBaby(name: String): Person {
          return Person(name,MIN_AGE)
      }
  }
}
```

**static**
- 클래스가 인스턴스화 될 때 새로운 값이 복제되는게 아니라 정적으로 인스턴스끼리의 값을 공유

**companion object**
- 클래스와 동행하는 유일한 오브젝트
- companion object(동반객체)도 하나의 객체로 간주된다. 때문에 이름을 붙일 수도 있고, interface를 구현할 수도 있다.
- companion object에 유틸성 함수들을 넣어도 되지만, 최상단 파일을 활용하는 것을 추천한다.

```kotlin
interface Log {
  fun log()
}

class Person private constructor(
    val name:String,
    val age:Int
){

    /*
    Factory라는 객체 이름을 붙일수도 있고
    Log와 같이 인터페이스를 구현할 수 있다.
     */
    companion object Factory :Log {
        private const val MIN_AGE: Int = 1

        fun newBaby(name: String): Person {
            return Person(name,MIN_AGE)

        }

        override fun log() {
            println("동행객체입니다~")
        }
    }

    fun add(factory: Factory) {
        println(factory.MIN_AGE) // 동행객체 사용가능
    }
}
```

- 만약 Java 코드에서 코틀린에 있는 static field나 static 함수를 사용한다면 아래와 같이 접근 가능하다.

```java
class Person private constructor(
    val name:String,
    val age:Int
){
  companion object {
      private const val MIN_AGE: Int = 1

      @JvmStatic
      fun newBaby(name: String): Person {
          return Person(name,MIN_AGE)
      }
  }
}

public class Prac {

  public static void main(String[] args) {
    // 동반객체에 이름이 없는 경우 Companion 이름을 통해 접근 가능
    // 이름이 명시되지 않았다면 Companion이라는 이름이 생략된 것
    Person.Companion.newBaby("jjaj"); 

    // 동반객체에 이름이 있는 경우 해당 이름으로만 접근 가능
    Person.Factor.newBaby("jjaj"); 

    // @JvmStatic 어노테이션을 코틀린쪽에 붙이면 바로 접근 가능
    Person.newBaby("jjaj");
  }
}
```

### 2) 싱글톤

```kotlin
== Java ==
public class SingletonJava {
  @Getter // 생성되는 getter 메서드는 필드의 static 여부를 따라간다.
  private static final SingletonJava INSTANCE = new SingletonJava();

  private SingletonJava() {}
}

class abc{
  public static void main(String[] args) {
      SingletonJava instance = SingletonJava.getINSTANCE();
  }
}

== Kotlin ==
// object 키워드만 붙이면됨
object Singleton{
  var a:Int = 0
}

fun main() {
  // 바로 접근하면 됨
  // 애당초 인스턴스가 하나이기에 싱글톤 클래스에 대해서는 인스턴스화를 하는게 아니라
  // 코드에서 바로 .a/.함수 이런식으로 접근하면됨
  // 직접적으로 싱글톤 객체를 만들일은 서버 개발시 많이 있진 않음
  println(Singleton.a) 
  Singleton.a += 10
  println(Singleton.a)
}
```

### 3) 익명 클래스
- 특정 인터페이스나 클래스를 상속받은 구현체를 일회성으로 사용할 때 쓰는 클래스

```kotlin
== Java ==
public interface Movable {
  void move();
  void fly();
}

public class Lec12Main {
  public static void main(String[] args) {
    moveSomething(new Movable() {
      @Override
      public void move() {
        System.out.println("move");
      }

      @Override
      public void fly() {
        System.out.println("fly");
      }
    });
  }

  public static void moveSomething(Movable movable) {
    movable.move();
    movable.fly();
  }
}

== Kotlin ==
fun main() {
  // 코틀린에서는 Movable을 상속받은 object를 만드는식으로 익명 클래스를 구현한다.
  moveSomething(object : Movable{
      override fun fly() {
          println("플라이")
      }

      override fun move() {
          println("무브")
      }
  })
}
```

### 12강 정리
- Java의 static 변수와 함수를 만드려면, Kotlin에서는 companion object를 사용해야 한다.
- companion object도 하나의 객체로 간주되기에 이름을 붙일 수 있고, 다른 타입(인터페이스, 추상클래스 등)을 상속받을 수 있다.
- Kotlin에서 싱글톤 클래스를 만들 때 object 키워드를 사용한다.
- Kotlin에서 익명 클래스를 만들 때 'object : 타입'을 사용한다.

## 13강 - 코틀린에서 중첩 클래스를 다루는 방법

### 1) 중첩 클래스의 종류
**자바에서의 중첩 클래스**
- Static을 사용하는 중첩 클래스: 클래스 안에 static을 붙인 클래스, 밖의 클래스 직접 참조 불가
- 내부 클래스(Innter Class): 클래스 안의 클래스, 밖의 클래스 직접 참조 가능

```kotlin
public class JavaHouse {

  private String address;
  private LivingRoom livingRoom;

  public JavaHouse(String address) {
    this.address = address;
    this.livingRoom = new LivingRoom(10);
  }

  public LivingRoom getLivingRoom() {
    return livingRoom;
  }

  public class LivingRoom {
    private double area;

    public LivingRoom(double area) {
      this.area = area;
    }

    public String getAddress() {
      return JavaHouse.this.address; // 밖의 클래스 직접 참조 가능
    }
  }
}
```

- 이펙티브자바 아이템24, 86 기준으로 중첩 클래스 선언시 static 클래스를 사용하라고 가이드한다.
  - 1)내부 클래스는 숨겨진 외부 클래스 정보를 가지고 있어, 참조를 해지하지 못하는 경우 메모리 누수가 생길 수 있다.
  - 2)내부 클래스 직렬화 형태가 명확하게 정의되지 않아 직렬화에 제한이 있다.
- **코틀린은 위 가이드를 충실히 준수한다.**

```kotlin
class JavaHouse(
  private val addresss: String,
  private val livingRoom: LivingRoom
) {
  // 그냥 클래스 만들듯이 편하게 작성하면됨
  // 코틀린에서는 기본적으로 바깥 클래스에 대한 연결이 없는 중첩 클래스가 만들어짐
  class LivingRoom(
    private val area: Double
  )
}
```

### 2) 코틀린의 중첩 클래스와 내부 클래스
**Java의 내부 클래스(권장되지 않은 클래스 안의 클래스, 바깥 클래스에 대한 참조를 가지는)**

```kotlin
class House(
    private val address: String,
    private val livingRoom: LivingRoom
) {
    // inner 키워드를 명시적으로 붙여줘야함
    inner class LivingRoom(
        private val area: Double
    ){
        val address:String
            get() = this@House.address // 바깥 클래스 접근 (this@바깥클래스)
    }
}
```

### 13강 정리
- 클래스 안에 클래스가 있는 경우는 두 가지였다.
  - (Java 기준) static을 사용하는 클래스
  - (Java 기준) static을 사용하지 않는 클래스
- 권장되는 클래스는 static을 사용하는 클래스이다.
- 코틀린에서는 이러한 가이드를 따르기 위해
  - 클래스 안에 기본 클래스를 사용하면 바깥 클래스에 대한 참조가 없고
  - 바깥 클래스를 참조하고 싶다면, inner 키워드를 붙여야 한다.
- 코틀린 inner class에서 바깥 클래스를 참조하려면 'this@바깥클래스'를 사용해야 한다.

| 구분 | 유형 | 바깥 클래스 참조 여부 |
|------|------|----------------------|
| Java | 클래스 안의 static 클래스 | 바깥 클래스 참조 없음 (권장되는 유형) |
| Java | 클래스 안의 클래스 | 바깥 클래스 참조 있음 |
| Kotlin | 클래스 안의 클래스 | 바깥 클래스 참조 없음 (권장되는 유형) |
| Kotlin | 클래스 안의 inner 클래스 | 바깥 클래스 참조 있음 |

## 14강 - 코틀린에서 다양한 클래스를 다루는 방법

### 1) Data Class

```kotlin
== Java ==
public class JavaPersonDto {

  private final String name;
  private final int age;

  public JavaPersonDto(String name, int age) {
    this.name = name;
    this.age = age;
  }

  public String getName() {
    return name;
  }

  public int getAge() {
    return age;
  }

  @Override
  public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;
    JavaPersonDto that = (JavaPersonDto) o;
    return age == that.age && Objects.equals(name, that.name);
  }

  @Override
  public int hashCode() {
    return Objects.hash(name, age);
  }

  @Override
  public String toString() {
    return "JavaPersonDto{" +
        "name='" + name + '\'' +
        ", age=" + age +
        '}';
  }
}

== Kotlin ==
// builder, equals, hashcode, toString 자동 구현됨
data class PersonDto(
  val name:String,
  var age:Int
)

fun main() {
  // named argument를 이용하면 builder pattern처럼 생성하는 것도 가능
  val personDto = PersonDto(
      name = "je",
      age = 30
  )
}
```

### 2) Enum Class

```kotlin
== Java ==
public enum JavaCountry {

  KOREA("KO"),
  AMERICA("US");

  private final String code;

  JavaCountry(String code) {
    this.code = code;
  }

  public String getCode() {
    return code;
  }
}

== Kotlin ==
enum class Country(
  private val code: String
) {
  KOREA("KO"),
  AMERICA("US");
}
```

**when 은 Enum Class 혹은 Sealed Class 와 함께할 경우 진가를 발휘한다.**

```kotlin
== Java ==
private static void handleCountry(JavaCountry country) {
  if (country == JavaCountry.KOREA) {
    // 로직 처리
  }

  if (country == JavaCountry.AMERICA) {
    // 로직 처리
  }
}

== Kotlin ==
// 새로운 열거형 코드 추가되거나 삭제됨을 컴파일타임에 잡을 수 있음 
fun handleCountry(country: Country) {
  when (country){
      Country.KOREA -> println("KOREA")
      Country.AMERICA -> println("AMERICA")
  }
}
```

### 3) Sealed Class, Sealed Interface
- Sealed는 봉인을한 이라는 뜻을 가지고 있다.
- 상속 가능한 클래스를 제어하기 위한 기능이다.
  - 컴파일 타임 때 하위 클래스의 타입을 모두 기억한다.
  - 즉, 런타임때 클래스 타입이 추가될 수 없다.
  - 하위 클래스는 같은 패키지에 있어야 한다.
- Enum과 다른 점은 다음과 같다.
  - 클래스를 상속 받을 수 있다.
  - 하위 클래스는 멀티 인스턴스가 가능하다.

```kotlin
sealed class HyundaiCar(
  val name: String,
  val price: Int,
)

class Avante : Hyundai("아반떼",1000)
class Sonata : Hyundai("소나타",1000)

// Enum 클래스와 동일하게 when 이랑 함께 사용할때 컴파일 시점에 안전한 분기처리가 가능함
// 추상화가 필요한 엔티티나 DTO에 활용
// JDK17에서도 Sealed Class가 추가되었음
private fun handleCar(car: HyundaiCar) {
  when(car) {
    is Avante -> TODO()
    is Sonata -> TODO()
  }
}
```
### 4) 14강 정리
- Kotlin의 Data Class를 사용하면 equals, hashCode, toString을 자동으로 만들어준다.
- Kotlin의 Enum Class는 Java의 Enum Class와 동일하지만, when과 함께 사용함으로써 큰 장점을 갖게 된다.
- Enum Class보다 유연하지만, 하위 클래스를 제한하는 Sealed Class 역시 when과 함께 주로 사용된다.

# 섹션5. 코틀린에서의 FP

## 15강 - 코틀린에서 배열과 컬렉션을 다루는 방법

### 1) 배열
- 엔터프라이즈 애플리케이션 잘 사용되지 않음
  - 이펙티즈 자바에서도 '배열보다 리스트를 사용하라'고 명시

```kotlin
== Java ==
int[] array = {100, 200};

for (int i = 0; i < array.length; i++) {
  System.out.printf("%s %s", i, i);
}

== Kotlin ==
fun main() {
  var array = arrayOf(100, 200)
  array = array.plus(300) // 새로운 요소를 추가후 반환된다.

  // 인덱스 활용(indices는 0부터 마지막 index까지의 Range)
  for (i in array.indices) {
      println("${i} ${array[i]}")
  }

  // 인덱스 + 값 활용
  for ((index, value) in array.withIndex()) {
      println("$index $valuoe")
  }
}

```

### 2) 코틀린에서의 Collection - List, Set, Map
**List**
- 컬렉션을 만들때 불변인지, 가변인지를 설정해야 한다.
  - 가변 컬렉션: 새로운 요소 추가 및 삭제 가능
  - 불변 컬렉션: 새로운 요소 추가 및 삭제 불가
- 불변 컬렉션이더라도 요소의 필드를 변경하는 것은 가능하다.
- **우선 불변 리스트를 만들고, 꼭 필요한 경우 가변 리스트로 바꾸는 방식을 활용하자.**

```kotlin
== Java ==
public class Main {
  public static void main(String[] args) {
    // 불변 리스트
    List<Integer> numbers = Arrays.asList(100, 200);

    // 하나 가져오기
    System.out.println(numbers.get(0));

    // For Each
    for (int number : numbers) {
      System.out.println(number);
    }

    // 전통적인 for문
    for (int i = 0; i < numbers.size(); i++) {
      System.out.printf("%s %s", i, numbers.get(i));
    }
}

== Kotlin ==
fun main() {
  // 불변 리스트 생성
  val numbers = listOf(100,200)

    // 가변 리스트
    val numbers2 = mutableListOf(100,200)
    // 자바에 있는 List 의 기능들이 코틀린에도 존재함
    mutableList.add(300)

  // 빈 리스트 생성시 타입을 명시적으로 지정해야함
  val numbers = emptyList<Int>()

  // 하지만 타입 추론이 가능하면 생략 가능
  printNumbers(emptyList())

  // 하나 가져오기
  numbers.get(0)
  numbers[0]

  // For-Each
  for (number in numbers) {
    println(number)
  }

  // 전통적인 for문
  for ((idx, value) in numbers.withIndex)) {
    println($index $number)
  }
}

private fun printNumbers(numbers: List<Int>) {

}
```

**Set**
- 기본적인 구현체는 LinkedHashSet

```kotlin
fun main() {
  val numbers = setOf(100,200)

  val numbers2 = mutableSetOf(100,200) 

  // For-Each
  for (number in numbers) {
    println(number)
  }

  // 전통적인 for문
  for ((idx, value) in numbers.withIndex()) {
    println($index $number)
  }
}
```

**Map**

```kotlin
== Java ==
public static void main(String[] args) {

    // JDK 8까지
    Map<Integer, String> map = new HashMap<>(); // 가변 Map
    map.put(1, "MON");
    map.put(2, "TUE");

    // JDK 9부터
    Map<Integer, String> map2 = Map.of(1, "MON", 2, "TUE"); // 불변 Map

    for (int key : map.keySet()) {
      System.out.println("key = " + key);
      System.out.println(map.get(key));
    }

    for (Map.Entry<Integer, String> entry : map2.entrySet()) {
      System.out.println(entry.getKey());
      System.out.println(entry.getValue());
    }
}

== Kotlin ==
fun main() {
  // 타입 추론할 수 없기에 타입 지정해줘야함
  val oldMap = mutableMapOf<Int, String>()
  oldMap.put(1, "MONDAY")

  // map[key] = value 도 가능
  oldMap[1] = "MONDAY"
  oldMap[2] = "TUESDAY"

  // to 라는 중위호출 사용 -> to 를 실행하면 Pair를 반환
  // 불변 map
  mapOf(1 to "MONDAY", 2 to "TUESDAY")


  for (key in oldMap.keys) {
    println(key)
    println(oldMap[key])
  }

  for ((key, value) in oldMap.entries) {
    println(key)
    println(value)
  }
}
```


### 3) 컬렉션의 null 가능성, Java와 함께 사용하기
- 컬렉션 정의시 ? 위치에 따라 null 가능성 의미가 달라지므로 잘 이해해야 한다.
  - `List<Int?>`: 리스트에 null이 들어갈 수 있지만, 리스트는 절대 null이 아님
  - `List<Int>?`: 리스트에는 null이 들어갈 수 없지만, 리스트는 null일 수 있음
  - `List<Int?>?`: 리스트에 null이 들어갈 수도 있고, 리스트가 null일 수도 있음
- Java는 읽기 전용 컬렉션과 변경 가능 컬렉션을 구분하지 않는다.
  - 코틀린의 불변 리스트를 자바에서 요소를 추가하고, 다시 코틀린으로 제어권이 넘어가면 오동작을 일으킬 수 있음
  - 자바의 non-null 리스트를 자바에서 null을 추가하고, 다시 코틀린으로 제어권이 넘어가면 오류가 날 수 있음
  - **즉, 코틀린 쪽의 컬렉션이 자바에서 호출되면 컬렉션 내용이 변할 수 있음을 감안해야 한다.(ex. 방어 로직)**
  - **또한, 코틀린쪽에서 Collections.unmodifiableXXX() 를 활용하면 변경 자체를 막을 수는 있다.**
- 코틀린에서 자바 컬렉션을 가져다 사용할때 '플랫폼 타입'을 신경써야 한다.
  - 자바의 `List<Integer>`를 코틀린에서 사용할때 `List<Int?>`, `List<Int>?`, `List<Int?>?` 셋 중에 뭔지를 알 수 없다.
  - **-> Java 코드를 보며, 맥락을 확인하고 Java 코드를 가져오는 지점을 wrapping한다.**

### 4) 15강 정리
- 배열의 사용법이 약간 다르다.
- 코틀린에서는 컬렉션 생성시 불변/가변을 지정해야 한다.
- List, Set, Map에 대한 사용법이 변경, 확장되었다.
- Java와 Kotlin 코드를 섞어 컬렉션을 사용할 때에는 주의해야 한다.
  - Java에서 Kotlin 컬렉션을 가져갈 때는 불변 컬렉션을 수정할 수도 있고, non-nullable 컬렉션에 null을 넣을 수도 있다.
  - Kotlin에서 Java 컬렉션을 가져갈 때는 '플랫폼 타입'을 주의해야 한다.


## 16강 - 코틀린에서 다양한 함수를 다루는 방법

### 1) 확장함수
- 탄생 배경
  - 코틀린은 자바와 100% 호환하는 것을 목표로 하고 있다.
  - 기존 자바 코드 위에 자연스럽게 코틀린 코드를 추가할 수는 없을까? 라는 고민을 시작함
  - 자바로 만들어진 라이브러리를 유지보수, 확장할 때 코틀린 코드를 덧붙이고 싶어하는 니즈가 생김
  - 그래서 어떤 클래스 안에 있는 메서드처럼 호출할 수 있찌만, 함수는 밖에 만들 수 있게하자하여 탄생됨
- **확장함수가 public이고, 확장함수에서 수신객체클래스의 private 함수를 가져오면 캡슐화가 깨지기 때문에 확장함수는 클래스에 있는 private 또는 protected 멤버를 가져올 수 없다.**

```kotlin
fun main() {
  val str = "ABC"
  println(str.lastChar()) // String 의 멤버함수처럼 사용
}

// String 클래스에 추가되는 문자열에 존재하는 가장 마지막 문자를 반환하는 확장함수
// fun 확장하려는클래스.함수이름(파라미터): 리턴타입 {}
// this를 통해 실제 클래스 안의 값에 접근
fun String.lastChar(): Char {
  return this[this.length - 1]
}
```

**확장함수,멤버 함수 같은이름일때**
- 이미 존재하는 함수명으로 확장함수를 만들면 멤버함수가 우선적으로 호출된다.
- **그러므로 확장함수를 만들었지만, 다른 기능의 똑같은 멤버함수가 생기면 오류가 생길 수 있다.**

```kotlin
== Java ==
public class Person {
  private String firstName;
  private String lastName;
  private int age;

  ...

  public int nextYearAge() {
    System.out.println("멤버 함수");
    return this.age + 1;
  }
}

== Koitlin ==
package com.lannstark.lec16

fun Person.nextYearAge() {
  println("확장 함수")
  return this.age + 1
}

fun main() {
  val person = Person("A", "B", 100)
  person.nextYearAge() // 자바 클래스내 멤버 함수가 출력됨
}
```

**확장함수 오버라이드**
- 해당 변수의 현재 타입. 즉, 정적인 타입에 의해 어떤 함수가 호출될지 결정된다.

```kotlin

open class Train(
  val name: String = "train"
  val price: Int = 5_000,
)

fun Train.isExpensive(): Boolean {
  println("Train의 확장함수)
  return this.price >= 10000
}

class Srt : Train("SRT", 40_000)

fun Srt.isExpensive(): Boolean {
  println("Srt의 확장함수)
  return this.price >= 10000
}

// Client 코드
val train: Train = Train()
train.isExpensive() // Train의 확장함수

val srt1: Train = Srt()
srt1.isExpensive() // Train의 확장함수

val srt2: Srt = Srt()
srt2.isExpensive() // Srt의 확장함수
```

**확장함수 정리**
- 확장함수는 원본 클래스의 private, protected 멤버 접근이 안된다.
- 멤버함수, 확장함수 중 멤버함수에 우선권이 있다.
- 확장함수는 현재 타입을 기준으로 호출된다.

> 자바에서는 코틀린에 존재하는 확장함수를 정적메서드를 부르는 것처럼 사용 가능하다.(ex. `StringUtilsKt.lastChar("ABC), 파일명+Kt.확장함수명();`)

**확장 프로퍼티**
- 클래스의 프로퍼티도 확장할 수 있다.
- 확장 프로퍼티의 원리는 확장함수 + custom getter와 동일하다.

```kotlin
// 확장 프로퍼티 선언
val String.lastChar: Char {
  get() = get(length - 1)
}

// 가변적인 확장 프로퍼티의 경우 var로 선언
var StringBuilder.lastChar: Char
  get() = get(length - 1)
  set(value: Char) {
  	this.setCharAt(length - 1, value)
  }
```

### 2) infix 함수(중위 함수)
- 함수를 호출하는 새로운 방법이다.
- downTo, step 도 함수이다.(중위 호출 함수)
- '변수.함수이름(arg)' 대신 '변수 함수이름 arg' 형태로 호출한다.

```kotlin
fun Int.add(other: Int): Int {
  return this+other
}

// infix 키워드는 멤버함수에도 붙일 수 있다.
infix fun Int.add2(other: Int): Int {
  return this + other
}

// 클라이언트 코드
3.add(4)
3.add2(4)
3 add2 4
```

### 3) inline 함수
- 함수가 호출되는 대신, 함수를 호출한 지점에 함수 본문을 그대로 복사하는 함수이다.
- 함수를 호출할때 발생하는 오버헤드를 줄이기 위해 사용된다.
- 하지만 inline 함수의 사용은 성능 측정과 함께 신중하게 사용되어야한다.

```kotlin
fun main() {
  3.add(4)
}

inline fun Int.add(other: Int): Int {
  return this+other
}

// 바이트 코드로 변환시
public static final void main() {
  byte $this$add$iv = 3;
  ...

  // 덧셈하는 로직 자체가 함수 호출 부분에 그대로 복붙
  int var10000 = $this$add$iv + other$iv;
}

```

### 4) 지역 함수
- 함수 안에 함수를 선언할 수 있다.

```kotlin
== Java ==
fun createPerson(firstName, lastName): Person {
  // 중복 코드
  if (firstName.isEmpty()) {
    throw IllegalArgumentException()
  }

  // 중복 코드
  if (lastName.isEmpty()) {
    throw IllegalArgumentException()
  }

  return Person(firstName, lastName, 1)
}

== 아래와 같이 변경 가능
fun createPerson(firstName, lastName): Person {
  // 함수 안의 함수를 생성하여 중복 코드 분리
  // 함수로 추출하면 좋을것 같은데, 지금 함수 내에ㅐ서만 사용하고 싶을때 사용
  // 하지만, depth가 깊어지기도 하고, 코드가 그렇게 깔끔하지는 않다. 잘 사용되진 않음
  // 차라리 Person 클래스내에서 검증해주는게 훨씬 나을것임
  fun validateName(name: String, filedName: String) {
    if (name.isEmpty()) {
      throw IllegalArgumentException("${fieldName}은 비어일 수 있을 수 없다. 값: ${value}")
    }
  }

  validateName(firstName, "firstName")
  validateName(lastName, "lastName")

  return Person(firstName, lastName, 1)
}
```

### 16강 정리
- Java 코드가 있는 상황에서, Kotlin 코드로 추가 기능 개발을 하기 위해 확장함수와 확장프로퍼티가 등장했다.

```kotlin
fun 확장하려는 클래스.함수이름(파라미터): 리턴타팁 {
  this를 이용해 실제 클래스 안의 값에 접근
}
```

- 확장함수는 원본 클래스의 private, protected 멤버 접근이 안된다.
- 멤버함수, 확장함수 중 멤버함수에 우선권이 있다.
- 확장함수는 현재 타입을 기준으로 호출된다.
- Java에서는 static 함수를 쓰는 것처럼 Kotlin의 확장함수를 쓸 수 있다.
- 함수 호출 방식을 바꾸는 infix 함수가 존재한다.
- 함수를 복붙하는 inline 함수가 존재한다.
- Kotlin에서는 함수 안에 함수를 선언할 수 있고, '지역함수'라고 부른다.

## 17강 - 코틀린에서 람다를 다루는 방법

### 1) Java에서 람다를 다루기 위한 노력
- Java에서의 람다 탄생 배경
- 자바에서는 '메서드 자체를 직접 넘겨주는 것처럼' 쓸 수 있다.
  - 바꿔 말하면, Java에서 함수는 변수에 할당되거나 파라미터로 전달할 수 없다.(2급시민)

### 2) 코틀린에서의 람다

- Java와는 근봊거으로 다른 한 가지가 있다. 코틀린에서는 함수가 그 자체로 값이 될 수 있다. 변수에 할당할수도, 파라미터로 넘길 수도 있다.

```kotlin
fun main() {
  val furits = listOf(
    Fruit("사과", 1_000),
    Fruit("사과", 1_200),
    Fruit("사과", 1_200),
    Fruit("사과", 1_500),
    Fruit("바나나", 3_000),
    Fruit("바나나", 3_200),
    Fruit("수박", 2_500),
  )

  // 람다 선언 방버 2가지
  val isApple: (Fruit) -> Boolean = fun(fruit::Fruit): Boolean { // 익명 함수
    return fruit.name == "사과"
  }
  val isApple2: (Fruit) -> Boolean = { fruit: Fruit -> fruit.name == "사과"}

  // 호출 방법 2가지
  isApple(fruits[0]) // 일반 함수 호출
  isApple.invoke(fruits[0]) // invoke 함수 호출


  filterFruits(fruits, isApple)

  filterFruits(fruits, fun(fruit::Fruit): Boolean { // 익명 함수
    return fruit.name == "사과"
  })

  // 아래와 같이 소괄호 밖으로 중괄호로 빼면 마지막 파라미터로 들어가게됨
  filterFruits(fruits) { fruit -> fruit.name == "사과"}
  filterFruits(fruits) { it.name == "사과"} // 파라미터가 한 개일때에는 it 이 Fruit가 됨

  filterFruits(fruits) { fruit ->
    println("only apple")
    fruit.name == "사과" // 리턴을 작성하지 않아도 마지막 줄의 결과가 람다의 반환값
  }
}

private fun filterFruites(fruits: List<Fruit>, filter: (Fruit) -> Boolen): List<Fruit> {
  val results = mutableListOf(Fruit)()
  for (fruit in fruits) {
    if (filter(fruit)) {
      results.add(fruit)
    }
  }
  return results
}
```

### 3) Closure

```kotlin
== Java ==
String targetFruitName = "바나나"
targetFruitName = "수박"
// 아래 함수 호출문의 targetFruitname 변수에 다음과 오류가 발생함
// Variable used in lambda expression should be final or effectively final
// Java에서는 람다를 쓸 때 실질적으로 final 인 변수만 가능한 제약이 존재한다.
filterFruits(fruits, (fruit) -> targetFruitname.equals(fruit.getName()));

== Kotlin ==
var targetFruitName = "바나나"
targetFruitName = "수박"
// 코틀린에서는 아무런 문제 없이 동작한다!
// 코틀린에서는 람다가 시작하는 지점에 람다식 안에서 참조하고 있는 변수들을 모두 포획하여 그 정보를 가지고 있다.
// 이렇게 해야만, 람다를 진정한 일급 시민으로 간주할 수 있다. 이 데이터 구조를 Closure라고 부른다.
// 즉, 람다가 실행되는 시점에 쓰고 있는 변수들을 모두 포획한 데이터 구조를 Closure라고 한다.
filterFruits(fruits) { it.name == targetFruitName}
```

### 4) 다시 try with resources

```kotlin
fun readFile(path: String) {
  BufferedReader(FileReader(path)).use { 
      reader -> println(reader.readLine())
  }
}
```

- use 함수(`public inline fun <T : Closeable?, R> T.use(block: (T) -> R): R {}`)에 대해 살펴보면 다음과 같다.
  - Closeable 구현체에 대한 확장함수이다.
  - inline 함수이다.
  - 람다를 받게 만들어진 함수이다.
  - 실제 람다를 전달하고 있다.(`(T) -> R`)

### 17강 정리
- 함수는 Java에서 2급시민이지만, 코틀린에서는 '1급시민'이다.
  - 때문에, 함수 자체를 변수에 넣을 수도 있고 파라미터로 전달할 수도  있다.
- 코틀린에서 함수 타입은 '(파라미터 타입, ...) -> 반환타입' 이었다.
- 코틀린에서 람다는 두 가지 방법으로 만들수도 있고, 중괄호({}) 방법이 더 많이 사용된다.
  
```kotlin
// 람다 만드는 방법1
val isApple = fun(fruit::Fruit): Boolean {
  return fruit.name == "사과"
}

// 람다 만드는 방법2
val isApple2 = { fruit: Fruit -> fruit.name == "사과"}
```

- 함수를 호춣하며, 마지막 파라미터인 람다를 쓸 때는 소괄호 밖으로 람다를 뺄 수 있다. (아래 코드에서 전자의 방법을 추천, 왜냐하면 it을 쓰면 함수를 부르는쪽만 봐선 어떤 데이터인지 모르기에)

```kotlin
filterFruits(fruites) { fruit -> fruit.name == "사과"}

filterFruits(fruites) { it.name == "사과"}
```

- 람다의 마지막 expression 결과는 람다의 반환 값이다.

```kotlin
filterFruits(fruites) { fruit -> {
  println("사과만 받는다..!!")
  fruit.name == "사과"
}}
```

- 코틀린에서는 Closure를 사용하여 non-final 변수도 람다에서 사용할 수 있다.


## 18강 - 코틀린에서 컬렉션을 함수형으로 다루는 방법

### 1) 필터와 맵

**filter / filterIndexed / map / mapIndexed / mapNotNull**

```kotlin
data class Furit(
  val id: Long,
  val name: String,
  val factoryPrice: Long,
  val currentPrice: Long,
)

// 람다를 전달하여 필터
val apples = fruits.filter { fruit -> fruit.name == "사과" }

// 인덱스 또한 받아서 처리 가능
val apples2 = fruits.filterIndexed { index, fruit ->
    println(index)
    fruit.name == "사과"
}

// 자바에서 map의 반환은 Stream 이지만 코틀린에서는 List를 반환한다.
// 그래서 map 이후 .collect(Collectors.toList())를 안해도 된다.
val applePrices = fruits.filter{fruit -> fruit.name=="사과"}
    .map { fruit -> fruit.price }

// Map 에서 인덱스가 필요한 경우
 applePrices = fruits.filter { fruit -> fruit.name == "사과" }
  .mapIndexed { idx, fruit -> 
    println(idx)
    fruit.currentPrice
  }

// Mapping의 결과가 null이 아닌 것만 가져오고 싶다면?
val values = fruits.filter { fruit -> fruit.name == "사과" }
  .mapNotNull { fruit -> fruit.nullOrValue()}
```

**필터 적용 예시**

```kotlin
// as-is
private fun filterFruits(
    fruits: List<Fruit>,
    funcName: (Fruit) -> Boolean
): List<Fruit> {
  val results = mutableListOf<Fruit>()
  
  for (fruit in fruits) {
      if (funcName(fruit)) {
          results.add(fruit)
      }
  }    
  return results
}

// to-be
private fun filterFruits(
    fruits: List<Fruit>,
    filter: (Fruit) -> Boolean
): List<Fruit> {

  return fruits
      .filter(filter)
}
```

### 2) 다양한 컬렉션 처리 기능

**all / none / any /count / sortedBy / sortedByDescending / distinctBy / first / firOrNull / last / lastOrNull**

```kotlin
// all: 조건을 모두 만족하면 true, 그렇지 않으면 false
val isAllApple = fruits.all {fruit -> fruit.name == "사과"}

// none: 조건을 모두 불만족하면 true, 그렇지 않으면 false
val isAllApple = fruits.none {fruit -> fruit.name == "사과"}

// any : 조건을 하나라도 만족하면 true, 그렇지 않으면 false
val isAllApple = fruits.any {fruit -> fruit.name == "사과"}

// count : 개수를 센다 (Java List.size()와 동일)
val fruitCount = fruits.count();

// sortedBy : (오름차순) 정렬을 한다.
val sorted = fruits.sortedBy { fruit -> fruit.price }

// sortedByDescending: (내림차순) 정렬을 한다.
val sortedDesc = fruits.sortedByDescending { fruit -> fruit.price }

// distinctBy : 변형된 값을 기준으로 중복을 제거한다.
val distinct = fruits.distinctBy { fruit -> fruit.name }

// first : 첫번째 값 가져오기(무조건 null이 아니어야한다. null이라면 exception 발생)
val first = fruits.first()

// firstOrNull : 첫번째 값 또는 null을 가져온다
val firstNullable = fruits.firstOrNull()

// last : 마지막 값 가져오기(무조건 null이 아니어야한다. null이라면 exception 발생)
val last = fruits.last()

// lastOrNull : 마지막 값 또는 null을 가져온다.
val lastNullable = fruits.lastOrNull()
```

### 3) List를 map으로
**groupBy / associateBy**

```kotlin
// groupBy : List -> Map, 반환되는 값을 키값으로 하는 Map 반환
val fruitMap: Map<String, List<Fruit>> = fruits.groupBy { fruit -> fruit.name }

// associateBy : List 가 아닌 단일 객체가 들어가게됨, 중복되지 않은키(ID)를 통해 Map을 생성할때
val fruitMap2: Map<Long, Fruit> = fruits.associateBy { fruit -> fruit.name }

// Key 말고 Value에 대한 변환 할 수 있다.
val fruitMap3: Map<String, List<Long>> = fruits.groupBy ({fruit -> fruit.name},{ fruit -> fruit.factoryPrice })


// Key, Valoue 둘 다 변환할 수 있다.(id -> 출고가 Map 생성)
// 람다를 하나 쓸 때와 다른점은 두번째는 중괄호를 소괄호 밖으로 빼줄 수 있지만, 중괄호가 두 개 이상인 경우에는 같이 넣어주는게 컨벤션
val fruitMap4: Map<Long, Long> = fruits.associateBy({ fruit -> fruit.id }), { fruit -> fruit.factoryPrice }

// Map에 대해서도 앞선 List의 기능들을 대부분 사용 가능(filter, map 둥)
val fruitMap5: Map<String, List<Fruit>> = fruits.groupBy { fruit -> fruit.name }.filter( (key, value) -> key == "사과")
```

### 4) 중첩된 컬렉션 처리

**flatMap / flatten**

```kotlin
val fruitsInList: List<List<Fruit>> = listOf(
    listOf(
        Fruit("사과", 1000),
        Fruit("사과", 1200),
        Fruit("사과", 1200),
        Fruit("사과", 1500),
        Fruit("바나나", 2500),
        Fruit("수박", 10000)
    ),
    listOf(
        Fruit("사과", 1000),
        Fruit("사과", 1200),
        Fruit("사과", 1200),
        Fruit("바나나", 3000)
    ),
    listOf(
        Fruit("사과", 1000),
        Fruit("사과", 1200),
        Fruit("수박", 10000)
    )
)

// 자바와 동일하게 flatMap 사용 가능
val samePriceFruits = fruitsInList.flatMap { list->
  list.filter { fruit -> fruit.name =="사과" }
}

// 아래와 같이 리팩토링 가능하다.
data class Furit(
  ...
) {
  val isSampePrice: Boolean
    get() = factoryPrice == currentPrice
}

// 확장함수 정의
val List<Fruit>.samePriceFilter: List<Fruit>
  get() = this.filter(Fruit::isSamePrice)

// 간결하게 개선
val samePriceFruits = fruitsInList.flatMap { list -> list.samePriceFilter }
```

```koitlin
// flatten : List<List<Fruit>> -> List<Fruit> 변환(중첩 해제되어 반환)
fruitsInList.flatten()
```

### 5) 18강 정리
- List: filter / filterIndexed / map / mapIndexed / mapNotNull / all / none / any /count / sortedBy / sortedByDescending / distinct / first / firstOrNull / last / lastOrNull 
- Map: groupBy / associateBy
- 중첩된 컬렉션: flatMap / flatten


# 섹션6. 추가적으로 알아두어야 할 코틀린 특성

## 19강 - 코틀린의 이모저모

### 1) Type Alias와 as import
- 긴 이름의 클래스 혹은 함수 타입이 있을때 축약하거나 더 좋은 이름을 쓰고 싶을때 사용하는 기능이다.

```kotlin
private fun filterFruits(
  fruits: List<Fruit>,
  funcName: (Fruit) -> Boolean // 타입이 너무 길다, 파라미터가 더 많아진다면?
): List<Fruit> {
  ...
}

// 아래와 같이 별칭 설정
typealias FruitFilter = (Fruit) -> Boolean

private fun filterFruits2(
  fruits: List<Fruit>,
  funcName: FruitFilter // 별칭으로 대체
): List<Fruit> {

  return fruits
      .filter(funcName)
}

data class UltraSuperGuardianTribe(
  val name:String
)

// 이름 긴 클래스를 컬렉션 사용할 때도 간단히 줄일 수 있다.
typealias USGTMap = Map<String, UltraSuperGuardianTribe>

fun USGTMapOut(): USGTMap? {
  return null
}

```

**다른 패키지의 같은 이름 함수를 동시에 가져오고 싶다면?**
- as import: 어떤 클래스나 함수를 임포트 할 때 이름을 바꾸는 기능

```kotlin
// 다른 패키지의 동일한 함수를 가져온다면?
package lannstark.lec19.a

fun printHelloWorld() {
    println("hello wolrd A")
}

package lannstark.lec19.b

fun printHelloWorld() {
    println("hello wolrd B")
}

// 아래와 같이 as import 사용하여 이름 변경 가능
import lannstark.lec19.a.printHelloWorld as printHelloWolrdA
import lannstark.lec19.b.printHelloWorld as printHelloWolrdB

fun main() {
  printHelloWorldA()
  printHelloWorldB()
}
```

### 2) 구조분해와 componentN 함수
- 구조분해: 복합적인 값을 분해하여 여러 변수를 한 번에 초기화하는 것

```kotlin
data class Person(
    val name:String,
    val age:Int
)

val person = Person("jeon", 100)

val name2 = person.component1() // 첫번째 프로퍼티를 가져오는거(componentN 함수)
val age2 = person.component2() // 두번째 프로퍼티를 가져오는거(componentN 함수)
val (name, age) = person // javascript의 구조분해 기능과 동일, 구조분해 문법을 쓴다는것은 componentN 함수를 호출한다는 것, 단순히 첫번째 프로퍼티를 첫번째 변수에 넣어주고 두번쨰 프로퍼티를 두번째 변수에 너어주는 것임(순서 바꾸면 안됨)
```

- **Data class는 componentN이란 함수도 자동으로 만들어준다.**
- componentN은 각 프로퍼티를 반환하는 함수이다.
- Data Class가 아닌데 구조분해를 사용하고 싶다면, 직접 componentN 함수를 구현 가능하다.

```kotlin
class Person(
    val name:String,
    val age:Int
) {
  // 연산자의 속성을 가지고 있기 때문에, 연산자 오버로딩을 하는 것처럼 간주되야하므로 operator 키워드가 붙어야함
  operator fun component1(): String {
    return this.name
  }

  operator fun component2(): String {
    return this.age
  }
}

val person = Person("jeon", 100)
val (name, age) = person
```

- 아래 map.entries 와 list.withIndex 를 쓸때도 역시 구조분해를 통해 처리된다.

```kotlin
val map = mapOf(1 to "A",2 to "B")
for ((key, value) in map.entries) {
    
}
```

### 3) Jump와 Label
**return / break / continue**

- 우선 코드의 흐름을 정의할때 사용한다.
- return: 기본적으로 가장 가까운 enclosing function 또는 익명함수로 값이 반환된다.
- break : 가장 가까운 루프가 제거된다.
- continue : 가장 가까운 루프를 다음 step으로 보낸다.
- **코틀린에서 for문 및 while 문에서 break, continue 기능은 동일하다.**
- **단!!! 자바와 코틀린 둘 다 forEach에서 continue, break를 사용할 수 없다.**

```kotlin
val numberList = listOf(1, 2, 3)
  numberList.map { number -> number + 1 }
    .forEach {number ->
        println(number)
//            break, continue 쓸 수 없다.
    }
```

- 만약 코틀린을 이용해서 forEach에서 continue, break를 사용하려면 아래와 같이 run 블록을 감싸면 된다.
- **하지만 break, continue를 사용할 때엔 가급적 익숙한 for문 사용하자.**

```kotlin
run {
  numberList.forEach { number ->
      if (number == 3) {
          return@run // break 기능, run을 가리켜서 리턴시킴
      }
      if (number == 4) {
          return@forEach // continue 기능, run을 가리켜서 해당 부분 한 번만 건너뛰게됨
      }
      println(number)
  }
}
```

- 코틀린에는 **라벨**이라는 기능이 있다.
- 특정 expression에 **라벨이름@** 을 붙여 하나의 라벨로 간주하고 break, continue, return 등을 사용하는 기능이다.
- **라벨을 사용한 Jump는 사용하지 않는 것을 강력 추천한다.**
  - -> 코드의 흐름이 위아래로 계속 움직일수록 복잡도가 드라마틱하게 증가하고, 유지보수가 어려워지기 때문이다.

```kotlin
abc@for (i in 1..100) {
    for (j in 100) {
        for (k in 50..500) {
            if (j == 2) {
                break@abc; // 라벨이 지정된 부분(반복문, abc)을 탈출하게 된다. 
            }
            print("${i} ${j}")
        }
    }
}
```

### 4) TakeIf와 TakeUnless

**takeIf**

```kotlin
fun getNumeberOrNull(number: Int): Int? {
  return if (number <= 0) {
    null
  } else {
    number
  }
}

// Kotlin에서는 method chaning을 위한 특이한 함수를 제공한다.
// 위 코드는 taekIf 를 통해 한 줄로 변경할 수 있다.
fun getNumeberOrNull(number: Int): Int? {
  // takeIf : 주어진 조건을 만족하면 그 값이, 그렇지 않으면 null이 반환된다.
  return number.takeIf { it > 0 }
}
```

**TakeUnless**

```kotlin
fun getNumeberOrNull(number: Int): Int? {
  // TakeUnless : 주어진 조건을 만족하지 않으면 그 값이, 그렇지 않으면 null이 반환된다.
  return number.takeUnless { it <= 0 }
}
```

### 5) 19강 정리
- 절대 외울 필요없다. 나중에 필요할떄 찾아서 적용하면 된다.
- 타입에 대한 별칭을 줄 수 있는 **typealias** 라는 키워드가 존재한다.
- import 당시 이름을 바꿀 수 있는 **as import** 기능이 존재한다.
- 변수를 한 번에 선언할 수 있는 **구조분해 기능이 있으며 componentN 함수를 사용**한다.
- for문, while문과 달리 forEach에는 break와 continue를 사용할 수 없다.
- **takeIf**와 **takeUnless**를 활용해 코드양을 줄이고 method chaning을 활용할 수 있다.


## 20강 - 코틀린의 scope function

### 1) scope function이란 무엇인가?
- scope: 영역, function: 함수
- **scope function**: 일시적인 영역을 형성하는 함수(takeIf, takeUnless)

```kotlin
// as-is
fun printPerson(person: Person?) {
    if (person != null) {
        println(person.name)
        println(person.age)
    }
}

// to-be (refactor)
fun printPerson(person: Person?) {
  // Safe Call (?.)을 사용 : person이 null이 아닐때 let 호출
  // let : scope function의 한 종류(일시적인 영역 생성)
  person?.let { // 람다를 사용하고 있다. 람다 안에서 it을 통해 person에 접근
    println(it.name)
    println(it.age)
  }
}

// let 함수는 람다를 받아, 람다 결과를 반환한다.
// T라는 함수를 받아서 R이라는 실행 결과를 반환한다.
public inline fun <T, R> T.let(block: (T) -> R): R {
  return block(this)
}
```

- **람다**를 사용해 일싲거인 영역을 만들고 **코드를 더 간결**하게 만들거나, **method chaining**에 활용하는 함수를 **scope function**이라고 한다.

### 2) scope function의 분류
- 절대 scope function의 종류를 하나씩 외우지 말자. 이런게 있구나 정도의 감만 잡고 필요할떄 찾아보거나 IDE에서 확인할 수 있기에 그런식으로 익히자.
- `let`, `run`, `also`, `apply`, `with` 다섯가지가 있다.
- `with`만 확장함수가 아니고, 나머지 4개는 모두 확장함수다.
- 확장함수의 특징은 마치 멤버함수를 쓰는 것처럼 사용된다는 것이다.
- let, run: 람다의 결과를 반환
- also, apply: 객체 그 자체를 반환

```kotlin
val value1 = person.let { // value1: age, 람다의 결과를 반환
  it.age // it 사용
}

val value2 = person.run { // value2: age, 람다의 결과를 반환
  it.age // this 사용
}

val value3 = person.also { // value3: Person, 객체 그 자체를 반환
  it.age // it 사용
}

val value4 = person.apply { // value4: Person, 객체 그 자체를 반환
  it.age // this 사용
}
```

- this와 it의 차이는 다음과 같다.
  - this: 생략이 가능한 대신, 다른 이름을 붙일 수 없다.
  - it: 생략이 불가능한 대신, 다름 이름을 붙일 수 있다.

```kotlin
// it는 다름 이름을 붙일 수 있으며, 생략이 불가능하다.
val value1 = person.let { p ->
  p.age
}

// this는 생략 가능하며, 다른 이름을 붙일수 없다.
val value2 = person.run {
  age
}
```

- 이런 차이가 발생하는 이유는 코틀린의 문법 때문이다.

```kotlin
// let은 일반함수를 파라미터로 받아서 파라미터를 함수 내부에서 호출하기에 파라미터에 대한 이름을 직접 넣어줄 수 있음 (let, also)
public inline fun <T, R> T.let(block: (T) -> R): R {
  return block(this)
}

// run은 T에 대한 확장함수를 받고(T.()), 확장함수에서는 본인 자신을 this로 호출하고 생략 할 수 있음 (run, apply)
public inline fun <T, R> T.run(block: (T).() -> R): R {
  return block()
}
```

**with(확장함수 아님)**
- with(파라미터, 람다) : this를 사용해 접근하고, this는 생략 가능하다.

```kotlin
fun main() {
  val person = Person("jeon", 100)

  with(person) { // 확장함수가 아니기에 일반함수처럼 호출 
      println(name)
      println(this.age) // 변수 정의가 없기에 this 사용
  }
}
```

### 3) 언제 어떤 scope function을 사용해야 할까?
**let**
- 하나 이상의 함수를 call chain 결과로 호출 할 때

```kotlin
val strings = listof("APPLE", "CAR")
strings.map { it.length }
  .filter { it > 3 }
  .let(::println)
  // .let { lengths -> println(lengths) } // 동일한 코드
```

- non-null 값에 대해서만 코드 블록을 실행시킬 때(제일 많이 사용되는 경우)

```kotlin
val length = str?.let {
  println(it.uppercase())
  it.length
}
```

- 일회성으로 제한된 영역에 지역 변수를 만들 때
  - -> 주로 쓰지 않는 이유는 private function으로 빼서 depth를 줄이면되기에

```kotlin
 val numbers = listOf("one", "two", "three", "four)

val modifiedFirstItem = numbers.first()
  .let { firstItem -> // 첫번째 원소에 대해 이것저것 가공할 경우 추가적인  일회성 지역변수를 생성하여 활용
    if (firstItem.length >= 5) firstItem else "!$firstItem!"
  }.uppercase()

println(modifiedFirstItem)
```

- **객체 초기화와 반환 값의 계산을 동시에 해야 할 때**
  - 기존 자바에서의 코드(`val person = personRepository.save(Person())`)가 익숙하기도 하고, 반복되는 생성 후처리는 생성자, 프로퍼티, init block으로 넣는 것이 좋기에
  - 생성자가 너무 길어지는 경우에는 코드가 좀 더 깔끔해지긴함

```kotlin
// Person 객체를 만들어 DB에 바로 저장하고, 그 인스턴스를 활용할때, run을 통해 저장된 person이 반환됨
val person = Person("jeon", 100).run(personRepository::save)

// 조금 응용하면
val person = Person("jeon", 100).run {
  hobby = "독서" // run안에서는 this를 통해 인스턴스 접근할수있기에, ㅇ리부 필드를 수정하고 저장하고 반환해줄 수 있음
  personRepository.save(this)
}
```

**apply**
- apply 특징 : 객체 그 자체가 반환된다.

**객체 설정을 할 때에 객체를 수정하는 로직이 call chain 중간에 필요할 때**

```kotlin
// 예시로 Test Fixture을 만들때 사용
// Person의 생성자에는 hobby가 없을 경우 apply 로 추가 설정 가능
fun createPerson(
  name: String,
  age: Int,
  hobby: String,
): Person {
  return Person(
    name = name,
    age = age,
  ).apply { // Person 그 자체를 반환
    this.hobby = hobby
  }
}

// 아래와 같이 특정 함수 실행전 자신의 함수를 호출하는 경우도 가능(실제 잘 사용은 안함)
val person = Person("jeon", 100)
person.apply { this.growOld() }
  .let { println(it) }
```

**also**
also 특징 : 객체 그 자체가 반환된다.

**객체를 수정하는 로직이 call chain 중간에 필요할 때**

```kotlin
mutableListOf("one", "two", "three")
  .also { println("four 추가 이전 지금 값: %it") }
  .add("four")

// 아래와 같이도 가능
val numbers = mutableListOf("one", "two", "three")
println("four 추가 이전 지금 값: %numbers")
numbers.add("four")
```

**with**

**특정 객체를 다른 객체로 변환해야 하는데, 모듈 간의 의존성에 의해 정적 팩토리 혹은 toClass 함수를 만들기 어려울 때**
- 객체를 Converting 해야 하는데 (ex. 도메인 모델 -> Dto) 한쪽에 로직을 넣기 어려울때 사용하기도함
 
```kotlin
return with(person) {
  PersonDto(
    name = name, // with를 사용함으로써 this를 생략할 수 있기에 필드가 많아지더라도 코드가 간결해짐
    age = age,
  )
}
```

### 4) scope function과 가독성
scope function을 사용한 코드가 그렇지 않은 코드보다 가독성 좋은 코드일까?

```kotlin
== 이펙티브 코틀린 책 예제 ==
// 1번 코드(전통적인 if-else 활용한 코드)
if (person != null && person.isAdult) {
  view.showPerson(person)
} else {
  view.showError()
}

// 2번 코드(scope function을 활용한 코틀린스러운 코드)
person?.takeIf { it.isAdult }
  ?.let(view::showPerson)
  ?: view.showError()
```

- 1, 2번 두 코드의 기능은 동일하다.
- 1번 코드가 개인적으로 훨씬 더 좋은 코드라 생각한다.(훨씬 코드를 이해하기 쉽기에)
  - 1)2번코드는 숙련된 코틀린 개발자만 더 알아보기 쉽다. 어쩌면 숙련된 코틀린 개발자도 잘 이해하지 못할 수 있다.(takeIf도 들어가고, let도 들어가고..)
  - 2)1번코드가 (읽기 쉽기에) 디버깅이 쉽다.
  - 3)1번코드가 그러기에 수정도 더 쉽다.
  - 4)2번코드에는 숨겨진 버그가 하나 생길 수 있는 여지가 있다. 만약,  `view::shoPerson`에서 null을 반환한다면, 엘비스 연산자에 의해 `view.showError()`도 호출되게 된다.

> **note**: 사용 빈도가 적은 관용구는 코드를 더 복잡하게 만들고 이런 관용구들을 한 문장 내에서 조합해 사용하면 복잡성이 훨씬 증가한다.

**그렇기 떄문에 scope function을 쓴다해서 꼭 가독성이 좋아지는 건 아니다. 하지만 scope function을 절대 사용하면 안된다도 아니다.**

**적절한 convention을 적용하면 유용하게 활용할 수 있는 포인트들이 분명 존재한다. 유용하다는것은 팀의 코틀린에 대한 숙련도와 개인의 코드 선호도가 영향을 줄 수 있기에 다양한 요인들에 의해 적절한 convention을 선택할 경우에는 scope function을 쓰는게 유용하게 활용될 수 있다.**

### 20강 정리
- 코틀린의 **scope function**은 일시적인 영역을 만들어 코드를 더 간결하게 하거나, method chain에 활용된다.
- scope function의 종류에는 **let / run / also / apply / with**가 있었다.
- **scope function을 사용한 코드는 사람에 따라 가독성을 다르게 느낄 수 있기 때문에, 함께 프로덕트를 만들어 가는 팀끼리 convention을 잘 정해야 한다.**


## 강의 마무리
**강의에서 다룬 내용**
- 변수와 타입
- 반복문과 제어문
- 예외처리
- OOP
- FP
- scope function

**다루지 못한 내용**
- 제네릭
- 리플렉션
- 복잡한 함수형 프로그래밍
- DSL
- 동시성 프로그래밍 (코루틴)

# Reference
- [https://www.inflearn.com/course/java-to-kotlin?cid=328606](https://www.inflearn.com/course/java-to-kotlin?cid=328606)
- [https://keeeeeepgoing.tistory.com/category/강의/자바%20개발자를%20위한%20코틀린%20입문](https://keeeeeepgoing.tistory.com/category/%EA%B0%95%EC%9D%98/%EC%9E%90%EB%B0%94%20%EA%B0%9C%EB%B0%9C%EC%9E%90%EB%A5%BC%20%EC%9C%84%ED%95%9C%20%EC%BD%94%ED%8B%80%EB%A6%B0%20%EC%9E%85%EB%AC%B8)