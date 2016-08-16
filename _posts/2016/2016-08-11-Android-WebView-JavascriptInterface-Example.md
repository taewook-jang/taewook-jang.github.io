---
layout: post
title: Android WebView javascriptInterface 사용하기
categories: [AndroidDev]
tags: [Android, AndroidDev, WebView]
fullview: false
comments: true
published: true
---

안드로이드 웹뷰를 이용하여 Web에서 javascriptInterface를 호출하고, WebView에서 Web의 javascript를 호출하는 방법을 정리하려고 합니다.

위와 같이 정리할 예정이며, 첫 번째로 Android WebView javascriptInterface 사용하기를 정리하려고 합니다.

<br />

## 관련 글

- [Android WebView javascriptInterface 사용하기](/androiddev/2016/08/11/Android-WebView-JavascriptInterface-Example.html)
- [Android WebView TestCode 작성하기 기본 테스트 진행하기](/androiddev/2016/08/16/Android-WebView-TestCode.html)
- <a href="/androiddev/2016/08/17/Android-WebView-TestCode-Extended">Android WebView TestCode 작성하기 확장(Alert, Script 만들어 테스트)</a>
- Android WebView Kotlin Base 코드 정의 살펴보기
- Android WebView Kotlin TestCode 작성하기

<br />

해당 프로젝트는 Android minSdk 18 이상으로 생성하였고, Target API는 24입니다.

minSdk를 18로 설정한 이유는 아래의 이유 포함 Android TestCode 작성 시에 사용한 기법이 포함되어 있습니다. 읽으시면서 참고하여 읽어주세요.

**MinSdk 17 이상으로 적용한 이유??**

minSdk 17 이상으로 적용한 이유는 Android <a href="https://developer.android.com/reference/android/webkit/WebView.html#addJavascriptInterface(java.lang.Object, java.lang.String)">addJavascriptInterface</a>를 사용하는데 있어서 최소 17버전 이상을 권장하고 있어서입니다.

>This method can be used to allow JavaScript to control the host application. This is a powerful feature, but also presents a security risk for apps targeting JELLY_BEAN or earlier. Apps that target a version later than JELLY_BEAN are still vulnerable if the app runs on a device running Android earlier than 4.2. The most secure way to use this method is to target JELLY_BEAN_MR1 and to ensure the method is called only when running on Android 4.2 or later. With these older versions, JavaScript could use reflection to access an injected object's public fields. Use of this method in a WebView containing untrusted content could allow an attacker to manipulate the host application in unintended ways, executing Java code with the permissions of the host application. Use extreme care when using this method in a WebView which could contain untrusted content.

API 17 전 버전에서도 `@JavascriptInterface`를 명시적으로 사용하게 되며, 워닝 메시지가 표시가 되며, 사용하는데 문제가 없습니다.

Android TestCode 적용으로 인해 저는 API 18 이상을 사용하여 작업하였습니다.


<br />

## API 문서

다음을 정리하면서 참고한 자료들입니다. TDD는 별도의 글에서 작성합니다.

