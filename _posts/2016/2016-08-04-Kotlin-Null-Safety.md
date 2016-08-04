---
layout: post
title: Kotlin 안전한 null 처리
published: false
---

## Kotlin 관련 글

- [Android Kotlin 시작하기](http://thdev.tech/Kotlin-Android-Start/)
- [Kotlin 기본 문법 정리](http://thdev.tech/Basic-Kotlin-01/)

<br />

이번 글을 작성하면서 참고한 문서는 아래와 같습니다.

- [Null Safety](https://kotlinlang.org/docs/reference/null-safety.html) 문서를 살펴보면 Kotlin에서 제공하는 안전한 null 사용 방법을 제공하고 있습니다. 해당 내용을 포함하여 정리하였습니다.
- [Realm - 안녕하세요. 코틀린 #3](https://realm.io/kr/news/kotlin-03/)
- [커니의 안드로이드 이야기 - 코틀린의 유용한 함수들 - let, apply, run, with](http://kunny.github.io/lecture/kotlin/2016/07/06/kotlin_let_apply_run_with/)

<br />

NullPointerException은 아주 흔하게 볼 수 있는 오류입니다.

NullPointerException을 줄여서 NPE라고도 하는데 이 Null을 처음 도입한 "찰스 앤터니 리처드 호어"가 다음과 같이 말했다고 합니다.

- [The Billion Dollar Mistake. : wikipedia](https://en.wikipedia.org/wiki/Tony_Hoare#Apologies_and_retractions)

코틀린도 null에 대한 예외는 따로 없습니다. 하지만 기본적으로 null을 허용하지 않도록 개발되었고, null을 쓰더라도 안전하게 사용될 수 있도록 구성하고 있습니다.

안전한 null 처리를 위한 글이 코틀린 문서에 나와 있어서 정리해보았습니다.

<br />

## 안전한 null 처리 방법

Android Java에서 Kotlin으로 convert 해보면 @NotNull과 @Nullable이 조금 다르게 표현됨을 볼 수 있습니다.

다음의 코드를

```java
public void set(@NotNull String a, @Nullable String b) {
  // Do noting
}
```

Convert를 통해서 kotlin으로 변환하면 다음과 같은 코드가 나오게 됩니다.

```java
fun set(a: String, b: String?) {
// Do noting
}
```

기존 @NotNull을 포함하던 코드는 아래와 같이 아무것도 없이 변환됩니다.

```
@NotNull String a -> a: String
```

@Nullable으로 작성하였던 b String은 물음표가 포함되었습니다.

Kotlin에서는 물음표가 Nullable 임을 말합니다.


```
@Nullable String b -> b: String?
```

다시 코틀린으로 돌아와서 문법 오류를 통해서 NotNull과 Nullable을 확인할 수 있습니다.

```java
var temp: String = "abc"
temp = null // 문법 오류
```

위의 String은 NotNull을 뜻 함으로 null 입력이 불가능하며, 문법상 오류가 발생하게 됩니다.

null을 사용하고 싶다면 다음과 같이 물음표를 포함하는 코드를 작성해야 합니다.

```java
var temp: String? = "abc"
temp = null
```

이 경우에는 null을 담을 수 있고, null이 포함되더라도 오류가 발생하지 않습니다.


<br />

## null 체크를 통한 해결

Java와 코틀린에서 각각 null을 피하는 방법을 간단하게 적어보겠습니다.

가장 기본적인 방법으로 null을 피하는 방법은 아래와 같습니다.

try/catch를 통해서도 피할 수 있으나, if의 null 체크를 통해서 String의 TextSize를 확인하는 방법입니다.

```java
String temp = null;
int size = -1;
if (temp != null) {
  size = temp.length();
}

// 또는 TextUtils.isEmpty()를 활용
if (!TextUtils.isEmpty(temp)) {
  size = temp.length();
}
```

위와 비슷하게 kotlin에서도 사용할 수 있습니다.

뭐 같은 방법으로 한줄로 줄여줄 수 있지만 아래와 같습니다.

```java
var temp: String? = null
// java를 닮은 kotlin
val size = if (temp != null) temp.length else -1
```

temp가 null이므로 -1이 size가 됩니다.

<br />

## Safe Calls

if 문을 이용해 null을 체크하지 않고도 null을 피할 수 있는 방법이 있습니다.

좀 더 코틀린 스러운 접근 방법입니다.

문법상 "?." 라는게 제공됩니다. null을 사용하는 코드에 물음표를 포함하고 .으로 이어지면 코틀린에서 제공하는 가장 쉽게 널을 피하는 방법입니다.

```java
// null을 포함 할 수 있는 temp var 변수이며, null로 초기화 합니다.
var temp: String? = null

// 다음과 같이 사이즈를 체크하게 됩니다.
val size = temp?.length
```

위의 결과는 다음과 같습니다. temp가 null이기 때문에 val size를 출력하면 다음과 같은 결과를 확인할 수 있습니다.

//// TODO images 추가


다음은 함수에서의 사용 방법입니다.

다음의 함수는 null을 포함할 수 있는 변수 temp를 입력 받아 Int로 return 하는 함수입니다.

Int 역시 null을 포함할 수 있습니다.

```java
// 함수를 정의할 경우는
fun getSize(temp: String?): Int? {
  return temp?.length
}
```

위의 결과도 이전과 같습니다. temp가 null이면 return은 null이 됩니다.

null이 포함된 함수에 물음표를 이용하여 null 체크하였을때의 장점은 다음과 같은 코드입니다.

타고타고 넘어가는 다음과 같은 코드에서도 아래 자바 코드 처럼 할 필요 없이

```java
if (abc != null && bbb != null && ddd != null) {
  return abc.bbb.ddd.name;
}
return null;
```

아래와 같이 간결하게 ?.을 붙여줌으로써 중간에 어디서든 null이라면 간단하게 null을 return 하게됩니다.

```java
// 다음과 같이 초기화 되었을 경우
// abc = ABC()
// bbb = BBB()
// ddd = null

return abc?.bbb?.ddd?.name
```

별도의 null 체크해줄 필요 없이 간단하게 처리가 가능합니다.

<br />

## NULL을 완전히 배제하려면?

null을 완전히 배제해야 하는 경우도 있습니다.

list에서 null을 제외한 값을 뽑아야 하는 경우가 있습니다.

커니님이 정리해두신 다음의 글을 참고하시면 많은 도움이 되실것 같습니다.

- [커니의 안드로이드 이야기 - 코틀린의 유용한 함수들 - let, apply, run, with](http://kunny.github.io/lecture/kotlin/2016/07/06/kotlin_let_apply_run_with/)
- [Safe Calls](http://kotlinlang.org/docs/reference/null-safety.html#safe-calls) 부분에 .let{} 부분을 보시면 됩니다.


list에서 null을 배제하기 위해서는 다음의 코드를 사용하게 됩니다.

```java
List<String> itemList = new ArrayList<>();
itemList.add("A");
itemList.add(null);
itemList.add("B");

for (String text : itemList) {
  if (text != null) {
    Log.d("TAG", "Text : " + text);
  }
}
```

코틀린에서는 ?.let {}을 함께 사용하면 null을 제외한 결과를 출력할 수 있습니다.

?.let를 함께 사용할 경우 null 아이템이면 let {} 구문을 타지 않고 다음 라인을 타게 됩니다.

출력이나 List 복사를 할때에는 null을 제외하여야 하기 때문에 .let{} 구문을 사용해야 합니다.

아래 구문에는 위의 Java와 같이 null을 리스트에 포함하고 있습니다.

```java
val listWithNulls: List<String?> = listOf("A", null, "B")
for (item in listWithNulls) {
     item?.let { println(it) } // prints A and ignores null
}
```

![Screenshot 2016-08-01 22.57.37](/images/2016/2016-08-04-Kotlin-Null-Safety/Screenshot 2016-08-01 22.57.37.png)

위와 같은 결과를 확인 가능합니다.


<br />

## ?.을 이용하면 null이 return 되는데 다른 값을 하려면?

?.을 이용하였을때 결과는 아래와 같습니다.

![Screenshot 2016-08-01 23.05.21](/images/2016/2016-08-04-Kotlin-Null-Safety/Screenshot 2016-08-01 23.05.21.png)

하지만 java에서는 ? : 을 이용하면 조건에 맞는 값을 return 할 수 있습니다.

코틀린에서는 없을까요? 우선 Java 문법으로는 다음과 같이 축약 할 수 있습니다.

```java
String temp = "";
int size = temp != null ? temp.length : 0;
```

똑같은 방식으로 코틀린에서 작성하면 아래와 같습니다.

```java
var temp: String? = ""
val size = if (temp != null) temp.length else 0
```

더 복잡하게 if와 else 까지 붙여야 하지는 않습니다.

다음 코드는 null이 리턴되게됩니다.

```java
var temp: String? = null
val size = temp?.length
```

![Screenshot 2016-08-01 23.05.21](/images/2016/2016-08-04-Kotlin-Null-Safety/Screenshot 2016-08-01 23.05.21.png)

결과를 보시면 첫 줄은 String이 널이 아니라서 0을 출력하였지만 2번째는 null이라서 ?. 문법이 통과하지 못하여 null을 리턴합니다.

하지만 temp의 length 또는 0이 return 되어야 합니다.

Kotlin에서는 다음과 같은 코드를 제공하고 있습니다. ?뒤에 : 을 붙여주면 ?:이 됩니다.

if 에 대체되는 문법이 물음표라면, else에 대체되는 문법이 ?:이 됩니다.

아래 코드와 같이 작성해주면 length 또는 0이 return 되게 됩니다.

```java
var temp: String? = null
val size = temp?.length ?: 0
```

위 코드의 결과는 아래와 같습니다.

![Screenshot 2016-08-01 23.08.02](/images/2016/2016-08-04-Kotlin-Null-Safety/Screenshot 2016-08-01 23.08.02.png)


?: 문법 뒤에 throw 를 붙여서 오류를 낼 수도 있습니다.

```java
var temp: String? = null
val size = temp?.length ?: throw IllegalArgumentException("temp is null")
```

?: 뒤에 throw의 결과는 아래와 같이 Exception이 됩니다.

![Screenshot 2016-08-01 23.09.43](/images/2016/2016-08-04-Kotlin-Null-Safety/Screenshot 2016-08-01 23.09.43.png)


<br />

## NPE를 발생시키려면??

NullPointerException을 발생시키는 방법도 있습니다.

throw을 이용하여서도 Exception을 발생시킬 수 있지만 좀 더 쉬운 방법을 제공합니다.

NotNull이어야 할 경우 "!!"을 사용합니다. 느낌표 두개가 null일 경우 Exception을 발생시키게 됩니다.

```java
var temp: String? = null
val size = temp!!.length
```

위의 경우 무조건 NullPointerException이 발생하게 됩니다.

![Screenshot 2016-07-31 22.17.45](/images/2016/2016-08-04-Kotlin-Null-Safety/Screenshot 2016-07-31 22.17.45.png)


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

filterNotNull이라는 메소드가 제공되고 있습니다.

List copy 시 위에서 정의하였던 ?.let {}를 이용할 수도 있지만 이미 구현된 코드가 filterNotNull이 있습니다.

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
