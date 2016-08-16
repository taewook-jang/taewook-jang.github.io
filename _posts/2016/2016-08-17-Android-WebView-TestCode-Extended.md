---
layout: post
title: Android WebView TestCode 작성하기 확장(Alert, Script 만들어 테스트)
categories: [AndroidDev]
tags: [Android, AndroidDev, WebView]
fullview: false
comments: true
published: true
---

Android WebView TestCode 작성 2번째 글입니다.

앞에서는 기본적인 WebView 테스트 코드들을 살펴보았고, 이번 글에서는 javascript을 직접 만들어서 테스트하거나, Alert을 자동으로 테스트할 수 있는 방법 등을 살펴보겠습니다.

Alert을 자동으로 테스트하기 위한 uiautomator에 대해서는 추후 좀 더 자세한 글로 작성될 수 있어서 간단하게 WebView에 맞는 테스트 방법을 소개하도록 하겠습니다.

사용한 dependencies는 다음과 같으며 이중 espresso와 mockito 관련 부분을 지난번 글에서 작성하였고 이번에는 uiautomator 적용 및 사용법과 javascript를 추가로 적용하여 테스트하는 법 등을 살펴보겠습니다.

- test runner
- test rule
- espresso : UI 테스트를 위한 도구
- uiautomator : 시스템 UI를 테스트해볼 수 있음(Alert 등)
- awaitility : Thread sleep과 비슷함


<br />

## 관련 글

- [Android WebView javascriptInterface 사용하기](/androiddev/2016/08/11/Android-WebView-JavascriptInterface-Example.html)
- [Android WebView TestCode 작성하기 기본 테스트 진행하기](/androiddev/2016/08/16/Android-WebView-TestCode.html)
- <a href="/androiddev/2016/08/17/Android-WebView-TestCode-Extended">Android WebView TestCode 작성하기 확장(Alert, Script 만들어 테스트)</a>
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

사실 쓸 일인 많지 않은 것 같지만... 정리해두려고 합니다.


<br />

**ArrayList의 String 값을 지정하고, 0번째 값 받아오기**

ArrayList의 String을 넘겨주고, 0번째 값을 받아와서 webMatches를 활용해 값이 맞는지 확인하는 간단한 예제 코드입니다.

직접 다음과 같은 script를 작성합니다. 이 코드는 Web 샘플에는 존재하지 않지만 그냥 추가해줄 수 있습니다.

arguments로 넘겨받은 값 중 0번째 값을 그냥 return 합니다.

```java
String script = "function() { return arguments[0] }";
```

return 결과는 `castOrDie`을 통해서 `String`으로 변환하고, 이를 `is`를 통해서 확인하는 단순한 코드입니다.

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