- [Android - Building Web Apps in WebView](https://developer.android.com/guide/webapps/webview.html)
- [Android WebView](https://developer.android.com/reference/android/webkit/WebView.html)
- [Kotlin](kotlinlang.org)
- [Android Kotlin  시작하기](/androiddev/kotlin/2016/07/31/Kotlin-Android-Start.html)
- [Android TODO MVP 어떻게 적용할까?](/androiddev/2016/06/14/Android-TODO-MVP-Example.html)


<br />

## Sample Project 정보

해당 프로젝트는 [dependencies.gradle](https://github.com/taehwandev/Android-BlogExample/blob/master/dependencies.gradle)에 API 사용 정보를 별도 명시하고 있습니다.

- Android Studio 2.2+(추후 업데이트시 해당 프로젝트 전체가 변경될 수 있음)
- minSdk = 17
- TargetSdk = 24+(최신 버전에 따라 대응)
- Kotlin 사용(Base 및 Util에서 사용)
- ButterKnife 사용


<br />

## Sample Project 링크

샘플 프로젝트는 아래와 같이 확인이 가능합니다.

- [Android WebView Sample - Github](https://github.com/taehwandev/Android-BlogExample/tree/master/2016-08-11-WebViewJavascriptInterafce)
- [Android WebView Sample page(html)](https://github.com/taehwandev/taehwandev.github.io/blob/master/sample/webview_sample.html)


<br />

## CustomWebView 생성하기

안드로이드 CustomWebView를 생성하였습니다. WebView를 상속받아 CustomWebView를 만들고, 거기에 필요한 WebChromeClient, WebViewClient를 각각 구성하였습니다.

기본 WebView 커스텀은 가장 기본적인 것들로만 구성하였고, `@JavascriptInterface`를 테스트할 수 있는 구조로 작업하였습니다.

굳이 Custom까지 할 필요는 없지만 함께 진행하여, TDD 테스트 코드까지 만들어 두었습니다.

대략 다음과 같은 Flow로 동작하게 됩니다.

![Screenshot 2016-08-11 22.11.18](/images/2016/2016-08-11-Android-WebView-JavascriptInterface-Example/Screenshot 2016-08-11 22.11.18.png)

Web에서 버튼을 누르게 되면 이를 Android WebView의 `@JavascriptInterface`가 동작하게 되고, 이를 `Preseter`가 받아서 `View(Fragment)`에 전달하도록 만들었습니다.


<br />

**JavascriptInterface 정의**

Android에서 JavascriptInterface를 정의시에는 다음의 코드와 같이 정의해야 합니다.

실제 Web의 Javascript에서 호출할 수 있도록 `@JavascriptInterface`를 함께 사용하여 정의해야 합니다.

```java
public class CustomJavaScript {

    private OnCustomJavaScriptListener listener;

    public CustomJavaScript() {

    }

    public void setOnCustomJavascriptListener(OnCustomJavaScriptListener listener) {
        this.listener = listener;
    }

    @JavascriptInterface
    public void updateKeyword(String keyword) {
        if (listener != null) {
            listener.onUpdateKeyword(keyword);
        }
    }

    @JavascriptInterface
    public void changeWebView(String url) {
        if (listener != null) {
            listener.onChangeWebView(url);
        }
    }
}
```

[전체 코드 - CustomJavaScript](https://github.com/taehwandev/Android-BlogExample/blob/master/2016-08-11-WebViewJavascriptInterafce/src/main/java/tech/thdev/webviewjavascriptinterface/webkit/CustomJavaScript.java)

제가 정의한 `@JavascriptInterface`는 `updateKeyword`와 `changeWebView`입니다. 각각 keyword, url을 입력받아 Preseter와 View에서 정의하도록 만들었습니다.

<br />

**MainPresenter**

Preseter는 `OnCustomJavaScriptListener`을 상속받고 있고, Preseter는 아래와 같이 정의하고 있습니다.

```java
// ...

@Override
public void onUpdateKeyword(String keyword) {
    if (isAttachView()) {
        getView().updateKeyword(keyword);
    }
}

@Override
public void onChangeWebView(String url) {
    if (isAttachView()) {
        getView().changeWebView(url);
    }
}

// ...
```

[전체 코드 - MainPresenter](https://github.com/taehwandev/Android-BlogExample/blob/master/2016-08-11-WebViewJavascriptInterafce/src/main/java/tech/thdev/webviewjavascriptinterface/view/main/presenter/MainPresenter.java)

위와 같이 UI 처리는 View에서 담당하기에 view에 전달하도록 되어 있습니다.


<br />

**MainFragment**

MainFragment는 Preseter의 View를 구현하게 됩니다.

View의 구현 부분은 다음과 같습니다. `updateKeyword`는 `etKeyword`에 값을 세팅하게 되고, `changeWebView`는 `Intent` 정의 후 `startActivity`를 호출하게 됩니다.

```java
// ...

@Override
public void updateKeyword(final String keyword) {
    getActivity().runOnUiThread(new Runnable() {
        @Override
        public void run() {
            etKeyword.setText(keyword);
        }
    });
}

@Override
public void changeWebView(String url) {
    Intent intent = new Intent(getActivity(), KotlinActivity.class);
    intent.putExtra(Constant.getKEY_INTENT_URL(), url);
    startActivity(intent);
}

// ...
```

[전체코드 - MainFragment](https://github.com/taehwandev/Android-BlogExample/blob/master/2016-08-11-WebViewJavascriptInterafce/src/main/java/tech/thdev/webviewjavascriptinterface/view/main/MainFragment.java)


<br />

## WebView 정의 코드는?

이제 WebView의 정의 코드를 살펴보겠습니다. WebView는 CustomWebView로 init()을 조금 편하게 만들었습니다. 그 외에 다른 부분은 `Override`하지는 않았습니다.

[WebView](https://developer.android.com/reference/android/webkit/WebView.html)를 상속받은 CustomWebView는 [getSettings](https://developer.android.com/reference/android/webkit/WebSettings.html)을 사용하여 설정을 정의할 수 있습니다.

```java
public void init() {
    WebSettings settings = getSettings();
    // Javascript 사용하기
    settings.setJavaScriptEnabled(true);
    // WebView 내장 줌 사용여부
    settings.setBuiltInZoomControls(true);
    // 화면에 맞게 WebView 사이즈를 정의
    settings.setLoadWithOverviewMode(true);
    // ViewPort meta tag를 활성화 여부
    settings.setUseWideViewPort(true);
    // 줌 컨트롤 사용 여부
    settings.setDisplayZoomControls(false);
    // 사용자 제스처를 통한 줌 기능 활성화 여부
    settings.setSupportZoom(false);
    // TextEncoding 이름 정의
    settings.setDefaultTextEncodingName("UTF-8");

    // Setting Local Storage
    settings.setDatabaseEnabled(true);
    settings.setDomStorageEnabled(true);

    // 캐쉬 사용 방법을 정의
    settings.setCacheMode(WebSettings.LOAD_NO_CACHE);
}
```

setCacheMode의 경우 `LOAD_DEFAULT`가 기본값입니다. `LOAD_DEFAULT`는 상황에 따라서 캐시에서 불러올지 직접 호출할지를 분기하게 됩니다. 변경이 없으면 다음 값을 가지며, 일부 캐시를 사용하기 때문에 데이터 절약이 함께 동작하게 되겠습니다.

여기서는 `LOAD_NO_CACHE`를 통해 항상 웹에서 최신 데이터를 불러오도록 하였습니다.

[전체코드 - WebView](https://github.com/taehwandev/Android-BlogExample/blob/master/2016-08-11-WebViewJavascriptInterafce/src/main/java/tech/thdev/webviewjavascriptinterface/webkit/CustomWebView.java)


<br />

**WebViewClient/WebChromeClient 정의**

WebView에 아래와 같이 WebViewClient와 WebChromeClient를 각각 세팅합니다.

두 개 모두 Custom으로 간단하게 정의하여 만들어두었습니다.

```java
webView.setWebViewClient(new CustomWebViewClient());
webView.setWebChromeClient(new CustomWebChromeClient(getActivity()));
```


<br />

**addJavascriptInterface()**

JavascriptInterface 정의를 하였으니 WebView에 세팅해주어야 합니다.

<a href="https://developer.android.com/reference/android/webkit/WebView.html#addJavascriptInterface(java.lang.Object, java.lang.String)">addJavascriptInterface</a>함수를 호출하여 정의합니다.

앞에는 WebView에서 호출할 수 있는 `@JavascriptInterface`이고, 뒤에는 이름입니다.

Web에서는 `name.JavascriptInterface name`으로 호출할 수 있습니다.

제가 만든 건 `WebViewTest.updateKeyword(keyword);`의 형태로 불러오게 됩니다.

```java
webView.addJavascriptInterface(getPresenter().getCustomJavaScript(), "WebViewTest");
```


<br />

## 전체 구동

아래와 같이 간단하게 WebView를 테스트하도록 구성하였습니다.

![device-2016-08-11-223205](/images/2016/2016-08-11-Android-WebView-JavascriptInterface-Example/device-2016-08-11-223205.png)

위의 사이트에서 각각 버튼을 눌러주면 Android 측의 JavascriptInterface가 호출되고, 그에 따라 Preseter > View에서 각각의 이벤트를 처리하게 됩니다.

안드로이드 전체 코드를 구동하시면 확인이 가능합니다.


<br />

## Web 페이지 간단한 코드 보기

WebPage의 간단한 코드를 보여드리도록 하겠습니다.

```javascript
function updateKeyword() {
  var keyword = document.getElementById("search_keyword").value;
  WebViewTest.updateKeyword(keyword);
}
```

javascript는 위와 같이 정의하였고, html에서는 아래와 같습니다.

```html
<!-- 값을 입력 -->
<input type="text" id="search_keyword" value="Search keyword" />

<!-- input button 정의 -->
<input id="updateKeywordBtn" type="button" value="Update keyword" onclick="updateKeyword();" />
```

Web의 button을 눌러주면 `updateKeyword`가 호출되고 `WebViewTest.updateKeyword(keyword);`가 동작하게 됨으로 실제로는 Android `@JavascriptInterface`가 동작하게 되는 형태입니다.

[Sample page - 전체코드](https://github.com/taehwandev/taehwandev.github.io/blob/master/sample/webview_sample.html)


<br />

## 마무리

간단하게 웹뷰의 `@JavascriptInterface`를 구현하여보았습니다. 다음 글에서는 이를 WebView TestCode를 통해서 검증하는 과정과 Kotlin 코드, Kotlin TestCode 작성하는 방법 등을 살펴보도록 하겠습니다.

해당 예제는 base는 Kotlin으로 작성하였으며, 오늘 작성한 글의 코드는 Java로 구현되어 있습니다.

`KotlinActivity`라고 별도의 WebView를 Kotlin 코드로 구현한 부분도 있어서 각각의 글을 작성하려고 합니다.


<br />

## 관련 글

- [Android WebView javascriptInterface 사용하기](/androiddev/2016/08/11/Android-WebView-JavascriptInterface-Example.html)
- [Android WebView TestCode 작성하기 기본 테스트 진행하기](/androiddev/2016/08/16/Android-WebView-TestCode.html)
- <a href="/androiddev/2016/08/17/Android-WebView-TestCode-Extended">Android WebView TestCode 작성하기 확장(Alert, Script 만들어 테스트)</a>
- Android WebView Kotlin Base 코드 정의 살펴보기
- Android WebView Kotlin TestCode 작성하기


<br />

## Sample Project 링크

샘플 프로젝트는 아래와 같이 확인이 가능합니다.

- [Android WebView Sample - Github](https://github.com/taehwandev/Android-BlogExample/tree/master/2016-08-11-WebViewJavascriptInterafce)
- [Android WebView Sample page(html)](https://github.com/taehwandev/taehwandev.github.io/blob/master/sample/webview_sample.html)
