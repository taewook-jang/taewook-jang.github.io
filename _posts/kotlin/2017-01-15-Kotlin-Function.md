---
layout: post
title: Kotlin Function - default, 변수 순서 변경
categories: [Kotlin]
tags: [Kotlin, Android]
fullview: false
comments: true
published: true
---

코틀린 함수에 대해서 정리합니다.

- default 지정 함수 및 변수 지정
- 변수 순서 변경
- Infix notation


<br />

## default 지정 함수 및 변수 지정

default 지정을 통해 초깃값을 지정할 수 있습니다.

```kotlin
fun setUserInfo(name: String = "Name", email: String, phone: String = "000-0000-0000") {
	this.name = name
	this.email = email
	this.phone = phone
}
```

위와 같이 초깃값을 지정할 수 있습니다.

`setUserInfo`를 사용할 때 다음과 같이 지정하면 오류가 발생합니다.

```kotlin
setUserInfo("Email")
```

![kotlin_error_01]

초깃값을 지정할 수 있지만, 위와 같이 자동으로 순서 보장이 일어나지 않습니다.

그래서 아래와 같이 지정할 수 있습니다.

```kotlin
setUserInfo("Name") // Error
setUserInfo("Name", "email") // 정상
setUserInfo("Name", "email", "Phone") // 정상
```

위와 같습니다. 1 번째 줄은 사용이 불가능합니다.

그래서 다음과 같은 문법을 통해서 해결할 수 있습니다.

```kotlin
setUserInfo(email = "email")
```

이렇게 지정하면 `email` 변수에만 값을 지정하고, 나머지는 `default` 값으로 처리함을 나타냅니다.


<br />

## 변수 순서 변경

위와 같이 default로 값을 처리하고, `email = "email"` 형태로 값을 지정할 수 있습니다.

그래서 좀 더 유용한 방법도 있습니다.

변수가 추가되었을 때를 대비해서 다음과 같은 방법으로 코딩을 하게 되어 나중에 좀 더 편할 수 있습니다.

```kotlin
setUserInfo(
	phone = "000-0000-000",
	email = "email@email.com",
	name = "User Name")
```

위와 같은 형태로 변수를 호출할 수 있습니다.

변수 명을 앞에 주고, 값을 입력하는 방식을 사용할 수 있어, 변수 순서를 맘대로 지정할 수 있습니다.

```kotlin
setUserInfo(
	email = "email@email.com",
	name = "User Name")
```


<br />

## Infix notation

이전 글에서 간단하게 [Infix notation](http://thdev.tech/kotlin/2016/08/07/Kotlin-Idioms.html)을 다루었습니다.

몇 가지 추가로 정리합니다.

`Infix notation`은 다음과 같이 사용합니다.

```kotlin
fun AppCompatActivity.replaceContentFragment(@IdRes frameId: Int, fragment: android.support.v4.app.Fragment) {
    supportFragmentManager.beginTransaction().replace(frameId, fragment)?.commit()
}
```

`AppCompatActivity`를 상속받거나, `AppCompatActivity` 변수를 통해서 `replaceContentFragment`을 사용하게 됩니다.

좀 더 쉬운 걸로 이야기하면

```kotlin
fun Int.sum(y: Int) = this + y
```

이 경우도 역시 `Int`를 상속받는 클래스이거나, `Int` 변수를 통해서 접근할 수 있습니다.

이를 Java에서 사용하게 되면, `sum(1, 2)`라고 사용하게 됩니다.

kotlin에서는 당연히 null을 허용하지 않으므로, java에서는 `@NotNull`이어야 합니다.


<br />

### Infix notation에서 default 사용하기

위에서 정리했던 2가지를 모두 포함할 수 있습니다.

```kotlin
fun Int.sum(y: Int = 0, z: Int) = this + y + z
```

호출 시에는 다음과 같이 합니다.

```kotlin
15.sum(z = 15) // 15 + 0 + 15 = 30
15.sum(y = 5, z = 3) // 15 + 5 + 3 = 23
```


<br />

## 마무리

함수 사용에 대해서 추가로 정리해 보았습니다.

default를 통해서 값을 정할 수 있는데 변수의 순서도 변경하면서 값을 사용할 수도 있습니다.

그런 부분을 정리해보았습니다.

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


[kotlin_error_01]: /images/kotlin/2017/2017-01-15-Kotlin-Function/kotlin_error_01.png
