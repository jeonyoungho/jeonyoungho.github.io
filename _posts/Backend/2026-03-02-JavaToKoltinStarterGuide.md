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

# Reference
- [https://www.inflearn.com/course/java-to-kotlin?cid=328606](https://www.inflearn.com/course/java-to-kotlin?cid=328606)
- [https://keeeeeepgoing.tistory.com/348](https://keeeeeepgoing.tistory.com/348)
- [https://keeeeeepgoing.tistory.com/349](https://keeeeeepgoing.tistory.com/349)