---
layout: post
title: Kotlin Class 살펴보기
categories: [Kotlin]
tags: [Kotlin, Android]
fullview: false
comments: true
published: false
---

코틀린 함수에 대해서 정리합니다.

- default 지정 함수 및 변수 지정
- 변수 순서 변경
- Infix notation


<br />

## default 지정 함수 및 변수 지정

default 지정을 통해 초기값을 지정할 수 있습니다.

```kotlin
fun setUserInfo(name: String = "Name", email: String, phone: String = "000-0000-0000") {
	this.name = name
	this.email = email
	this.phone = phone
}
```

위와 같이 초기값을 지정할 수 있습니다.

`setUserInfo`를 사용할 때 다음과 같이 지정하면 오류가 발생합니다.

```kotlin
setUserInfo("Email")
```

![kotlin_error_01]

초기값은 지정할 수 있지만, 위와 같이 자동으로 순서 보장이 일어나지 않습니다.

그래서 아래와 같이 지정할 수 있습니다.

```kotlin
setUserInfo("Name") // Error
setUserInfo("Name", "email") // 정상
setUserInfo("Name", "email", "Phone") // 정상
```

위와 같습니다. 1 번째 줄은 사용이 불가능합니다.

그래서 다음과 같은 문법을 통해서 해결 할 수 있습니다.

```kotlin
setUserInfo(email = "email")
```

이렇게 지정하면 `email` 변수에만 값을 지정하고, 나머지는 `default` 값으로 처리함을 나타냅니다.


<br />

## 변수 순서 변경




[kotlin_error_01]: /images/kotlin/2017/2017-01-15-Kotlin-Function/kotlin_error_01.png
