---
layout: post
title: Android WebView TestCode 작성하기 확장(Alert, Script 만들어 테스트)
categories: [AndroidDev]
tags: [Android, AndroidDev]
fullview: false
comments: true
published: false
---

Android WebView TestCode 작성 2번째 글입니다.

앞에서는 기본적인 WebView 테스트 코드들을 살펴보았고, 이번 글에서는 javascript을 직접 만들어서 테스트하거나, Alert을 자동으로 테스트할 수 있는 방법 등을 살펴보겠습니다.

Alert을 자동으로 테스트하기 위한 uiautomator에 대해서는 추후 좀 더 자세한 글로 작성될 수 있어서 간단하게 WebView에 맞는 테스트 방법을 소개하도록 하겠습니다.

사용한 dependencies는 다음과 같으며 이중 espresso와 mockito 관련 부분을 지난번 글에서 작성하였고 이번에는 uiautomator 적용 및 사용법과 javascript를 추가로 적용하여 테스트 하는 법 등을 살펴보겠습니다.

- `test runner`
- `test rule`
- `espresso` : UI 테스트를 위한 도구
- `uiautomator` : 시스템 UI를 테스트해볼 수 있음(Alert 등)
- `awaitility` : Thread sleep과 비슷함


<br />

## 관련 글

- [Android WebView javascriptInterface 사용하기](http://thdev.tech/androiddev/2016/08/11/Android-WebView-JavascriptInterface-Example.html)
- Android WebView TestCode 작성하기 기본 테스트 진행하기
- Android WebView TestCode 작성하기 확장(Alert, Script 만들어 테스트)
- Android WebView Kotlin Base 코드 정의 살펴보기
- Android WebView Kotlin TestCode 작성하기


<br />

## TestCode 작성에 사용한 API

다음의 Test API를 사용하였습니다.

- [Espresso](https://google.github.io/android-testing-support-library/docs/espresso/index.html)
- [AndroidJUnitRunner](https://google.github.io/android-testing-support-library/docs/androidjunitrunner-guide/index.html)
- [JUnit4 Rules](https://google.github.io/android-testing-support-library/docs/rules/index.html)
- [Dexmaker - Mockito](https://github.com/crittercism/dexmaker)
- [awaitility](https://github.com/awaitility/awaitility)
- [UI Automator](https://developer.android.com/topic/libraries/testing-support-library/index.html)


<br />

## Javascript 추가해서 테스트하기

Javascript을 추가해서 테스트를 진행해볼 수 있습니다. WebView에 굳이 URL이 없어도 동작을 하게 되는 이 테스트는 간단하게 Javascript을 직접 작성하여 웹에 추가되어 있지 않아도 동작 테스트를 해볼 수 있는 Test입니다.

script를 간단하게 작성하고 이가 동작하는지 테스트 가능하고, arguments를 넘겨주고 이를 단순 계산하고 return 받아서 값을 체크하는 등의 테스트가 가능합니다.

사실 쓸 일인 많지 않은것 같지만... 정리해두려고 합니다.


<br />

**ArrayList의 String 값을 지정하고, 0번째 값 받아오기**

ArrayList의 String을 넘겨주고, 0번째 값을 받아와서 webMatches를 활용해 값이 맞는지 확인하는 간단한 예제 코드입니다.

직접 다음과 같은 script를 작성합니다. 이 코드는 Web 샘플에는 존재하지 않지만 그냥 추가해줄수 있습니다.

arguments로 넘겨받은 값중 0번째 값을 그냥 return 합니다.

```java
String script = "function() { return arguments[0] }";
```

return한 결과는 `castOrDie`을 통해서 `String`으로 변환하고, 이를 `is`를 통해서 확인하는 단순한 코드입니다.

```java
@Test
public void testReturnValue() throws Throwable {
    waitWebViewLoad(MainFragment.DEFAULT_URL);

    String script = "function() { return arguments[0] }";

    onWebView()
            .check(webMatches(transform(scriptWithArgs(script,
                    Lists.newArrayList("String")), castOrDie(String.class)), is("String")));
}
```

return 값이 `String`이므로 오류 없이 동작합니다.


<br />

**ArrayList 값을 더하는 간단한 Script**

해당 코드는 Android platform WebView 테스트 코드에 있는 코드입니다.

arguments가 배열 형태로 들어가기 때문에 하나씩 뽑아와서 initial에 += 해주게 됩니다. 최종적으로 1, 2, 42, 7의 값을 더하기 때문에 52인 `Integer` 형태로 return 됩니다. 이를 `is()`를 통해서 확인하는 코드입니다.

```java
@Test
public void testAccumulate() throws Throwable {
    waitWebViewLoad(MainFragment.DEFAULT_URL);

    // Google Testing code
    // Writing using functions is nice for re-usable snippits of logic that vary by their
    // arguments.
    String accumulateFn = "function() {"
            + "  var initial = arguments[0];"
            + "  for (var i = 1; i < arguments.length; i++) { "
            + "    initial += arguments[i];"
            + "  }"
            + "  return initial;"
            + "}";
    onWebView()
            .check(webMatches(transform(scriptWithArgs(accumulateFn,
                    Lists.newArrayList(1, 2, 42, 7)),
                    castOrDie(Integer.class)),
                    is(52)));
}
```


<br />

### Alert 테스트 하기

Alert dialog 영역은 시스템 영역일 수 있고, 아닐 수도 있습니다. 사용자가 직접 누른다면 사실 테스트의미가 크지 않습니다. TestCode로 그걸 모두 커버하여 테스트할 수 있는 환경도 만들기 쉽지 않습니다.

UI Automator를 통해서 이를 해결할 수 있는데 문제는 API 18부터입니다. JellBean 4.3부터 이 프로세스를 제공하기 때문에 minSdk도 함께 올라가야 합니다. 안그럼 오류가 나더군요...

그래서 몇가지 설정을 해주어야 하는데 우선 UI Automator을 Android 6.0부터 적용된 Permissions에서 테스트 하는 방법을 장 정리해둔 글입니다.

- [Testing Runtime Permissions: Lessons Learned](http://blog.egorand.me/testing-runtime-permissions-lessons-learned/)

<br />

**UI Automator 설정하기**



</br >

## 마무리



<br />

## 관련 글

- [Android WebView javascriptInterface 사용하기](http://thdev.tech/androiddev/2016/08/11/Android-WebView-JavascriptInterface-Example.html)
- Android WebView TestCode 작성하기 기본 테스트 진행하기
- Android WebView TestCode 작성하기 확장(Alert, Script 만들어 테스트)
- Android WebView Kotlin Base 코드 정의 살펴보기
- Android WebView Kotlin TestCode 작성하기
