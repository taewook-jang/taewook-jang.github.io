---
layout: post
title: Android Kotlin  시작하기
categories: [AndroidDev, Kotlin]
tags: [Android Kotlin, Android, Kotlin]
fullview: false
comments: true
published: true
---

Android는 Java 대신 다른 언어를 통해서도 개발할 수 있습니다.

그중 IntelliJ IDEA를 제작한 [JetBrains](https://www.jetbrains.com/) 에서 개발 배포 중인 Kotlin을 시작하는 방법을 정리하려고 합니다.

Android Studio 역시 IntelliJ IDEA를 베이스로 개발되어 있습니다.

JetBrains의 Plugin을 설치하고 사용이 가능합니다. Kotlin 역시 간단하게 설치하고, 지원이 가능하죠.

첫 번째로 안드로이드 샘플 프로젝트를 Kotlin으로 변환하는 방법을 간단하게 정리하고, 이후 기본적인 코틀린 문법을 설명하겠습니다.

- Kotlin 홈페이지 - [https://kotlinlang.org/](https://kotlinlang.org/)
- Try Kotlin - [http://try.kotlinlang.org/](http://try.kotlinlang.org/)

Try kotlin 사이트를 통해서 간단하게 Kotlin을 연습 해볼 수 있습니다.


<br />

## Kotlin 설치하기

Android Studio에서 Plugin을 설치하여 간단하게 Kotlin을 사용해볼 수 있습니다.

Android Studio의 설정 페이지를 실행합니다.<br />
MAC[command + ,] 을 눌러주면 실행됩니다.

아래와 같이 Plugins 페지에서 하단의 Browse respositories... 을 실행합니다.

![Screenshot 2016-07-31 15.43.11](/images/2016/2016-07-31-Kotlin-Android-Start/Screenshot 2016-07-31 15.43.11.png)

아래와 같이 뜨게 되면 추가로 Plugin을 설치할 수 있게 됩니다.

검색 부분에서 Kotlin을 입력하시면 Kotlin Plugin과 Extensions For Android 등이 표시되는데 이 중에서 Kotlin 만 설치해주시면 됩니다.

![Screenshot 2016-07-31 15.55.01](/images/2016/2016-07-31-Kotlin-Android-Start/Screenshot 2016-07-31 15.55.01.png)

설치에 따른 진행 과정이 아래와 같이 나타나게 됩니다.

설치가 완료되면 Android Studio을 재시작 해주시면 됩니다.

![Screenshot 2016-07-31 15.43.43](/images/2016/2016-07-31-Kotlin-Android-Start/Screenshot 2016-07-31 15.43.43.png)

<br />

**플러그인 직접 받기**

만약 플러그인을 직접 다운로드하고 싶으시다면 아래 사이트를 통해 받을 수 있습니다.

JetBrains - [Plugins : Kotlin](https://plugins.jetbrains.com/plugin/6954?pr=androidstudio)

**직접 받으실 때 Android Studio Plugins에 버전 정보가 표시됩니다. 그 정보에 맞게 직접 다운로드해주시면 됩니다.**

다운로드 완료되면 아래 화면과 같이 Install plugins from disk... 을 통해서 직접 설치가 가능합니다.(**다운로드한 Plugins은 별도 압축 풀지는 않아야 합니다.**)

![Screenshot 2016-07-31 16.16.14](/images/2016/2016-07-31-Kotlin-Android-Start/Screenshot 2016-07-31 16.16.14.png)

별도 창이 표시되면 아래와 같이 Downloads(다운로드) 폴더에서 확인할 수 있습니다.

![Screenshot 2016-07-31 16.19.03](/images/2016/2016-07-31-Kotlin-Android-Start/Screenshot 2016-07-31 16.19.03.png)

<br />

## Kotlin 플러그인 설치 확인

설치된 Kotlin은 아래와 같이 Tools/Kotlin 을 확인할 수 있습니다.

해당 메뉴를 통해서 Kotlin dependencies를 간단하게 추가할 수 있습니다.

추가하는 방법은 Kotlin convert에서 살펴보겠습니다.

![Screenshot 2016-07-31 17.13.05](/images/2016/2016-07-31-Kotlin-Android-Start/Screenshot 2016-07-31 17.13.05.png)

<br />

## Kotlin convert

Plugins 설치가 완료되었기에 Kotlin을 개발할 수 있는 환경이 만들어졌습니다.

Kotlin은 확장자가 **.kt** 가 되게 됩니다.

**별도의 Kotlin 폴더를** 지정하거나, **java 폴더아래에서 .java와 .kt를** 함께 사용할 수도 있습니다.

오늘은 kotlin convert를 사용하여 Java 파일을 Kotlin으로 Convert 할 수 있는 방법을 살펴보도록 하겠습니다.

<br />

**Android Studio를 통해서 새로운 프로젝트를 생성합니다.**

간단하게 샘플 프로젝트를 생성합니다.

![Screenshot 2016-07-31 17.11.49](/images/2016/2016-07-31-Kotlin-Android-Start/Screenshot 2016-07-31 17.11.49.png)

원하는 샘플 화면을 지정합니다. 저는 Basic Activity를 이용하였습니다.

![Screenshot 2016-07-31 17.12.02](/images/2016/2016-07-31-Kotlin-Android-Start/Screenshot 2016-07-31 17.12.02.png)

생성된 프로젝트의 MainActivity.java에서 다음과 같이 실행하여 Kotlin을 간단하게 변환할 수 있습니다.

**[command + shift + a]를** 눌러주면 되고, 메뉴는 아래와 같이 위치하고 있습니다.

![Screenshot 2016-07-31 17.18.08](/images/2016/2016-07-31-Kotlin-Android-Start/Screenshot 2016-07-31 17.18.08.png)

아래와 같이 Find Action... 창이 실행되면 "convert java"를 입력합니다.

![Screenshot 2016-07-31 17.14.03](/images/2016/2016-07-31-Kotlin-Android-Start/Screenshot 2016-07-31 17.14.03.png)

또 다른 방법으로는 메뉴 중 Code의 **Convert Java File to Kotlin File을**  직접 눌러주거나 아래의 단축키를 활용할 수 있습니다.

맥에서의 단축키는 **[command + shift + option + k]** 입니다.

![Screenshot 2016-07-31 17.15.16](/images/2016/2016-07-31-Kotlin-Android-Start/Screenshot 2016-07-31 17.15.16.png)

Convert 진행 중 별도 팝업으로 최종 확인이 가능합니다. OK를 눌러주면 **.kt** 파일이 생성됩니다.

![Screenshot 2016-07-31 17.28.07](/images/2016/2016-07-31-Kotlin-Android-Start/Screenshot 2016-07-31 17.28.07.png)

<br />

**dependencies 추가하기**

최초로 kotlin을 생성할 경우 dependencies가 추가되지 않은 상황이라서 아래와 같이 표시될 수 있습니다.

오른쪽 끝의 configure을 통해서 kotlin dependencies 추가를 바로 할 수 있습니다.

![Screenshot 2016-07-31 17.29.25](/images/2016/2016-07-31-Kotlin-Android-Start/Screenshot 2016-07-31 17.29.25.png)

또는 다음의 메뉴를 통해서 dependencies를 추가할 수 있습니다.

Tools > Kotlin > Configure Kotlin in Project 메뉴를 눌러서 활성화 가능합니다.

![Screenshot 2016-07-31 17.13.05 copy](/images/2016/2016-07-31-Kotlin-Android-Start/Screenshot 2016-07-31 17.13.05 copy.png)

Configure Kotlin은 아래와 같이 기본값으로 설정해주면 됩니다. Kotlin은 최신 버전이 1.0.3이 최신입니다.

![Screenshot 2016-07-31 17.32.08](/images/2016/2016-07-31-Kotlin-Android-Start/Screenshot 2016-07-31 17.32.08.png)

추가가 완료되면 build.gradle 파일에 각각 필요한 데이터가 추가되게 됩니다.

전체 Project와 해당 앱의 dependencies에 추가가 됩니다.

![Screenshot 2016-07-31 17.32.18](/images/2016/2016-07-31-Kotlin-Android-Start/Screenshot 2016-07-31 17.32.18.png)

추가 내용은 아래와 같습니다.

전체 프로젝트의 build.gradle

```
buildscript {
    ext.kotlin_version = '1.0.3'
    dependencies {
        // ...
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
        // ...
    }
}
```

실제 app의 build.gradle에는 아래와 같이 dependencies가 추가됩니다.

```
dependencies {
    // ...
    compile "org.jetbrains.kotlin:kotlin-stdlib:$kotlin_version"
}
```

위와 같이 추가되면 kotlin을 사용할 수 있는 환경이 만들어지게 됩니다.

<br />

## 변환된 Activity 코드 살펴보기

**상속 코드**

Java에서는 extends/implements를 통해서 상속을 받게 됩니다.

AppCompatActivity를 상속받아서 Activity를 구현하게 되고, 가장 기본 샘플은 아래와 같습니다.

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
        setSupportActionBar(toolbar);

        FloatingActionButton fab =
          (FloatingActionButton) findViewById(R.id.fab);
        fab.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Snackbar.make(view,
                  "Replace with your own action",
                  Snackbar.LENGTH_LONG)
                    .setAction("Action", null).show();
            }
        });
    }
}
```

위의 코드를 kotlin으로 상속을 받게 되면, 상속 개념은 아래와 같습니다.

:를 통해서 상속을 표시합니다. 만약 Interface와 Abstract를 여러 개 상속받게 되면

: Abstract(), Interface, Interface2 등과 같이 간단하게 상속 개념을 표시할 수 있습니다.

```java
class KotlinActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        val toolbar =
          findViewById(R.id.toolbar) as Toolbar
        setSupportActionBar(toolbar)

        val fab =
          findViewById(R.id.fab) as FloatingActionButton
        fab.setOnClickListener {
            view -> Snackbar.make(view,
              "Replace with your own action",
              Snackbar.LENGTH_LONG)
              .setAction("Action", null).show() }
    }
}
```

**findViewById**

kotlin의 변수 선언 방식이 java와는 다르게 됩니다. 기존 Script 형태의 언어를 사용해보셨으면 쉽게 이해할 수 있지만 처음 본다면 완전히 다른 형태의 코드로 보입니다.

```java
// java 형식
변수Type 변수명 = (변수 Type 형변환) findViewById()
// Kotlin 형식
val 변수명: 변수Type = findViewById() as 변수Type
val 변수명 = findViewById() as 변수Type
```

Kotlin은 Lambda 식을 기본으로 제공합니다.

Java에서는 람다식을 사용하기 위해서는 별도의 dependencies를 추가하여 Lambda를 사용하거나, Java 8 이상에서 사용할 수 있습니다.

Kotlin은 기본으로 제공하기에 아래와 같이 setOnClickListener을 축약할 수 있습니다. new View.OnClickListener을 따로 정의할 필요 없이 간단하게 사용할 수 있게 됩니다.

```java
// Java
fab.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View view) {
        Snackbar.make(view,
          "Replace with your own action",
          Snackbar.LENGTH_LONG)
            .setAction("Action", null).show();
    }
});

// Kotlin
fab.setOnClickListener {
    view -> Snackbar.make(view,
      "Replace with your own action",
      Snackbar.LENGTH_LONG)
      .setAction("Action", null).show() }
```

Kotlin의 경우 { 안에서 view -> view. }을 사용하지 않고 it이라는 키워드를 사용하여 바로 정의할 수도 있습니다.

```java
fab.setOnClickListener {
    Snackbar.make(it,
      "Replace with your own action",
      Snackbar.LENGTH_LONG)
      .setAction("Action", null).show() }
```

<br />

## 마무리

간단하게 kotlin으로 변환된 코드를 살펴보았습니다.

![Screenshot_1469955766](/images/2016/2016-07-31-Kotlin-Android-Start/Screenshot_1469955766.png)

저도 Convert와 검색, Kotlin 문서를 보면서 공부하고 있습니다.

그동안 공부하면서 꼭 알아두면 좋은 점들을 정리해두려고 합니다.

완전 Kotlin을 이용하여 프로젝트를 진행해보고, 그에 대한 필요한 개념을 정리해보도록 하겠습니다.

최종적으로는 Kotlin을 이용한 Adapter 사용 및 RxJava 등을 간단하게 사용하려고 합니다.


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
