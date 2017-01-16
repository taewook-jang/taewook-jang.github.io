---
layout: post
title: Kotlin 기본 문법 - 1
categories: [Kotlin]
tags: [Kotlin]
fullview: false
comments: true
published: true
---

Android를 Kotlin으로 연습하면서 코틀린 플러그인을 통해 제공하는 Convert Java를 이용하여 기본 문법을 익혔습니다. 그와 Kotlin에서 제공하는 기본 문법을 정리하였습니다.

- [Kotlin lang 홈페이지](https://kotlinlang.org/)
- [Kotlin lang 연습해보기](http://try.kotlinlang.org/#/Examples/Hello,%20world!/Simplest%20version/Simplest%20version.kt)
- [Kotlin 문서](https://kotlinlang.org/docs/reference/)


<br />

## 코틀린 기본 문법

코틀린은 ;을 사용하지 않고, 아래와 같이 변수 타입이 뒤에 붙는 형태입니다.

또한 변수 타입을 지정하지 않을 수도 있습니다.

```java
// java
int temp = 10;

// Kotlin
val temp: Int = 10
var temp: = 15
```


<br />

## 코틀린 기본 문법 - val, var

코틀린에는 2가지 변수 선언 방식이 있습니다.

- val : 변할 수 없는 상수 Java : final, C/C++ 등에서는 const
- var : 일반적인 변수에 해당합니다.

**val**

val은 read-only이면서 로컬 변수가 됩니다. java에서는 final, c/c++에서는 const에 해당합니다. 초기화 이후 변할 수 없는 값을 말하게 됩니다.

```java
val a: Int = 1
val b = 1  // Int를 추론할 수 있습니다.
val c: Int  // Int를 초기화해주어야 하는데 생성자에서 초기화해야 합니다.
  c = 100 // 생성자 시점에서 초기화해주지 않으면 문법상 오류가 발생합니다.
```

**var**

var은 일반적인 변수에 해당합니다.

```java
var x = 5 // Int를 추론할 수 있음
x += 10

// 추론은 가능하지만 실제 값이 Int가 아니더라도 오류가 발생하지는 않음
var x: Int = 1 // Int를 고정
x = 15
```

var은 val과 달리 값이 변할 수 있습니다.

val과 var 두 개 모두 Int, Long 등의 타입을 지정할 수도, 없을 수도 있습니다.

지정하지 않아도 추론을 통해서 Int 인지 String 인지 확인이 가능하지만, 이 경우 코드의 실수를 할 수 있으므로 지정하는 게 좋을 것 같습니다.


<br />

## 코틀린 기본 문법 - 함수 생성

자바에서는 아래와 같이 함수를 생성합니다.

```java
public int sum(int a, int b) {
  return a + b;
}
```

위의 코드를 Kotlin으로 변환하면 아래와 같은 형태가 만들어집니다.

```java
fun sum(a: Int, b: Int): Int {
  return a + b
}
```

가장 기본적인 Kotlin 형태로 변환하면 위와 같습니다.

Java와 기본 형태가 다른데 왼쪽에

```
int 함수명(변수 타입 변수명) { return 값 }
```

을 가지는 반면 Kotlin은

```
fun 함수명(변수명: 변수 타입): 리턴 타입 { return 값 }
```

의 형태를 가지게 됩니다.

코틀린은 좀 더 단순하게 만들 수 있습니다.

```java
// 아래와 같이 Type을 정의하여 return을 정의하거나
fun sum(a: Int, b: Int): Int = a + b
// 또는 아래와 같이 바로 return도 가능합니다.
fun sum(a: Int, b: Int) = a + b
```

한 줄로 표현도 가능합니다. 변수 타입을 명시하거나, 하지 않을 수도 있고 = 을 이용하여 단순히 이 함수가 리턴을 의미하도록 함축적으로 표현 할 수 있습니다.

함수에 조건식을 추가하면 다음처럼 됩니다.

```java
fun max(a: Int, b: Int): Int {
  if (a > b) return a
  else return b
}
```

위의 코드를 Kotlin에서 단순하게 한 줄로 처리하면

```java
fun max(a: Int, b: Int) = if (a > b) a else b
```

위와 같이 단순하게 if 문을 포함하여 한 줄로 줄여줄 수 있습니다.

의미는 똑같습니다. a가 b보다 크면 a를 return 하고 아니면 b를 리턴하게 됩니다.


<br />

## 코틀린 기본 문법 - null

코틀린의 기본 변수는 null을 가질 수 없습니다.

```java
var a: Int = 15
a = null // 문법상 오류 발생

var b: Int? = null
b = null  // 정상 수행
```

위와 같이 물음표를 추가해주었을 때만 null을 명시할 수 있습니다.

- nullable : ?
- nullable 이면 오류 발생 : !!

물음표와 느낌표 2개를 사용하는 경우가 있습니다. 일단 의미는 위와 같습니다.

물음표는 null이고, !!는 nullable 이면 오류가 발생하게 됩니다.

함수에서 null은 다음과 같이 정의할 수 있습니다.

- 아래 함수는 ABC 객체의 a를 return 하게 됩니다.
- 이때 a는 Int이면서 null이 될 수 있도록 return type에 물음표를 추가하였습니다.

```java
// ABC 객체의 .a를 return 하지만 abc가 null이면 null을 리턴합니다.
fun abc(abc: ABC?): Int? {
  // ...
  return abc?.a
}
```

<br />

## 코틀린 기본 문법 - Any 사용하기

type을 Any라는 키워드로 지정할 수 있습니다.

```java
fun getStringLength(obj: Any): Int? {
  if (obj is String) {
    // 'obj' is automatically cast to 'String' int this branch
    return obj.length
  }
  // 'obj' is still of type 'Any'
  // Type이 String이 아니라서 null을 return 하게 됩니다.
  return null
}
```

Any는 Java의 Object에 해당 대며 is는 instanceof와 같은 행동을 합니다.

위의 형태를 java 코드로 구현하면 다음과 같습니다.

```java
int getStringLength(Object obj) {
  if (obj instanceof String) {
    return obj.length;
  }

  return 0;
}
```

is String이 아님을 나타낼 대는 is앞에 "!" 간단하게 부정할 수 있습니다.

```java
// Java에서는
int getStringLength(Object obj) {
  if (!(obj instanceof String)) return 0;
  return obj.length;
}

// Kotlin
fun getStringLength(obj: Any): Int? {
  if (obj !is String) return null
  return obj.length
}
```

<br />

## 코틀린 기본 문법 - loop

코틀린의 loop는 Java와 비슷합니다.

for loop은 아래와 같이 java와 Kotlin을 정의할 수 있습니다.

while이나 fori도 비슷합니다.

```java
// Java for
ArrayList<String> arrayList = new ArrayList<>();
for (String s : arrayList) {
    Log.d("TAG", "string : " + s);
}

// Kotlin
val arrayList = ArrayList<String>()
for (s in arrayList) {
    Log.d("TAG", "string : " + s)
}
```

<br />

## 코틀린 기본 문법 - when

코틀린에는 when이라는 키워드가 있습니다.

when은 if 문을 중첩하여 사용하지 않고 간단하게 Any와 함께 구현이 가능합니다.

- Try Kotlin 코드 실행하기 : [Use When.kt - Try Kotlin](http://try.kotlinlang.org/#/Examples/Basic%20syntax%20walk-through/Use%20when/Use%20when.kt)

```java
fun main(args: Array<String>) {
    cases("Hello") // String
    cases(1) // Int
    cases(System.currentTimeMillis()) // Long
    cases(MyClass()) // Not a string
    cases("hello") // Unknown
}

fun cases(obj: Any) {
    when (obj) {
        1 -> println("One")
        "Hello" -> println("Greeting")
        is Long -> println("Long")
        !is String -> println("Not a string")
        else -> println("Unknown")
    }
}
```

위의 예제를 실행하면 결과는 아래와 같습니다.

![Screenshot 2016-07-31 21.32.16](/images/2016/2016-08-02-Basic-Kotlin-01/Screenshot 2016-07-31 21.32.16.png)


<br />

## 코틀린 기본 문법 - ranges

숫자의 범위를 지정하여 사용하는 방법입니다.

이해하기 쉽도록 for loop에서 사용하는 방법을 먼저 설명해보겠습니다.

java의 for 문에서는 i = 1에서 5까지 출력하는 방법을 아래와 같이 작성하였습니다.

```java
// java
for (int i = 1; i <= 5; i++) {
  Log.i("TAG", "index : " + i);
}
```

코틀린에서는 ranges를 통해서 아래와 같이 1~5까지의 값을 출력하는 for loop을 만들 수 있습니다.

```java
// Kotlin
for (x in 1..5) {
  println(x)
}
```

Kotlin에서는 ranges 형태를 if 문에서도 사용 가능합니다.

- Try Kotlin 코드 실행하기 : [Try Kotlin - ranges and in](http://try.kotlinlang.org/#/Examples/Basic%20syntax%20walk-through/Use%20ranges%20and%20in/Use%20ranges%20and%20in.kt)

```java
val array = arrayList<String>
array.add("aaa")
array.add("bbb")

val x = 3

if (x !in 0..array.size - 1)
  println("Out: array 사이즈는 ${array.size} 요청한 x = ${x}")

//위의 출력 값은
// Out: array 사이즈는 2 요청한 x = 3
```

<br />

## 마무리

코틀린 기본 문법을 정리해보았습니다.

코틀린은 안전한 null을 처리할 수 있도록 지원하고 있습니다.

기본 문법에서 보았던 null 처리에 대해서 조금더 상세하게 다루어보고, Idioms, Generics 도 다루어 보도록 하겠습니다.

이런 부분은 추후 하나씩 정리해보도록 하겠습니다.

우선 다음 첫 번째 예제로 Generics 부분과 Idioms을 간단하게 살펴보고 Basic adapter 예제를 다루어보도록 하겠습니다.


<br />

## 코틀린 관련 포스트 목록

- [Android Kotlin 시작하기](http://thdev.tech/androiddev/kotlin/2016/07/31/Kotlin-Android-Start.html)
- [Kotlin 기본 타입](http://thdev.tech/kotlin/2016/10/02/Kotlin-Basic-Types.html)
- [Kotlin 기본 문법 - 1](http://thdev.tech/kotlin/2016/08/02/Basic-Kotlin-01.html)
- [Kotlin 안전한 null 처리](http://thdev.tech/kotlin/2016/08/04/Kotlin-Null-Safety.html)
- [Kotlin 기본 문법 - 2](http://thdev.tech/kotlin/2016/08/07/Kotlin-Idioms.html)
- [Kotlin Generics 사용](http://thdev.tech/kotlin/android/2016/09/08/Kotlin-Generics.html)
- [kotlin 주요 class](http://thdev.tech/kotlin/2016/10/09/Kotlin-Class.html)
- [Kotlin Function - default, 변수 순서 변경](http://thdev.tech/kotlin/2017/01/15/Kotlin-Function.html)
- [Kotlin constructor init 이란?](http://thdev.tech/kotlin/2017/01/17/Kotlin-Constructor-Init.html)

### 추가 내용

- [Android Kotlin으로 작성한 Activity/Fragment 살펴보기](/androiddev/kotlin/2016/09/04/Android-Kotlin-Base-Activity_Fragment.html)
- [Android Kotlin으로 작성한 Presenter/View 살펴보기](/androiddev/kotlin/2016/09/22/Android-Base-Presenter_View.html)
- Android Kotlin으로 작성한 Adapter 살펴보기
