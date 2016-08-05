---
layout: post
title: Kotlin 코틀린의 주요 문법들
categories: [Kotlin]
tags: [Kotlin]
fullview: false
comments: true
published: false
---

코틀린을 사용하기 위한 기본 문법 정리에 이어서 좀 더 디테일한 문법을 정리해보고자 합니다.

Kotlin 문서에 Idioms가 정리가 되어 있습니다. 해당 문서를 참조하여 작성합니다.

- [Idioms 문서](https://kotlinlang.org/docs/reference/idioms.html)

간단한 Data class 생성과 Java/Kotlin에서 접근 방법 등을 살펴봅니다.


<br />

## 코틀린 관련 포스트 목록

- [Android Kotlin  시작하기](http://thdev.tech/androiddev/kotlin/2016/07/31/Kotlin-Android-Start.html)
- [Kotlin 기본 문법 정리](http://thdev.tech/kotlin/2016/08/02/Basic-Kotlin-01.html)
- [Kotlin 안전한 null 처리](http://thdev.tech/kotlin/2016/08/04/Kotlin-Null-Safety.html)
- [Kotlin 코틀린의 주요 문법들]()


<br />

## DTOs(Data Transfer Object)(POJOs/POCOs) 생성하기

- 참고자료 : [DTO, POJO에 대한 설명 - okky](http://okky.kr/article/115632)
- DTO : [Data Transfer Object](https://en.wikipedia.org/wiki/Data_transfer_object)
- POJO : [Plain Old Java Object](https://en.wikipedia.org/wiki/Plain_Old_Java_Object)
- POCO : [Plain Old CLR Object](http://stackoverflow.com/questions/3392580/a-better-explanation-of-poco)

코틀린을 사용하면서 get/set을 사용할 필요가 없다는 말을 들었습니다.

실제 코틀린은 data class를 간단하게 생성할 수 있는 DTO를 제공하고 있습니다.

java에서는 아래와 같은 일반적인 get/set을 포함하거나 public 변수를 선언하여 사용하게됩니다.

```java
public class Sample() {
  public String name;
  public String email;

  public Sample(String name, String email) {
    this.name = name;
    this.email = email;
  }
}
```

위와 같이 별도의 get/set을 선언하지 않고 만들거나, 아래와 같이 get/set을 각각 추가하여 만들기도 합니다.

더 쉬운 방법으로는 get/set을 만들어주는 Annotation을 사용하는게 더 좋긴 합니다....

**get/set을 쉽게 만들 수 있도록 Java9에서는 추가해준다는데 ... 사실 이건 사용하려면 한참 후에나...**


```java
public class Sample() {
  private String name;
  private String email;

  public void setName(String name) {
    this.name = name;
  }

  public String getName() {
    return name;
  }

  public void setEmail(String email) {
    this.email = email;
  }

  public String getEmail() {
    return email;
  }
}
```

kotlin으로는 data class를 아래와 같이 구현할 수 있습니다.

아래는 읽기 전용의 val로 선언한 data class 입니다.

```Kotlin
data class Sample(val name: String, val email: String)
```

아래는 read/write가 가능하도록 구현한 data class 입니다.

```kotlin
data class Sample(var name: String, var email: String)
```

위의 두개의 코드는 당연히 다릅니다.

일반적으로 Java의 data class를 생성한다면 var이 되겠고, Read-only로 작성한다면 val이 됩니다.

데이터 클래스니깐 Read-only 보다는 var이 맞겠죠.


<br />

###Java에서 val로 선언된 data class 접근 시

Java에서 val을 사용하여 접근할때는 다음과 같습니다.

val을 사용하기 때문에 수정이 불가능하고 Read-only가 됩니다.

**java에서는 final 을 붙인것과 같습니다.**

Sample에 다음과 같이 초기화 합니다.

![Screenshot 2016-08-02 21.48.56](/images/2016/2016-08-05-Kotlin-Idioms/Screenshot 2016-08-02 21.48.56.png)

위의 값을 초기화 한 다음에는 val로 선언하였기에 접근만 가능하고, 셋은 할 수 없습니다.

![Screenshot 2016-08-02 21.49.06](/images/2016/2016-08-05-Kotlin-Idioms/Screenshot 2016-08-02 21.49.06.png)


<br />

###java에서 var로 선언된 data class 접근 시

var로 선언하였기에 다음과 같이 set을 사용할 수 있습니다.

Read-only로 선언하는 data 클래스는 거의 없다고 봐야 하니 아래와 같이 사용 될 수 있도록 만들면 좋겠습니다.

**특정 변수만 Read-only로 만드는것도 가능하니 잘 활용하면 편하게 사용 가능합니다.**

![Screen Shot 2016-08-05 at 6.14.21 PM](/images/2016/2016-08-05-Kotlin-Idioms/Screen Shot 2016-08-05 at 6.14.21 PM.png)

<br />

###Kotlin에서 Sample 접근시

코틀린에서도 java와 같지만 get/set이 없이 아이템에 직접 접근하게 됩니다.

코틀린에서도 var은 get/set 모두 간으하고 val은 get 만 가능한건 동일합니다.

![Screenshot 2016-08-02 21.58.02](/images/2016/2016-08-05-Kotlin-Idioms/Screenshot 2016-08-02 21.58.02.png)

<br />

###data 클래스는 생성자로만 초기화 할 수 있나?

코틀린의 data 클래스에서는 최소 한개의 값을 생성자에 추가해주어야 합니다.

코틀린에서는 default 값을 정의하여 초기화를 하지 않을 수 있지만 Java에서는 무조건 1개의 값을 추가해야 합니다.

아쉽지만 다음과 같은 형태로 만들 수 있습니다.

```java
data class Sample(var abc: String) {
    var name: String = ""
    var email: String = ""
}
```

또는 data를 제외하고 다음과 같은 클래스를 만드셔도 됩니다.

```java
class Sample() {
    var abc: String = ""
    var name: String = ""
    var email: String = ""
}
```

위와 같이 선언해주더라도 java에서는 get/set을 모두 사용 가능하기에 문제 없습니다.

data class 사용할 경우에는 좀 더 data 클래스 처럼 사용이 가능한데 다음의 문서를 참고해주세요.

- [Data class 정의](https://kotlinlang.org/docs/reference/data-classes.html)


<br />

###함수에 기본값 정의

함수 정의에서 기본값을 가지도록 정의할 수 있습니다.

처음 C++을 배울 때 유용하게 사용하였었는데 Java에서는 제공되지 않았었습니다.

Overloading을 이용해서 비슷하게 구현이 가능합니다.

```java
public void add(String name) {
  add(name, "default");
}

public void add(String name, String email) {
  // ...
}
```

Kotlin에서는 다음과 같이 변수에 기본값을 정의할 수 있습니다.

```java
fun add(name: String, email: String = "default") {
  // ...
}
```

코틀린에서는 변수 뒤에 `=`을 붙임으로 초기값을 지정할 수 있습니다.

초기값으 지정되어 있으면 별도로 지정하지 않아도 email은 default를 가지게 됩니다.

Kotlin으로 작성한 코드를 Java와 Kotlin으로 각각 사용하면 다음과 같습니다.


<br />

###Kotlin에서 접근시

Kotlin으로 접근할 경우에는 아래와 같이 Default 값 정의를 확인할 수 있으며, default로 초기화되어 있는 값은 입력하지 않아도 됩니다.

![Screenshot 2016-08-02 22.01.15](/images/2016/2016-08-05-Kotlin-Idioms/Screenshot 2016-08-02 22.01.15.png)

**Java에서 접근시**

Java에서는 Kotlin에서 default로 정의한 함수를 접근하여도 default 값이 적용되지 않습니다.

![Screenshot 2016-08-02 22.01.52](/images/2016/2016-08-05-Kotlin-Idioms/Screenshot 2016-08-02 22.01.52.png)

아래와 같이 @NotNull으로 처리되며 입력하지 않을 경우 오류가 발생하게 됩니다.

![Screenshot 2016-08-02 22.02.04](/images/2016/2016-08-05-Kotlin-Idioms/Screenshot 2016-08-02 22.02.04.png)

**생성자에서도 동일하게 default 값 처리를 할 수 있으며, 이 역시 Kotlin에서만 default 값이 적용될 뿐 java에서는 NotNull으로 처리됩니다.**

<br />

## Kotlin Lambdas

Kotlin은 Lambdas을 제공하고 있습니다. Java 8이나 레트로람다 같은 별도의 라이브러리 없이 Lambdas을 바로 사용할 수 있습니다.

- [Kotlin Lambdas](http://kotlinlang.org/docs/reference/lambdas.html) 문서

예를 들면 다음과 같은 코드를 바로 람다식으로 변환할 수 있습니다.

Android OnClick에 대한 기본 형태는 다음과 같습니다.

```java
button.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View view) {
        view.setAlpha(0.5f);
    }
});
```

위 코드를 코틀린의 람다식으로 변환하면 아래와 같습니다.

onClick의 View 변수를 사용하기 위해서는 아래와 같이 view -> view를 이용할 수 있습니다.

```java
button.setOnClickListener {
    view -> view.alpha = 0.5f
}
```

view -> view.의 형태를 좀 더 쉽게 사용하려면 아래와 같은 방법도 가능합니다.

kotlin에서는 it이라는 키워드를 제공합니다.

하나의 변수를 사용할 때 다음과 같이 it을 사용하면 동일한 방법의 코드를 좀 더 짧게 사용할 수 있습니다.

```java
button.setOnClickListener {
    it.alpha = 0.5f
}
```

**for 문에 필터 걸기**

for 문에 필터를 걸기 위해서는 아래와 같이 사용할 수 있습니다.

if 문에 5보다 큰 값에 대해서 * 2를 처리하고 이를 Log.로 출력하는 예제입니다.

```java
for (Integer integer : list) {
    if (integer > 5) {
        integer *= 2;
        Log.e("TAG", "Index " + integer);
    }
}
```

위의 코드를 람다식과 kotlin의 filter를 함께 사용하면 아래와 같이 간단하게 처리가 가능합니다.

```java
list.filter { it > 5 }.map { Log.d("TAG", "index " + (it * 2)) }
```

필터는 다음의 코드가 원문 코드입니다.

```java
/**
 * Returns a list containing only elements matching the given [predicate].
 */
public inline fun <T> Iterable<T>.filter(predicate: (T) -> Boolean): List<T> {
    return filterTo(ArrayList<T>(), predicate)
}
```

map의 코드도 다음과 같습니다.

```java
/**
 * Returns a list containing the results of applying the given [transform] function
 * to each element in the original collection.
 */
@Suppress("NON_PUBLIC_CALL_FROM_PUBLIC_INLINE")
public inline fun <T, R> Iterable<T>.map(transform: (T) -> R): List<R> {
    return mapTo(ArrayList<R>(collectionSizeOrDefault(10)), transform)
}
```

모두 Iterable을 사용하고 있고, Generics을 적절하게 만들어서 Collections 만들어져있습니다.

**코틀린 null 처리**

Kotlin에서는 List에 null이 포함될 수 있습니다. 하지만 null을 제거해야 할 경우가 있습니다.

안전한 null 처리에서 nullable 형태를 간단하게 null을 제외한 리스트로 만들 수 있었습니다.

```java
val list2: List<Int?> = listOf(1,2,null,3,4,5)
val intList: List<Int> = list2.filterNotNull()
intList.map { Log.d("TAG", "NotNull it " + it)}
```

filterNotNull을 이용해서 null 아이템을 제거할 수 있습니다.

```
// filterNot으로 출력한 결과
filterNotNull it 1
filterNotNull it 2
filterNotNull it 3
filterNotNull it 4
filterNotNull it 5
```

filterNot이라는 키워드도 제공됩니다. filterNot은 조건에 맞지 않은 것만 필터 가능합니다.

filterNot에 it == null 인 경우를 제외한 나머지를 출력하게 됩니다.

```java
val list2: List<Int?> = listOf(1,2,null,3,4,5)
list2.filterNot { it == null }.map { Log.d("TAG", "notNull it " + it) }
```

위의 코드를 실행하면 아래와 같이 notNull it에 1, 2, 3, 4, 5의 값이 출력됩니다.

```
// filterNot으로 출력한 결과
notNull it 1
notNull it 2
notNull it 3
notNull it 4
notNull it 5
```

java에서보다 filter를 이용해서 좀 더 편리하게 접근할 수 있습니다.

java8의 Stream을 이용하면 같은 방법으로 사용할 수 있습니다.

<br />

## String Interpolation

Java 문법대로 접근하면

```java
Log.d("TAG", "A : " + a + ", B : " + b);
```

+를 이용하여 문자 조합을 할 수 있습니다.

kotlin에서는 좀 더 쉽게 접근할 수 있는 방법을 제공합니다.

```java
Log.d("TAG", "A : $a, B : $b");
```

$를 이용하면 문자와 변수를 쉽게 조합할 수 있습니다.

map의 key, value를 사용하여 간단하게 출력이 가능합니다.

map의 key, value는 (k, v)로 간단하게 표현이 가능합니다.

```java
for ((k, v) in map) {
    println("$k -> $v")
}
```

<br />

## 읽기 전용 list/map 생성하기

kotlin에서는 읽기만 가능한 list와 map을 만들 수 있습니다.

```java
// Read-only list
val list = listOf("a", "b", "c")

// Read-only map
val map = mapOf("a" to 1, "b" to 2, "c" to 3)
```

list와 map을 위와 같이 만들게 되면 add는 하지 못하는 Read-only로 생성됩니다.

이를 출력하는 방법은 위에서 보았던 방법과 아래의 방법이 있습니다.

```java
val map = mapOf("A" to 1, "B" to 2, "C" to 3)

// forEach를 통해서 출력하는 방법
map.forEach { Log.i("TAG", "${it.key} -> ${it.value}") }

// 별도의 for를 이용하는 방법
for ((K, V) in map) {
    Log.d("TAG", "$K -> $V")
}
```

<br />

## 함수 - Infix notation(중위 표기법)

한국어로는 중위 표기법이라고 하는데 Kotlin은 이 중위 표기법(Infix notation)을 지원합니다.

**Infix notation을 사용할 경우는 this라는 키워드로 Int. 의 값을 접근할 수 있게 됩니다.**

```java
fun Int.max(x: Int):
  Int = if (this > x) this else x

1.max(15)
```

하나의 변수일 경우에는 infix 키워드를 추가하면 좀 더 단촐하게 줄여줄 수 있습니다.

```java
infix fun Int.max(x: Int):
  Int = if (this > x) this else x

1 max 15
```

infix 키워드를 추가하면 1 max 15라고 표현도 가능합니다.

저는 Utils에 다음과 같이 Bitmap을 사용할 경우 createBlurImage를 쉽게 접근할 수 있도록 만들어두었습니다.

this는 Bitmap?. 이 되는 것이고, Bitmap?을 return 하게 만들었습니다.

```java
fun Bitmap?.createBlurImage(
  context: Context,
  radius: Float = 25.0f): Bitmap? {
  return this?.let { // ...
    return bitmap
  }
}
```

Java와 코틀린에서 사용하는 방법

```java
// Kotlin
val bitmap: Bitmap? = null
bitmap.createBlurImage(context)

// Java
Bitmap bitmap = null;
RenderScriptUtilKt.createBlurImage(bitmap, context, 25.0f)
```

Java에서는 아래와 같이 입력해야 할 항목이 자동적으로 늘어나 보이게 됩니다.

![Screenshot 2016-08-02 23.44.03](/images/2016/2016-08-05-Kotlin-Idioms/Screenshot 2016-08-02 23.44.03.png)


<br />

## 마무리

Kotlin에서 자주 사용하는 표현들을 정리하였습니다.

일부는 작성하지 않았는데 kotlin의 문서를 참고하시면 좋을 것 같습니다.

- [Kotlin-Idioms 문서](http://kotlinlang.org/docs/reference/idioms.html)

저는 idioms에는 나와있지 않았지만 Infix notation을 추가로 정리해보았습니다.

사용하다 보니 Utils을 구현 및 사용이 편리하여 Utils에서 많이 사용하게 되는 것 같습니다.

다음 글에서는 class의 Generics에 대해서 정리해보려고 합니다.

제가 작성한 Kotlin Android 예제의 MVP 기본 코드를 기준으로 설명해보도록 하겠습니다.


<br />

## 코틀린 관련 포스트 목록

- [Android Kotlin  시작하기](http://thdev.tech/androiddev/kotlin/2016/07/31/Kotlin-Android-Start.html)
- [Kotlin 기본 문법 정리](http://thdev.tech/kotlin/2016/08/02/Basic-Kotlin-01.html)
- [Kotlin 안전한 null 처리](http://thdev.tech/kotlin/2016/08/04/Kotlin-Null-Safety.html)
- [Kotlin 코틀린의 주요 문법들]()
