---
layout: post
title: Kotlin 안전한 null 처리
published: false
---

## Kotlin 관련 글

- [Android Kotlin 시작하기](http://thdev.tech/Kotlin-Android-Start/)
- [Kotlin 기본 문법 정리](http://thdev.tech/Basic-Kotlin-01/)

<br />

이번 글을 작성하면서 참고한 문서를 아래와 같습니다.

- [Null Safety](https://kotlinlang.org/docs/reference/null-safety.html) 문서를 살펴보면 Kotlin에서 제공하는 안전한 null 사용 방법을 제공하고 있습니다. 해당 내용을 포함하여 정리하였습니다.
- [Realm - 안녕하세요. 코틀린 #3](https://realm.io/kr/news/kotlin-03/)

<br />

코틀린은 안전한 null 처리를 지향하고 있습니다.

NullPointerException. 줄여서 NPE라고도 합니다. 이는 모든 개발 언어에서 발생할 수 있고, 개발자가 안고 가야 할 숙명과도 같습니다.

Null을 처음 도입한 "찰스 앤터니 리처드 호어" 조차 [The Billion Dollar Mistake.](https://en.wikipedia.org/wiki/Tony_Hoare#Apologies_and_retractions)라고 하였다고 합니다.

이번 글은 코틀린에서 제공하는 안전한 null 처리를 정리해보았습니다.

<br />

## 안전한 null 처리 방법

코틀린으로 작성된 코드를 보게 되면 아주 흔하게 ? 사용을 볼 수 있습니다.

Java에서는 이를 @Nullable로 변환하여 보여줍니다.

말 그대로 물음표를 함께 사용하였을 경우 null이 될 수 있다는 것을 의미합니다.

코틀린은 기본적으로 null을 담을 수 없도록 설계하였습니다.

```java
var temp: String = "abc"
temp = null // 문법 오류
```

temp 변수에 null을 초기화하려면 그 즉시 문법 오류를 발생시킵니다.

코틀린에서 null을 사용하기 위해서는 아래와 같습니다.

```java
var temp: String? = "abc"
temp = null
```

<br />

## null 체크를 통한 해결

일반적으로 java에서는 null을 피하기 위해서는 아래와 같은 코드를 사용하고 있습니다.

```java
int size = -1;
if (temp != null) {
  size = temp.length();
}

// 또는 TextUtils.isEmpty()를 활용
if (!TextUtils.isEmpty(temp)) {
  size = temp.length();
}
```

위와 같은 방법으로 null을 피할 수 있는데 코틀린도 비슷한 형태로 null을 피할 수 있습니다.

```java
// java를 닮은 kotlin
val size = if (temp != null) temp.length else -1
```

<br />

## Safe Calls

kotlin에서는 null을 직접 체크하지 않고도 간단하게 null을 피할 수 있는 방법을 제공하고 있습니다.

null을 사용할 수 있도록 만들어주는 물음표(?)를 피할 때도 동일하게 사용하게 됩니다.

아래와 같이 ?.을 함께 사용하면 간단하게 null을 피할 수 있습니다.

```java
val size = temp?.length

// 함수를 정의할 경우는
fun getSize(temp: String?): Int? {
  return temp?.length
}
```

null을 간단하게 피할 수 있으므로 다음과 같은 중첩 호출에서도 간단하게 null을 피할 수 있게 됩니다.

```java
// 다음과 같이 초기화 되었을 경우
// abc = ABC()
// bbb = BBB()
// ddd = null

abc?.bbb?.ddd?.name
```

abc에서 bbb를 호출하고, ddd를 호출하게 됩니다. 이때 ddd는 null로 초기화되어 있는 상태라면 다음을 이어서 수행하지 않고 그 즉시 null을 return 하게 됩니다.

별도의 null로 인한 오류가 발생하지는 않고, 바로 null을 return 하고 완료 처리 됩니다.

**null을 배제하려면?**

또 다른 null을 피하는 방법도 있습니다. 저는 커니님의 글에서 보고 이 .let {} 문법을 흔하게 사용하게 되었습니다.

[커니의 안드로이드 이야기 - 코틀린의 유용한 함수들 - let, apply, run, with](http://kunny.github.io/lecture/kotlin/2016/07/06/kotlin_let_apply_run_with/)

위의 글에 .let에 대한 설명이 나와있고, [Safe Calls](http://kotlinlang.org/docs/reference/null-safety.html#safe-calls)에도 나와 있습니다.

?. 와 let을 함께 사용할 경우 null을 배제하고 동작을 수행할 수 있게 됩니다.

그에 대한 예제 코드는 아래와 같습니다.

list의 "A", null, "B"가 있습니다. ?.let {}를 사용하여 출력할 경우 A, B 만 출력되게 됩니다.

```java
val listWithNulls: List<String?> = listOf("A", null, "B")
for (item in listWithNulls) {
     item?.let { println(it) } // prints A and ignores null
}
```

![Screenshot 2016-08-01 22.57.37](/images/2016/2016-08-04-Kotlin-Null-Safety/Screenshot 2016-08-01 22.57.37.png)

단순히 print를 한다면 null 을 포함하게 되지만 ?.let {}를 함께 사용한다면 null을 배제할 수 있게 됩니다.


<br />

## ?.을 이용하면 null이 return 되는데 다른 값을 하려면?

java에서는 아래와 같이 temp의 사이즈를 null 일 경우와 그렇지 않은 경우에 대해서 간단하게 확인이 가능합니다.

```java
String temp = "";
int size = temp != null ? temp.length : 0;
```

코틀린에서도 동일한 코드로 temp의 사이즈 또는 0을 return 할 수 있습니다.

```java
var temp: String? = ""
val size = if (temp != null) temp.length else 0
```

비슷하지만 사실 if 문을 포함하여 더 길어졌습니다. ?.을 제공함으로 인해

```java
var temp: String? = null
val size = temp?.length
```

![Screenshot 2016-08-01 23.05.21](/images/2016/2016-08-04-Kotlin-Null-Safety/Screenshot 2016-08-01 23.05.21.png)

결과를 보시면 첫 줄은 String이 널이 아니라서 0을 출력하였지만 2번째는 null이라서 ?. 문법이 통과하지 못하여 null을 리턴합니다.

하지만 제가 필요한 건 temp의 length 또는 0이어야 합니다.

그래서 kotlin은 또 다른 코드를 제공합니다. ?: 입니다.

```java
var temp: String? = null
val size = temp?.length ?: 0
```

![Screenshot 2016-08-01 23.08.02](/images/2016/2016-08-04-Kotlin-Null-Safety/Screenshot 2016-08-01 23.08.02.png)

?: 문법을 추가함으로 인해 null 일 경우 0을 출력하도록 만들었습니다.

throw 역시 만들 수 있습니다.

```java
var temp: String? = null
val size = temp?.length ?: throw IllegalArgumentException("temp is null")
```

![Screenshot 2016-08-01 23.09.43](/images/2016/2016-08-04-Kotlin-Null-Safety/Screenshot 2016-08-01 23.09.43.png)

?: 뒤에 throw를 바로 달면 바로 Exception 발생도 가능합니다.

<br />

## NPE를 발생시키려면??

?.이 safe null이다 보니 throw 같은 처리가 없다면 NullPointerException(NPE)가 발생할 일은 없습니다.

하지만 진행상 NPE를 발생하도록 해야 하는 경우도 있습니다.

null 일 경우 NPE가 발생하도록 "!!"을 제공하고 있습니다.

```java
var temp: String? = null
val size = temp!!.length
```

위의 경우 무조건 NullPointerException이 발생하게 됩니다.

![Screenshot 2016-07-31 22.17.45](/images/2016/2016-08-04-Kotlin-Null-Safety/Screenshot 2016-07-31 22.17.45.png)

위와 같이 NPE가 필요한 코드에서는 "!!"을 사용하시면 NPE 확인을 할 수 있습니다.

<br />

## 안전한 형 변환

안전한 형 변환을 제공합니다. 형 변환이 불가능한 곳에서 형 변환을 시도할 경우 "?"을 활용하여 오류가 발생하지 않게 할 수 있습니다.

String a를 aInt로 변환할 경우 null이 return 됩니다. ?: 을 함께 활용한다면 원하는 값으로 초기화도 가능합니다.

```java
val a: String? = "ABC"

// String을 강제로 형 변환 할 경우에는 CastException이 발생하여 이 경우 null이 저장
val aInt: Int? = a as? Int

// null이 아닌 0을 저장하려면
val aInt: Int? = a as? Int ?: 0
```

<br />

## filterNotNull

List에 filterNotNull이라는 메서드가 제공됩니다. List Copy 시 null을 제외한 값을 복사하게 됩니다.

```java
val nullableList: List<Int?> = listOf(1, 2, null, 4)
for (i in nullableList) {
  print("${i} ")
}
println("")

// null filter
val intList: List<Int> = nullableList.filterNotNull()
for (i in intList) {
  print("${i} ")
}
```

![Screenshot 2016-08-01 23.18.26](/images/2016/2016-08-04-Kotlin-Null-Safety/Screenshot 2016-08-01 23.18.26.png)

위와 같이 결과를 가지게 됩니다.

<br />

## 마무리

코틀린은 null을 기본적으로 허용하지 않습니다. 하지만 개발을 하다 보면 null이 필요한 경우도 있고, null이 예기치 않게 발생할 수 있습니다.

Android Annotations을 활용하면 @Nullable와 @NotNull을 Annotations으로 적용할 수 있습니다.

@Nullable이 올 경우 개발자가 if () 등을 활용하여 null을 명시적으로 예외 처리해 주어야 합니다.

코틀린에서는 이를 ?. 또는 ?.let {}을 사용한다면 안전하게 처리할 수 있고, 문법도 짧아지게 됩니다.

기본 문법에 이어 Kotlin의 null 예외 처리를 정리해보았습니다.

<br />

## Kotlin 관련 글

- [Android Kotlin 시작하기](http://thdev.tech/Kotlin-Android-Start/)
- [Kotlin 기본 문법 정리](http://thdev.tech/Basic-Kotlin-01/)
