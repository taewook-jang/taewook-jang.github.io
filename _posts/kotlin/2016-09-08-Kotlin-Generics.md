---
layout: post
title: Kotlin Generics 사용하기
categories: [Kotlin, Android]
tags: [Kotlin]
fullview: false
comments: true
published: true
---

코틀린의 Generics을 정리하였습니다. 코틀린의 Generics은 Java에서 사용하는 것과 동일합니다.

다만 Kotlin에서 생성한 Generics을 Kotlin의 클래스에서 사용하게 되면 명시적으로 써주어야 하는 점을 살펴보려고 합니다.

- [Kotlin Generics 문서](https://kotlinlang.org/docs/reference/generics.html)
- [참고 사이트 - 일본어 페이지라 한글/영어로 번역해서 보세요](https://sites.google.com/site/tarokotlin/chap4/sec46)


<br />

## Kotlin에서 Generic 작성하기

Kotlin에서도 Generic을 제공하고 있습니다. 일반적인 `Java interface`에서는 아래와 같이 작성할 수 있습니다.

```java
interface Generic<T> {
  void setItem(T item);
}
```

코틀린의 문법에 맞게 작성한다면 다음과 같겠습니다.

```
interface Generic<in T> {
  fun setItem(item: T)
}
```

간단한 Generic 코드를 표현할 수 있습니다.

저는 이 `Generic`을 이용하여 `AndroidBase`를 작성하였습니다.

2번째 글로 `Presenter/View`에 대해서 작성하려고 하였는데 우선 `Generic`을 정리하는게 좋겠다는 생각에 다음을 정리하였습니다.


<br />

## Generic 추론

Kotlin에서의 특징인 추론을 Generic에서도 활용할 수 있습니다.

기본적으로 `변수`에 `Generic` 타입을 정의하지 않는다면 강제이겠지만, 다음과 같은 클래스 `Foo`처럼 작성한다면 `Generic`을 강제하지 않고 사용이 가능합니다.

```
class Foo<T>(val t : T)

fun foo() {
  val foo1 = Foo<String>("foo1")
  val foo2 = Foo("foo2")
}
```

위의 경우 `T`를 `String`으로 표현되게 됩니다.

`foo2`에서처럼 변수가 이미 `String`임을 짐작할 수 있는 형태에서는 별도의 `Generic`을 정의하지 않아도 `String`임을 짐작할 수 있게 됩니다.


<br />

## Wildcard type argument in Java

Java에서는 와일드카드라 불리는 부분이 있습니다.

- `? extends T` : read만 가능한 서브타입 와일드카드입니다.
- `? super T` : write만 가능한 슈퍼 타입 와일드카드입니다.


다음의 예제는 아래의 `interface`을 정의하였습니다.

```java
interface Output<T> {
  boolean isArgument(T argument);
}
```

사용하는 데이터는 다음의 `ArrayList`에 `Output<String>`을 정의하였습니다.

```java
ArrayList<Output<String>> items = new ArrayList<>();
items.add(new Output<String>() {
    @Override
    public boolean isArgument(String argument) {
        return true;
    }
});
items.add(new Output<String>() {
    @Override
    public boolean isArgument(String argument) {
        return false;
    }
});
```


<br />

## Wildcard type argument - `? extends T`

`? extends T`는 읽기 전용 와일드카드입니다.

읽기 만 가능하기 때문에 수정이 불가능하여 `T`를 안전하게 처리가 가능한 형태입니다.

다음과 같이 `printAll`을 정의였는데 `? extends Output<String>`을 선언하였기 때문에 `add`를 호출할 경우 Error가 발생합니다.

다만 `null`을 허용하고 있어서 `null`을 포함할 수 있습니다.

```java
private void printAll(ArrayList<? extends Output<String>> items) {
    // Error...
    // items.add(new Output<String>() {
    //     @Override
    //     public boolean isArgument(String argument) {
    //         return false;
    //     }
    // });

    for (int i = 0; i < items.size(); i++) {
        if (items.get(i).isArgument("")) {
            Log.d("TAG", "item : " + items.get(i));
        }
    }
    items.add(null);
}
```

위를 실행하면 다음과 같은 결과를 확인할 수 있습니다.

```
items.add(new Output<String> ...)  // Error
items.get(0) // success - print
items.get(1) // success - Not print
items.add(null) // add success
```


<br />

## Wildcard type argument - `? super T`

이번에는 `super`입니다. 이 경우는 `extends`와 반대로 `쓰기`만 가능합니다.

아래와 같이 `printAll`을 추가하였고 `? super Output<String>`을 선언하였습니다.

이 경우 `get`을 통한 접근이 불가능함을 확인할 수 있습니다.

```java
private void printAll(ArrayList<? super Output<String>> items) {
    items.add(new Output<String>() {
        @Override
        public boolean isArgument(String argument) {
            return true;
        }
    });
    items.add(null);

    // Error...
    // for (int i = 0; i < items.size(); i++) {
    //     if (items.get(i).isArgument("")) {
    //         Log.d("TAG", "item : " + items.get(i));
    //     }
    // }
}
```

위의 테스트 결과는 다음과 같습니다.

- 마지막의 `items.get`을 하는 경우 오류가 발생하게 됩니다.

```
// printAll
items.add(new Output<String> ...) // add success
items.add(null) // add success

items.get // Error
```


또 다른 방법으로 아래와 같이 정의도 가능합니다.

Output 객체에 `? super Output<String>`을 별도로 정의하여 `filter`처럼 사용이 가능합니다.

```java
private void printAll(ArrayList<Output<String>> items, Output<? super Output<String>> filter) {
    for (int i = 0; i < items.size(); i++) {
        if (filter.isArgument(items.get(i))) {
            Log.d("TAG", "item : " + items.get(i));
        }
    }
}
```

여기까지가 Java의 `subType` 와일드카드와 `superType` 와일드카드에 대한 정의입니다.


<br />

## Kotlin의 in/out

Kotlin에서는 `in/out`이라는 키워드를 제공합니다. 이 키워드는 다음과 같습니다.

- `in T` : Java의 `? super T`에 해당하고, `input`의 약자입니다.
- `out T` : Java의 `? extends T`에 해당하고, `output`의 약자입니다.

Kotlin에서는 다음과 같이 `interface`를 정의하였습니다.

```
internal interface Output<T> {
    fun isArgument(argument: T): Boolean
}
```

사용하는 ArrayList 데이터는 아래와 같습니다.

```
val items = ArrayList<Output<String>>()
items.add(object : Output<String> {
    override fun isArgument(argument: String) = false
})
items.add(object : Output<String> {
    override fun isArgument(argument: String) = true
})
```


<br />

## Kotlin - out

Kotlin에서는 `? extends T`를 `out`이라는 키워드가 대체하게 됩니다.

`out`은 `output`의 약자인데 다음과 같이 `printAll`을 구현할 수 있습니다.

```
private fun printAll(items: ArrayList<out Output<String>>) {
  // Error...
  // items.add(new Output<String>() {
  //     @Override
  //     public boolean isArgument(String argument) {
  //         return false;
  //     }
  // });

  for (i in items.indices) {
      if (items[i].isArgument("")) {
          Log.d("TAG", "item : " + items[i])
      }
  }
// items.add(null)
}
```

위의 예제를 실행하면 다음과 같습니다.

kotlin은 `null`을 허용하지 않는 언어이기에 `items.add(null)`을 하려고 하면 오류가 발생합니다.

```
items.add(new Output<String> ...)  // Error
items.get(0) // Success - print
items.get(1) // Success - not print
items.add(null) // Error
```


<br />

## Kotlin - in

Kotlin에서는 `? super T`를 `in` 키워드를 사용하게 됩니다.

`in`은 `input`의 약자인데 다음과 같이 `printAll`을 구현할 수 있습니다.

```
private fun printAll(items: ArrayList<in Output<String>>) {
  items.add(object : Output<String> {

      override fun isArgument(argument: String) = true
  })

  //        for (int i = 0; i < items.size(); i++) {
  //            if (items.get(i).isArgument("")) {
  //                Log.d("TAG", "item : " + items.get(i));
  //            }
  //        }
  // items.add(null)
}
```

Kotlin에서는 다음과 같은 결과를 가집니다.

역시 `null`을 허용하지 않으므로 `add(null)`은 오류가 발생합니다.

```
// printAll
items.add(object : Output<String> ...) // add success
items.add(null) // Error

items.get() // Error
```

`in`의 또 다른 표현은 아래와 같이 할 수 있습니다.

```
private fun printAll(items: ArrayList<Output<String>>, filter: Output<in Output<String>>) {
    for (i in items.indices) {
        if (filter.isArgument(items[i])) {
            Log.d("TAG", "item : " + items[i])
        }
    }
}
```


<br />

## in과 out을 쓰지 않는 경우

`in`과 `out`이 코틀린에서는 명시적으로 사용됩니다. 다음의 경우는 `in/out`을 사용하지 않아도 됩니다.

```
class Array<T>(val size: Int) {
  fun get(index: Int): T { /* ... */ }
  fun set(index: Int, value: T) { /* ... */ }
}
```

위의 경우 `input`과 `output`이 모두 일어나는 경우라서 별도의 `in/out` 키워드가 필요하지 않는 일반적인 Generic 형태가 되겠습니다.


<br />

## Nothing

Nothing을 kotlin에서는 `Any`라는 키워드를 사용하게 됩니다.

이 부분은 코틀린 사이트에 나온 예제를 가지고 설명하겠습니다.

from의 Any를 to Any로 복사하게 되는 예제입니다.

```
fun copy(from: Array<Any>, to: Array<Any>) {
  assert(from.size == to.size)
  for (i in from.indices)
    to[i] = from[i]
}
```

다음과 같이 Int array와 Any array를 생성합니다.

**Android에서는 오류가 발생합니다.**

```
val ints: Array<Int> = arrayOf(1, 2, 3)
val any = Array<Any>(3)
copy(ints, any) // Error: expects (Array<Any>, Array<Any>)
```

여기에서 문제가 발생한다고 합니다. copy의 from의 ints에 대한 `ClassCastException`이 발생합니다.

생성한 `ints`는 `Int` Array이고, `copy`에서 사용하는 `from`은 `Any`로 받고 있습니다.

any에서 ints로 복사하는 과정에 `ints`의 데이터를 유효하게 처리하도록 하기 위함이라고 합니다.

안전한 복사?를 위해서 `ClassCastException`이 발생하는 것인데 다음과 같이 해결하여, `from`의 데이터를 별도의 가공을 하지 않고 `to`에 복사할 수 있도록 해주면 된다고 합니다.

아래와 같이 `from`에 `out`을 포함하여 데이터 수정을 하지 못하도록 처리하면 됩니다.

```
fun copy(from: Array<out Any>, to: Array<Any>) {
}
```

**써놓고 보니 어려운데 하나 더 보여드리면 다음과 같은 경우입니다.**

dest의 ArrayList에 값을 `add`해야 하는 경우입니다. dest의 기존 데이터를 컨트롤하지 못하도록 하여 무결성을 유지할 수 있게 됩니다.

```
fun fill(dest: ArrayList<in String>, value: String) {
    dest.add(value)
}
```


<br />

그리고 가장 많이 보게 될 부분이 Kotlin에서는 `*`입니다.

Java에서는 이 경우 `object`로 표현을 하게 됩니다.

Generic을 아무것이나 사용하여도 된다면 다음과 같이 생략이 가능하며, 생략하게 되면 자동으로 `Object`로 초기화됩니다.

```
ArrayList<Output> items = new ArrayList<>();
items.add(new Output() {
    @Override
    public boolean isArgument(Object argument) {
        return false;
    }
})
```

하지만 Kotlin에서는 아래와 같이 사용해야 합니다.

Kotlin에서는 명시적으로 Generic을 초기화하지 않을 수 없게 되어 있습니다.

그래서 `Object`와 같은 형태로 사용하려면 다음과 같이 사용해야 합니다.

- Generic에는 `*`으로 초기화합니다.
- 실제 변수에서는 `Any`을 사용하게 됩니다.
- `Any`로 초기화 시에는 다양한 값이 포함될 수 있습니다.

```
val items = ArrayList<Output<*>>()
items.add(object : Output<Any> {
    override fun isArgument(argument: Any) = true
})
```

`interface Function<in T, out U>`라고 정의하였을 경우에는 다음의 의미를 가질 수 있습니다.

그중 `*`으로 초기화 시에는 `in Nothing` 또는 `out Any`로 표현이 가능합니다.

- `Function<*, String>` : `Function<in Nothing, String>`
- `Function<Int, *>` : `Function<Int, out Any?>`
- `Function<*, *>` : `Function<in Nothing, out Any?>`


<br />

## Generic Function

Generic 함수도 선언이 가능합니다. 사용 방법은 Java와 동일합니다.

아래와 같이 `abc`를 Generic으로 선언하였습니다.

```
private fun <T> abc(t: T) {
    ...
}
```

이 경우 다음과 같이 사용할 수 있습니다.

```
abc<String>("ABC")
abc("ABC")
```

의미상 생략도 가능합니다.

`abc`를 호출하여 `ArrayList`를 생성하는 함수입니다.

```
private fun <T> abc(): List<T> {
    return ArrayList()
}
```

이 경우는 별도의 생략을 하지 못하고 다음과 같이 사용하여야 합니다.

```
abc<String>()
```


<br />

## Kotlin의 Generic

Kotlin은 Generic을 사용하게 되면 생략을 할 수 있는 경우와 하지 못하는 경우가 확연하게 구분됩니다.

- `생략 가능한 경우` : Generic을 선언하고, 변수로 사용하는 경우

```
class Foo<T>(val t : T)

fun foo() {
  val foo1 = Foo<String>("foo1") // 생략 가능
  val foo2 = Foo("foo2")  // 선언 가능
}
```

- `생략이 불가능한 경우` : 별도의 변수에 T를 담지 않는다면 생략이 불가능하게 됩니다.

```
class Foo<T>

fun foo() {
  val foo1 = Foo<String>() // 생략 불가능
  val foo2 = Foo()  // 생략 불가능으로 오류 발생
}
```

생략이 불가능하므로 어떻게 보면 명확하게 구분할 수 있습니다.

Java에서처럼 자동으로 `Object` 타입을 만들어주는것이 아닙니다.

만약 Java의 `Object`처럼 보여주고 싶은 경우라면 다음과 같이 할 수 있습니다.

```
class Foo<T>

fun foo() {
  val foo1 = Foo<Any>()
}
```


<br />

## 마무리

코틀린에서는 생략이 불가능한 경우가 더 많기에 이런 부분이 조금 귀찮을 수 있습니다.

저도 이런 부분을 잘 우려 넣어 [`AndroidBase`](https://github.com/taehwandev/AndroidBase)를 작성하였습니다.

다음 글에서 `Presenter/View`에 대해서 설명을 하게 되겠습니다.

이 글을 먼저 작성한 이유는 Generic 표현을 알고 있어야 `Presenter/View` 부분을 좀 더 쉽게 접근할 수 있으리라고 생각하여, 다음을 작성하였습니다.

읽어주셔서 감사합니다.


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