- [Android platform WebView Test code](https://android.googlesource.com/platform/frameworks/testing/+/android-support-test/espresso/sample/src/androidTest/java/android/support/test/testapp/WebViewTest.java)

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

### Alert을 그리기 전에

AlertDialog를 테스트하기 전에 사용하게 될 dependencies가 하나 있습니다.

```
'com.jayway.awaitility:awaitility:1.7.0'
```

 [awaitility](https://github.com/awaitility/awaitility)는 Thread.sleep과 같이 wait을 위한 용도로 사용됩니다.

 build.gradle에 다음의 라이선스 정보를 추가해주어야 합니다.

```
packagingOptions {
    exclude 'asm-license.txt'
}
```

그리고 사용 방법은 다음과 같습니다.

`CountDownLatch`을 초기화하고, `await`을 1초를 하는 예제입니다.

```java
// create  a signal to let us know when our task is done.
final CountDownLatch signal = new CountDownLatch(1);

// 1초를 wait 하게 됩니다.
signal.await(1, TimeUnit.SECONDS);
```


<br />

## Alert 테스트하기

Alert dialog 영역은 시스템 영역일 수 있고, 아닐 수도 있습니다.

AlertDialog를 uiautomator을 사용하여 테스트를 해볼 수 있습니다.

Android API 18 이상으로 설정하고 Ui Automator를 사용하거나, minSdk를 18 아래로 설정하고, Ui Automator를 Test 시에만 사용할 수 있도록 작업할 수 있습니다.

처음에는 Android API 18로 작성하였지만, 이번 글을 작성하면서 minSdk를 16으로 수정하였고, Android Test 동작시에만 Android API 18에서 제공하는 uiautomator를 설정하게 되었습니다.

그에 대한 내용을 정리하고, WebView에서 AlertDialog 테스트를 진행하는 부분을 설명하도록 하겠습니다.

UI Automator을 Android 6.0부터 적용된 Permissions에서 테스트하는 방법을 장 정리해둔 글입니다.

- [Testing Runtime Permissions: Lessons Learned](http://blog.egorand.me/testing-runtime-permissions-lessons-learned/)

<br />

### dependencies 추가

우선 dependencies을 추가하여야 합니다. uiautomator는 v18 버전의 dependencies입니다. v18이기에 최소 minSdk가 API 18이어야 합니다.

```
androidTestCompile 'com.android.support.test.uiautomator:uiautomator-v18:2.1.2'
```

그리고 `AndroidManifest.xml`에 다음을 추가해주어야 합니다.

```xml
<uses-sdk tools:overrideLibrary="android.support.test.uiautomator.v18" />
```

그리고 동기화를 해보면 다음과 같은 오류가 발생합니다.

uiautomator을 사용하기 위해서는 minSdkVersion이 최소 18이어야 한다는 이야기입니다. 하지만 minSdkVersion을 마음대로 올릴 수는 없으니... 해결 방법을 조금 검색해봤습니다.

```
Error:Execution failed for task ':2016-08-11-WebViewJavascriptInterafce:processDebugAndroidTestManifest'.
> java.lang.RuntimeException: Manifest merger failed : uses-sdk:minSdkVersion 16 cannot be smaller than version 18 declared in library [com.android.support.test.uiautomator:uiautomator-v18:2.1.2] /Users/tae-hwan/AndroidStudioProjects/Android-BlogExample/2016-08-11-WebViewJavascriptInterafce/build/intermediates/exploded-aar/com.android.support.test.uiautomator/uiautomator-v18/2.1.2/AndroidManifest.xml
  	Suggestion: use tools:overrideLibrary="android.support.test.uiautomator.v18" to force usage
```

검색을 통해서 해결 방법을 찾았습니다. 결국 구글의 샘플 코드에 방법이 있더군요.

- [Stack overflow 관련 질문](http://stackoverflow.com/questions/30585289/set-different-minsdkversion-for-testandroid-than-for-main-app)
- [Android Testing Blueprint 예제](https://github.com/googlesamples/android-testing-templates/tree/master/AndroidTestingBlueprint)

구글 샘플 코드를 통해서 해결을 할 수 있었습니다.

androidTest 폴더에 `AndroidManifest.xml`을 추가로 생성해주고, 기존의 `AndroidManifest.xml`에서는 문제의 `uses-sdk`를 제거합니다.

아래와 같이 `androidTest/AndroidManifest.xml`을 추가해주고, 다음을 진행하면 됩니다.

![Screenshot 2016-08-15 21.31.36](/images/2016/2016-08-17-Android-WebView-TestCode-Extended/Screenshot 2016-08-15 21.31.36.png)

<br />

해당 `AndroidManifest.xml`에는 다음의 코드를 작성해주면 됩니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest
    xmlns:tools="http://schemas.android.com/tools"
    package="자신의 앱 package 이름" >

    <uses-sdk tools:overrideLibrary="android.support.test.uiautomator.v18"/>
</manifest>
```

위와 같이 작업하고 동기화를 다시 진행하면 이상 없이 uiautomator의 사용이 가능합니다.


<br />

### Ui Automator 적용하기

우선 `@Rule`을 우선 적용합니다.

```java
@Rule
public IntentsTestRule<MainActivity> rule = new IntentsTestRule<>(MainActivity.class);
```

Ui Automator를 Test 코드에서 선언을 해주어야 합니다.

UiDevice라고 선언하고 `@Before`에서 초기화를 진행합니다.

초기화시 `JELLY_BEAN_MR2`이상에서 초기화하도록 해주어야 API 18 이하에서 테스트시 오류가 발생하지 않지만 UiDevice가 null이므로 AlertDialog 발생 시에는 개발자가 직접 눌러주어야 합니다.

아래와 같이 `JELLY_BEAN_MR2` 이상에서 UiDevice를 초기화합니다.

```java
private UiDevice device;

@Before
public void setUp() {
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.JELLY_BEAN_MR2)
        device = UiDevice.getInstance(InstrumentationRegistry.getInstrumentation());
}
```

<br />

### Alert Dialog 테스트 진행

AlertDialog를 테스트를 진행합니다.

`perform`의 `webClick`을 동작시키면 AlertDialog가 표시되고 난 다음 확인이 눌러지기 전에는 다음을 진행하지 않습니다.

일반적인 AlertDialog가 표시되는 사항도 같을 것 같지만 다음과 같습니다.

그래서 별도 Thread로 1 초 후에 ok 버튼이 UI 상 확인이 되면 해당 버튼을 click 하는 처리가 포함됩니다.

```java
@Test
public void testShowAlertDialog() throws Throwable {
    // create  a signal to let us know when our task is done.
    final CountDownLatch signal = new CountDownLatch(1);

    waitWebViewLoad(MainFragment.DEFAULT_URL);

    new Thread(new Runnable() {
        @Override
        public void run() {
            try {
                // Wait second
                signal.await(1, TimeUnit.SECONDS);
                // Find ok button and click
                assertViewWithTextIsVisible(device, rule.getActivity().getString(android.R.string.ok));
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }).start();

    // WebView show alert dialog
    onWebView().withElement(findElement(Locator.ID, "showAlertBtn"))
            .perform(webClick());
}
```

실제 UI에서 ok button을 확인하는 코드는 다음과 같습니다.

device.findObject를 통해서 특정 text를 확인할 수 있습니다. message 인지 버튼인지는 구분이 이루어지지 않지만 넘겨준 text가 ok이므로 `allowButton.click()` 이벤트를 사용할 수 있습니다.

```java
private void assertViewWithTextIsVisible(UiDevice device, String text) throws UiObjectNotFoundException {
    UiObject allowButton = device.findObject(new UiSelector().text(text));
    if (!allowButton.exists()) {
        throw new AssertionError("View with text <" + text + "> not found!");
    }
    allowButton.click();
}
```

없다면 AssertionError가 발생하고, 테스트는 fail이 처리됩니다.

**해당 코드는 Android API 18 이상에서 만 동작하고, 이전에는 `allowButton.click()`이 동작하지 않습니다. TestCode는 대기 시간을 초과하면 자동으로 Fail이 발생합니다.**

API 18 이상일 경우는 다음과 같이 동작하게 됩니다.

자동으로 동작하는 것이기에 별도의 사용자 컨트롤이 불필요합니다.

![testShowAlertDialog](/images/2016/2016-08-17-Android-WebView-TestCode-Extended/testShowAlertDialog.gif)

<br />

### Js 코드를 통해 AlertDialog 열기

이번에는 Javascript를 직접 생성하여 테스트도 가능합니다.

위와 동일하고, 다음의 script 코드를 적용하였습니다.

arguments[0]의 값을 alert()에 호출하게 됩니다.

```java
String alert = "function() { alert(arguments[0]) }";
```

별도 스레드에서 await 1초를 기다린 후 ok 버튼을 누르게 됩니다.

```java
@Test
public void testScriptAlert() throws Throwable {
    waitWebViewLoad(MainFragment.DEFAULT_URL);

    String alert = "function() { alert(arguments[0]) }";

    // create  a signal to let us know when our task is done.
    final CountDownLatch signal = new CountDownLatch(1);

    new Thread(new Runnable() {
        @Override
        public void run() {
            try {
                // Wait second
                signal.await(1, TimeUnit.SECONDS);
                // Find ok button and click
                assertViewWithTextIsVisible(device, rule.getActivity().getString(android.R.string.ok));
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }).start();

    onWebView()
            .perform(script(alert));
}
```

위의 코드를 실행하면 다음과 같습니다. 별도의 이름을 넘겨주지 않아서 `undefined`가 표시됩니다.

![testScriptAlert](/images/2016/2016-08-17-Android-WebView-TestCode-Extended/testScriptAlert.gif)


</br >

## 마무리

Android uiautomator를 통해서 Dialog를 자동으로 처리할 수 있습니다. 이를 통해 Permissions을 간단하게 테스트할 수 있게 되었습니다.

그 외 AlertDialog도 간단하게 테스트가 가능해지니 TestCode 작성에 유용하게 사용할 수 있을 것 같습니다.

lock이 걸리는 코드 부분에는 별도 Thread를 실행시켜 Test 코드를 진행할 수 있으니 위와 같이 작업할 수 있습니다.

다음 글에는 Kotlin으로 작성한 코드와 Kotlin TestCode를 정리해보도록 하겠습니다.


<br />

## 관련 글

- [Android WebView javascriptInterface 사용하기](/androiddev/2016/08/11/Android-WebView-JavascriptInterface-Example.html)
- [Android WebView TestCode 작성하기 기본 테스트 진행하기](/androiddev/2016/08/16/Android-WebView-TestCode.html)
- <a href="/androiddev/2016/08/17/Android-WebView-TestCode-Extended">Android WebView TestCode 작성하기 확장(Alert, Script 만들어 테스트)</a>
- Android WebView Kotlin Base 코드 정의 살펴보기
- Android WebView Kotlin TestCode 작성하기
