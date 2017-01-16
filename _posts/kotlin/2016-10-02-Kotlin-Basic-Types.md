---
layout: post
title: Kotlin 기본 타입
categories: [Kotlin]
tags: [Kotlin, Android]
fullview: false
comments: true
published: true
---

코틀린을 시작하기 전에 정리했어야 할 부분인데 지금 정리해보려고 합니다.

Kotlin의 기본 타입을 간단하게 정리해보겠습니다.


<br />

## 코틀린 Type 정의

코틀린은 아래와 같은 Type을 사용합니다. Java와 비교해보면 모두 대문자로 시작합니다.

| Java Type | Kotlin Type |  Kotlin Type 용량(bit)   |
| :-------: | :---------: | :---------------------: |
| double    | Double      | 64                      |
| float     | Float       | 32                      |
| long      | Long        | 64                      |
| int       | Int         | 32                      |
| short     | Short       | 16                      |
| byte      | Byte        | 8                       |


<br />

- `Int` : `123`으로 표현
- `Long` : `123L`으로 표현
- `Double` : `123.5`로 표현
- `Float` : `123.0F` 또는 `123.0f`으로 표현


<br />

## 명시적 형 변환

Java에서는 명시적 형 변환은 다음과 같이 하게 됩니다.

```java
long a = 123;
int b = (int) a;
```

코틀린에서도 동일한데, 위와 비슷한 방법으로 코드를 적용하면 다음과 같은 오류가 발생합니다.

```kotlin
val a: Long = 123
val b: Int = a // ERROR 발생
```

위와 같은 코드를 작성하게 되면 Kotlin에서는 오류가 발생하게 됩니다. `Type mismatch: inferred type is Long but Int was expected`이 발생합니다.

Type이 맞지 않다는 의미입니다. 그래서 아래와 같은 방법으로 형 변환을 해주어야 합니다.

```java
val a: Long = 123
val b: Int = a.toInt()

// 또는 Int를 생략하고 다음과 같이 할 수 있습니다.

val a: Long = 123
val b = a.toInt()
```

코틀린에서는 형 변환을 위해서 다음의 옵션을 제공합니다.

- `toByte()` : Byte로 변환
- `toShort()` : Short로 변환
- `toInt()` : Int로 변환
- `toLong()` : Long로 변환
- `toFloat()` : Float로 변환
- `toDouble()` : Double로 변환
- `toChar()` : Char로 변환


### 추가로 String의 형 변환은 어떻게 할까요?

Java와 코틀린에서 많은 차이는 없지만 다음과 같이 할 수 있습니다.

```java
// Java에서
String ab = "123";
int bb = Integer.parseInt(ab);

// Kotlin에서
val ab: String = "123"
val bb = Integer.parseInt(ab)
```


<br />

## Strings

String을 간단하게 for를 이용하여 출력이 가능합니다.

다음과 같이 item의 `Hello, world!`를 간단하게 한자씩 출력하는 예제입니다.

```kotlin
val item = "Hello, world!"
for (a in item) {
    Log.d("TAG", "out $a")
}
```

조금 더 확장하여 다음과 같은 구문도 가능합니다. `"""`을 이용하여 다음과 같이 val text에 실제 코드를 그대로 `String`으로 저장할 수 있습니다.

```
val text = """
  for (c in "foo")
    print(c)
"""
```

위의 `text`를 출력하면 다음이 출력됩니다.

```
I/System.out:       for (c in "foo")
I/System.out:         print(c)
```

### String Templates

`String`을 좀 더 자유자재로 사용하는 것도 가능합니다.

```kotlin
val index = 10
val s = "index = $index"
```

위의 코드를 실행하면 `index = 10`이 출력됩니다.

```kotlin
val item = "ABC"
val str = "$item.length is ${item.length}"
```

위를 실행하면 `ABC.length is 3`이 출력됩니다.

String을 좀 더 재미있게? 사용할 수 있습니다. `{}`으로 묶을 경우는 해당 함수가 포함되어 실행되게 된 결과를 `return` 하게 되어 위와 같은 결과 확인이 가능합니다.


<br />

## 마무리

[Kotlin Basic Types](https://kotlinlang.org/docs/reference/basic-types.html)에 가시면 더 많은 정리가 되어 있습니다. 저는 간단하게 자주 사용하게 될 부분에 대해서만 정리를 해보았습니다.

감사합니다.


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
