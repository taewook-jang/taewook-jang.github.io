---
layout: post
title: Kotlin constructor init 이란?
categories: [Kotlin]
tags: [Kotlin, Android]
fullview: false
comments: true
published: true
---

코틀린에서 `constructor`생성자를 뜻합니다.

우선 Java에서의 생성자는 아래와 같이 `className(매개 변수)` 형태로 초기화합니다.

```java
public class Sample {

	private String name;
	private int age;
	private String birthday;

	public Sample(String name) {
		this.name = name;
	}

	public Sample(String name, int age) {
		this(name);
		this.age = age;
	}

	public Sample(String name, int age, String birthday) {
		this(name, age);
		this.birthday = birthday;
	}
}
```


<br />

## Kotlin constructor

[코틀린 클래스](http://thdev.tech/kotlin/2016/10/09/Kotlin-Class.html)에서 언급하였던 `constructor`에 대해서 정리하면 아래와 같습니다.

- constructor(생성자) 대신할 수 있음
- 하지만 생성자는 아님

```kotlin
class Sample constructor(val name: String) {

	constructor(name: String, age: Int): this(name)

	constructor(name: String, age: Int, birthday: String): this(name, age)
}
```

기본 constructor에서는 name을 초기화하고, 다음 constructor에서는 age를 추가하였고, birthday를 포함하는 생성자 3개를 적으면 Java 스타일처럼 만들어집니다.

kotlin에서만 사용한다면 아래와 같이 `default`를 이용한 생성자 함수 만드는게 가능합니다.

```kotlin
class Sample(val name: String, val age: Int = 0, val birthday: String = "") {
	// Do nothing
}
```

이렇게 정리함으로써 Java와는 다름을 알 수 있습니다.

- kotlin : default 변수 선언으로 3가지 생성자를 한 번에 정의 가능
- java : 3가지 초기화를 만들기 위해서는 `override`를 적용함으로 써 초기화를 구분할 수 있음.


<br />

## constructor 정리

kotlin을 통해서는 간단하게 `default` 사용으로 모든 것을 해결할 수 있습니다.

하지만 kotlin은 Java와 혼용하여 사용이 가능하고, kotlin 초기화 방식도 다릅니다.

그러므로 먼저 kotlin constructor가 어떤 것인지를 파악해야 합니다.

- `constructor`(생성자)처럼 사용이 가능하지만, 자바에서처럼 사용하는 생성자는 아님
- `init` block을 사용하여 초기화가 가능하지만, 생성자의 조건과는 다름

이러한 부분은 모두 생성자와는 거리가 멉니다. 중요한 것은 생성자처럼 사용은 가능하지만, 모두 생성자가 아님을 알아야 합니다.

Java에서는 각각이 생성자임과 동시에 초기화를 할 수 있습니다.

```java
public class Sample {
	private String name;
	private int age;

	public Sample(String name) {
		System.out.println("name " + name);
		this.name = name;
	}

	public Sample(String name, int age) {
		this(name);
		System.out.println("name " + name);
		this.age = age;
	}
}
```

위와 같은 형태의 생성자 만드는게 가능합니다.

kotlin에서는 우선 위와 같은 형태로 만들고, 원하는 대로 동작하는지를 살펴보기 위해서 다음의 코드를 생성해보았습니다.

```kotlin
class Sample constructor(val name: String) {

	constructor(name: String, age: Int): this(name)

	constructor(name: String, age: Int, birthday: String): this(name, age)
}
```


<br />

## 로그로 확인하겠습니다.

java 스타일대로 작성하고, 이를 아래와 같이 로그로 출력해보았습니다.

```kotlin
class Sample {

    constructor(name: String) {
        println("name $name")
    }

	constructor(name: String, age: Int): this(name) {
        println("name $name, age $age")
    }

	constructor(name: String, age: Int, birthday: String): this(name, age) {
        println("name $name, age $age birthday $birthday")
    }
}
```

위의 코드를 출력하면 다음과 같이 동작합니다.

```
name a // constructor 첫 번째

name b // constructor 첫 번째
name b, age 10 // constructor 두 번째

name c // constructor 첫 번째
name c, age 20 // constructor 두 번째
name c, age 20 birthday 2017-01-01 // constructor 세 번째
```

순서대로 불리므로, 마지막 constructor은 3가지 생성자 모두 호출이 일어납니다.

하지만 각각의 변수를 모두 사용하는 것은 불가능합니다.


<br />

## java 스타일로 멤버 변수 초기화

java 스타일로 먼저 멤버 변수를 생성하고, 이를 초기화해보겠습니다.

`var name, age, birthday`를 각각 생성하고, 이를 초기화하면 아래와 같습니다.

```kotlin
class Sample {

    var name: String = ""
    var age: Int = 0
    var birthday: String = ""

    constructor(name: String) {
        this.name = name
    }

	constructor(name: String, age: Int): this(name) {
        this.age = age
    }

	constructor(name: String, age: Int, birthday: String): this(name, age) {
        this.birthday = birthday
    }
}
```

위와 같이 초기화 함으로 써 java처럼 사용이 가능해집니다.


<br />

## init을 포함할 경우

이제 init 블락을 추가해보겠습니다.

init 블락은 생성자는 아니라고 위에서 말씀드렸습니다.

init 포함 샘플은 다음과 같이 생성할 수 있습니다.

```kotlin
class Sample {
    var name: String = ""
    var age: Int = 0
    var birthday: String = ""

    constructor(name: String) {
        println("name $name")
        this.name = name
    }

	constructor(name: String, age: Int): this(name) {
        println("name $name, age $age")
        this.age = age
    }

	constructor(name: String, age: Int, birthday: String): this(name, age) {
        println("name $name, age $age birthday $birthday")
        this.birthday = birthday
    }

    init {
			println("init!!!! name $name, age $age, birthday $birthday")
    }
}
```

위의 코드를 java 스타일대로 만들었습니다.

이런 결과를 java에서처럼 생각해보면, init 블락의 결과는 다음과 같아야 한다고 생각할 수 있습니다.

```
init!!!! name a, age 0 birthday
init!!!! name b, age 10 birthday
init!!!! name c, age 20 birthday 2017-01-01
```

하지만 실제 결과는 아래와 같습니다.

```
init!!!! name , age 0 birthday
init!!!! name , age 0 birthday
init!!!! name , age 0 birthday
```

위와 같은 결과를 얻었습니다.

init의 호출 시점을 알아보기 위해서 전체 로그를 확인해보겠습니다.

```
init!!!! name , age 0, birthday
name a

init!!!! name , age 0, birthday
name b
name b, age 10

init!!!! name , age 0, birthday
name c
name c, age 20
name c, age 20 birthday 2017-01-01
```

생각했던 것과 다르게 init이 먼저 호출됩니다.

그리고 첫 번째 constructor, 2번째 constructor, 3번째 constructor이 순서대로 호출이 일어납니다.


<br />

## init의 호출 시점은

네 다시 정리하면 init은 최상위 클래스가 호출됨과 동시에 init이 호출됩니다.

위에서 작성한 코드에는 1가지 생성자가 제외되어 있습니다.

바로 `constructor()`입니다.

결국 kotlin은 `constructor()`이 호출됨과 동시에 `init`이 호출되는 것을 확인할 수 있습니다.

3번째 constructor(name, age, birthday) 가 호출되면 다음과 같이 호출이 일어납니다.

- constructor(name, age, birthday)
- this(name, age)
- this(name)
- this()
- init {}
- this(name) { // 동작 }
- this(name, age) { // 동작 }
- constructor(name, age, birthday) { // 동작 }

위와 같은 순서대로 호출이 일어나게 됩니다. 결국 `init {}`이 가장 먼저 동작하고, 그다음 차례대로 동작하게 됩니다.

그래서 java에서처럼 생성자를 사용하게 되면 실수와 오류가 발생하게 됩니다.


<br />

## constructor에서 val? var? 사용하기

constructor에서는 최상위 생성자를 제외한 나머지 생성자에서는 `val, var` 사용이 불가능합니다.

그래서 다음과 같이 초기화해주는 것이 필요합니다.

```kotlin
class Sample constructor(val name: String, val age: Int, val birthday: String)
```

또는 constructor 생략

```kotlin
class Sample(val name: String, val age: Int, val birthday: String)
```

또는 위에서 java 스타일로 했던 var 정의를 통해서 생성자 사용이 가능합니다.


<br />

## kotlin constructor와 init의 올바른 사용 방법

constructor와 init을 모두 합치고, java와 함께 사용하려면 다음과 같은 방법으로 생성자 사용이 가능합니다.

최상위 생성자에는 `val name, val age, val birthday`를 추가합니다.

그리고 이어서 2번째와 3번째 생성자를 초기화합니다.

마지막으로 `init {}` 블락에서 각종 초기화를 진행하면 원하는 결과를 얻을 수 있습니다.

```kotlin
class Sample constructor(val name: String, val age: Int, val birthday: String) {

	constructor(name: String, age: Int): this(name, age, "default")

	constructor(name: String): this(name, 0, "default")

    init {
        println("name $name age $age birthday $birthday")
    }
}
```

그래서 위의 코드를 최종적으로 실행하면!

이제 java처럼 함수 override도 하였고, kotlin 방식을 모두 따르면서 `init {}` 블락 까지 모두 사용한 형태가 만들어졌습니다.

```
name a age 0 birthday default
name b age 10 birthday default
name c age 20 birthday 2017-01-01
```


<br />

## 마무리

kotlin에서의 constructor와 init은 java에서 보았던 것과는 다릅니다.

그래서 별도의 글을 통해서 정리해보려고 합니다.

조금은 어렵고, 순서 자체도 거꾸로 생성자를 만들어야 합니다.

위에서 예로 들었던 다음만 이해하시면 constructor, init 사용이 자유롭습니다.

- constructor(name, age, birthday)
- this(name, age)
- this(name)
- this()
- init {}
- this(name) { // 동작 }
- this(name, age) { // 동작 }
- constructor(name, age, birthday) { // 동작 }

결국 java와 함께 사용하려면 .. 어쩔 수 없이 알아야 하는게 많네요

kotlin에서는 다 불 필요하고 다음 코드면 생성자 사용이 쉬운데 말이죠

```kotlin
class Sample(val name: String, val age: Int = 0, val birthday: String = "default") {

    init {
        println("name $name age $age birthday $birthday")
    }
}
```

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
