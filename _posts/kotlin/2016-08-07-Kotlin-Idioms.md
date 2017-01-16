---
layout: post
title: Kotlin 기본 문법 - 2
categories: [Kotlin]
tags: [Kotlin]
fullview: false
comments: true
published: true
---

코틀린을 사용하기 위한 기본 문법 정리를 진행하였습니다.

이어서 좀 더 심화된 문법을 정리해보고자 합니다.

Kotlin에서 제공하는 Data class의 사용법과 함수에 default 정의 등을 알아보려고 합니다.

Kotlin 문서 중 Idioms를 참조하여 정리하였습니다.

- [Idioms 문서](https://kotlinlang.org/docs/reference/idioms.html)


<br />

## <a name="dto"></a>DTOs(Data Transfer Object)(POJOs/POCOs) 생성하기

- 참고자료 : [DTO, POJO에 대한 설명 - okky](http://okky.kr/article/115632)
- DTO : [Data Transfer Object](https://en.wikipedia.org/wiki/Data_transfer_object)
- POJO : [Plain Old Java Object](https://en.wikipedia.org/wiki/Plain_Old_Java_Object)
- POCO : [Plain Old CLR Object](http://stackoverflow.com/questions/3392580/a-better-explanation-of-poco)

코틀린을 사용하면 get/set을 별도로 생성해줄 필요가 없다고 합니다.

Kotlin에서 정의하면 java에서는 get/set이 자동으로 생성이 됩니다.

var과 val의 사용에 따라서 get/set 사용법이 다르게 됩니다.

var의 경우 Read/Write가 모두 가능하고, val을 사용하면 Read-only가 되게 됩니다.

우선 Java에서 사용하는 Data 정의는 2가지가 있습니다.

public을 이용하여 직접 접근하여 사용하거나, private으로 정의하고, get/set을 직접 또는 Annotation을 활용하여 구현하기도 합니다.

get/set을 만드는 건 귀찮은 작업입니다. Android Studio에서는 그나마 만들기 쉽지만 여하튼 귀찮은 작업입니다.

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

위의 코드는 get/set을 만들지 않고, public을 통해서 직접 접근하는 방법입니다.

다음은 get/set을 사용하는 캡슐화를 사용하는 방법입니다.

**Java9에서는 get/set의 생성을 좀 더 편하게 해준다고 합니다.**

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

Kotlin은 data class를 별도로 제공합니다.

`data class`를 data 키워드 없이 만들 수도 있지만 생성자를 통해서 값을 초기화하는 일반적인 방법에서는 다음과 같이 구현할 수 있습니다.

아래는 `val`로 변수를 선언하였기에 생성자에서 초기화 후에는 Read-only가 만들어집니다.

```Kotlin
data class Sample(val name: String, val email: String)
```

아래는 가장 일반적으로 정의해야 할 Read/Write가 가능한 `data class`입니다. 이때는 `var`로 초기화해야 합니다.

```kotlin
data class Sample(var name: String, var email: String)
```


<br />

**Java에서 val로 선언된 data class 접근 시**

Java에서 val을 사용하여 접근할 때는 다음과 같이 `Read-only`로 초기화되기 때문에 java에서 final을 사용한 초기화와 같습니다.

Kotlin에서 `val`의 `Sample`로 작성한 코드를 Java에서 사용하면 다음과 같이 초기화할 수 있습니다.

val이면서 NotNull이므로 2개의 키워드 모두 초기화해야 합니다.

![Screenshot 2016-08-02 21.48.56](/images/2016/2016-08-07-Kotlin-Idioms/Screenshot 2016-08-02 21.48.56.png)

val로 선언하였기에 `getName`, `getEmail` 만 접근할 수 있습니다.

![Screenshot 2016-08-02 21.49.06](/images/2016/2016-08-07-Kotlin-Idioms/Screenshot 2016-08-02 21.49.06.png)


<br />

**java에서 var로 선언된 data class 접근 시**

Java에서 선언된 Data 구조와 동일합니다.

get/set을 사용 가능합니다. 가장 일반적인 `data class`이므로 `var`로 값을 초기화해주시면 됩니다.

![Screen Shot 2016-08-05 at 6.14.21 PM](/images/2016/2016-08-07-Kotlin-Idioms/Screen Shot 2016-08-05 at 6.14.21 PM.png)

**val과 var을 잘 사용하게 되면, 값이 변경되지 않아야 하는 변수를 구분하여 초기화할 수 있습니다.**


<br />

**Kotlin에서 Sample 접근시**

코틀린에서는 `get/set`을 별도로 사용하지 않고, 직접 접근하여 사용합니다.

`var`과 `val`은 모두 구분이 되기 때문에 `val`로 초기화하면 값을 변경할 수 없는 건 Java와 동일합니다.

![Screenshot 2016-08-02 21.58.02](/images/2016/2016-08-07-Kotlin-Idioms/Screenshot 2016-08-02 21.58.02.png)


<br />

**data 클래스는 생성자로만 초기화할 수 있나?**

코틀린의 data 클래스는 최소 한 개의 값을 생성자에 추가해주어야 합니다.

`GSON` 또는 `jackson`을 사용하게 될 텐데 이때는 다음과 같이 구현할 일은 없을 것 같지만 다음과 같이 초기화도 가능합니다.

`data class`의 1개만 초기화하도록 하고, data를 다음과 같이 정의할 수 있습니다.

```java
data class Sample(var abc: String) {
    var name: String = ""
    var email: String = ""
}
```

`data 키워드`를 제외하고 일반적인 class를 다음과 같이 작성할 수 있습니다.

```java
class Sample() {
    var abc: String = ""
    var name: String = ""
    var email: String = ""
}
```

Kotlin을 Java에서 호출하여 사용하게 되면, `get/set`은 별도로 정의하지 않더라도 사용이 가능합니다.

- 나중에 별도 옵션으로 set 또는 get을 private으로 정의할 수도 있습니다.(이 경우는 private으로 정의하지 않은 값이 박에서 보일 수 있습니다.)

data class 사용에 대해서 좀 더 자세한 내용은 아래 문서를 참고하세요.

- [Data class 정의](https://kotlinlang.org/docs/reference/data-classes.html)


<br />

**함수에 기본값 정의**

함수 정의에서 기본값을 가지도록 정의할 수 있습니다.

C++을 배울 때 유용하게 사용하였던 Default 정의인데 kotlin에서도 사용이 가능하더군요.


Java에서 default를 비슷하게 구현하려면 Overloading을 이용하여 구현할 수 있습니다.

Overloading은 다음과 같이 Java 코드를 구현할 수 있습니다.

```java
public void add(String name) {
  add(name, "default");
}

public void add(String name, String email) {
  // ...
}
```

Kotlin에서는 위의 코드를 default로 정의할 수 있습니다.

```java
fun add(name: String, email: String = "default") {
  // ...
}
```

default 값은 `=`을 사용하여 값을 초기화합니다.

위의 add 함수는 name은 항상 사용해야 하며, email은 초기화하지 않을 경우 default가 자동으로 사용되게 됩니다.


<br />

**Kotlin에서 접근시**

Kotlin에서 접근하면 default 값을 별도로 입력하지 않아도 되며, 다음과 같이 초기화를 확인할 수 있습니다.

![Screenshot 2016-08-02 22.01.15](/images/2016/2016-08-07-Kotlin-Idioms/Screenshot 2016-08-02 22.01.15.png)

<br />

**Java에서 접근시**

Java에서 Kotlin에서 default로 정의한 함수를 접근하여도 default 값이 적용되지 않습니다.

![Screenshot 2016-08-02 22.01.52](/images/2016/2016-08-07-Kotlin-Idioms/Screenshot 2016-08-02 22.01.52.png)


아래와 같이 입력하지 않으면 해당 함수를 찾을 수 없다고 표시됩니다.

그래서 Java에서 default 값을 함께 사용하려면 Java의 Overloading을 Kotlin에서 구현해주어야 합니다.

![Screenshot 2016-08-02 22.02.04](/images/2016/2016-08-07-Kotlin-Idioms/Screenshot 2016-08-02 22.02.04.png)


**생성자에서도 동일하게 default 값 처리를 할 수 있으며, 이 역시 Kotlin에서만 default 값이 적용될 뿐 java에서는 모든 값을 입력해야 합니다.**

생성자 역시 Overloading으로 default 정의를 할 수 있도록 만들어야 합니다.


<br />

## <a name="lambdas"></a>Kotlin Lambdas

Kotlin은 Lambdas를 기본적으로 제공합니다. Java에서와 다르게 Java8을 이용하거나, 레트로람다 같은 별도의 라이브러리 없이 바로 사용이 가능합니다.

- [Kotlin Lambdas](http://kotlinlang.org/docs/reference/lambdas.html) 문서

예를 들면 다음과 같은 코드를 바로 람다식으로 변환할 수 있습니다.

Java의 Android OnClick에 대한 기본 형태는 다음과 같이 사용되게 됩니다.

```java
button.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View view) {
        view.setAlpha(0.5f);
    }
});
```

위 코드를 코틀린과 람다식으로 변환하면 아래와 같이 변경될 수 있습니다.

`{}`만 있으면 람다식이고, onClick 함수에는 view 변수를 가지기 때문에 `view -> ` 형태로 간단하게 변수를 받게 됩니다.

```java
button.setOnClickListener {
    view -> view.alpha = 0.5f
}
```

Kotlin에서는 `it` 키워드가 제공됩니다. `it`을 이용하면 별도로 `view -> `형태를 사용하지 않고도 더 간단하게 사용할 수 있습니다.

위의 코드를 `it` 키워드를 사용하여 다음과 같이 더 간단하게 작성할 수 있습니다.

**`it`의 경우는 함수의 변수가 한 개여야 만 허용됩니다.**

```java
button.setOnClickListener {
    it.alpha = 0.5f
}
```

<br />

## <a name="for"></a>for 문에 필터 걸기

for 문에 필터를 걸 수 있습니다.

kotlin에서는 `filter`라는 키워드를 제공하고 있지만, Java에서는 filter를 다음과 같이 구현할 수 있습니다.

5보다 큰 int에 대해서 `*2`를 처리하고, 이를 출력하는 코드입니다.

```java
for (Integer integer : list) {
    if (integer > 5) {
        integer *= 2;
        Log.e("TAG", "Index " + integer);
    }
}
```

위의 코트를 Kotlin의 람다식을 함께 사용하면 아래와 같이 정의할 수 있습니다.

`filter`에서 5 보다 큰 값을 핕터링 하게 되면, `map` 키워드에서 검출된 결과만 확인이 가능하며, 이를 출력하게 되는 코드로 Java와 똑같지만 `filter`가 if 문을 대체하게 됩니다.

```java
list.filter { it > 5 }.map { Log.d("TAG", "index " + (it * 2)) }
```

**filter 원문 코드는 다음과 같습니다.**

```java
/**
 * Returns a list containing only elements matching the given [predicate].
 */
public inline fun <T> Iterable<T>.filter(predicate: (T) -> Boolean): List<T> {
    return filterTo(ArrayList<T>(), predicate)
}
```

**map 원문 코드는 다음과 같습니다.**

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

<br />

## <a name="null"></a>코틀린 null 처리

List에서는 null이 포함될 수 있습니다.

`if 문`을 이용하여 null 처리를 할 수 있지만 kotlin의 list에는 `filterNotNull`이라는 키워드가 제공됩니다.

이 키워드를 사용하게 되면 아래와 같이 null 값을 제외한 새로운 List를 생성할 수 있습니다.

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

**filterNot**

`filterNot`이라는 키워드도 제공됩니다. `filterNot`은 조건에 맞는 값을 제외한 결과를 `map`에서 확인 가능합니다.

`filterNot`에서 null인 아이템을 제외하고, `map`에서 `Log`를 확인할 수 있습니다.

```java
val list2: List<Int?> = listOf(1,2,null,3,4,5)
list2.filterNot { it == null }.map { Log.d("TAG", "notNull it " + it) }
```

위의 코드를 실행하면 `filterNotNull`을 이용한 결과와 동일한 결과를 얻을 수 있지만 별도의 list가 만들어지는 것은 아닙니다.

```
// filterNot으로 출력한 결과
notNull it 1
notNull it 2
notNull it 3
notNull it 4
notNull it 5
```

java의 `if 문`을 대체하여 사용할 수 있는 키워드를 사용하면 간단하게 원하는 결과를 얻을 수 있습니다.

**Android에서 사용하는 List랑은 조금 다르므로 참고 해주세요.**


<br />

## <a name="string-interpolation"></a>String Interpolation

String Interpolation은 String 처리를 쉽게 할 수 있습니다.

Java에서 String을 합치는 방법은 `StringBuilder`를 이용하거나 `+`를 사용하게 됩니다.

```java
Log.d("TAG", "A : " + a + ", B : " + b);
```

`+`를 이용하여 문자를 조합하여 출력하게 됩니다.

kotlin에서는 `String Interpolation` 문법이 가능하므로 아래와 같이 `$`를 이용하여 변수를 바로 사용할 수 있습니다.

```java
Log.d("TAG", "A : $a, B : $b");
```

`$`를 이용하면 위의 `+`를 사용하지 않고도 String 합치는 게 가능합니다.

java에서는 아래와 같은 코드지만

```java
public String updateKeyword(String keyword) {
  return "Keyword : " + keyword;
}
```

String을 합치는 코드도 fun으로 다음과 같이 처리가 가능해집니다.

```java
fun updateKeyword(val: String?) = "Keyword : $val"
```

<br />

## <a name="map-key-value"></a>Map의 Key/Value를 출력하기

map에서 Key, value를 간단하게 출력할 수 있습니다.

람다식을 함께 사용함으로 아래와 같은 표현이 가능합니다.

```java
for ((k, v) in map) {
    println("$k -> $v")
}
```

<br />

## <a name="read-only"></a>읽기 전용 list/map 생성하기

kotlin에서는 읽기만 가능한 list와 map을 만들 수 있습니다.

```java
// Read-only list
val list = listOf("a", "b", "c")

// Read-only map
val map = mapOf("a" to 1, "b" to 2, "c" to 3)
```

list와 map을 위와 같이 만들게 되면 `add` 키워드는 존재하지 않는 Read-only list로 생성됩니다.

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

## <a name="infix-notation"></a>함수 - Infix notation(중위 표기법)

한국어로는 중위 표기법이라고 하는데 Kotlin은 이 중위 표기법(Infix notation)을 지원합니다.

**Infix notation을 사용할 경우는 this라는 키워드로 Int. 의 값을 접근할 수 있게 됩니다.**

```java
fun Int.max(x: Int):
  Int = if (this > x) this else x

1.max(15)
```

하나의 변수일 경우에는 infix 키워드를 추가하면 좀 더 단출하게 줄여줄 수 있습니다.

```java
infix fun Int.max(x: Int):
  Int = if (this > x) this else x

1 max 15
```

infix 키워드를 추가하면 `1 max 15`라고 표현도 가능합니다.

저는 Utils에 다음과 같이 Bitmap을 사용할 경우 `createBlurImage`를 쉽게 접근할 수 있도록 만들어두었습니다.

`this`는 `Bitmap?.` 이 되는 것이고, `Bitmap?`을 `return` 하게 만들었습니다.

```java
fun Bitmap?.createBlurImage(
  context: Context,
  radius: Float = 25.0f): Bitmap? {
  return this?.let { // ...
    return bitmap
  }
}
```

**Java와 코틀린에서 사용하는 방법**

```java
// Kotlin
val bitmap: Bitmap? = null
bitmap.createBlurImage(context)

// Java
Bitmap bitmap = null;
RenderScriptUtilKt.createBlurImage(bitmap, context, 25.0f)
```

Java에서는 아래와 같이 입력해야 할 항목이 자동적으로 늘어나 보이게 됩니다.

![Screenshot 2016-08-02 23.44.03](/images/2016/2016-08-07-Kotlin-Idioms/Screenshot 2016-08-02 23.44.03.png)


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
