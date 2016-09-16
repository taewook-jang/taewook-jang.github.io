---
layout: post
title: Android WebView 예제
categories: [AndroidDev]
tags: [Android, AndroidDev, WebView]
fullview: false
comments: true
published: true
---

예전에 작성하였던 Android WebView 예제를 다시 수정하였습니다.

최신 버전에 맞게 적용되었고, Android CustomWebView의 JavaScriptInterface 부분을 제외한 웹뷰를 생성하였습니다.

Android WebView 예제는 [v7.Toolbar](https://developer.android.com/reference/android/support/v7/widget/Toolbar.html)에 EditText, [ContentLoadingProgressBar](https://developer.android.com/reference/android/support/v4/widget/ContentLoadingProgressBar.html)을 추가하여 만들었습니다.


<br />

## Sample Project 생성

Sample Project는 다음의 레이아웃 선택 화면에서 `Navigation Drawer Activity`를 선택하여 적용하였습니다.

![Screenshot 2016-08-15 14.04.15](/images/2016/2016-08-15-Android-WebView-Default-Example/Screenshot 2016-08-15 14.04.15.png)

\+ Fragment를 추가하였습니다.


<br />

## 작성한 샘플 화면 구성

1. Fragment에 CustomWebView 페이지
2. Navigation Drawer
3. Share

![all_capture](/images/2016/2016-08-15-Android-WebView-Default-Example/all_capture.png)

위와 같이 1개의 페이지와 공유 기능이 포함되어 있습니다.


<br />

## 작성한 샘플 코드 및 API 문서

- [GitHub URL - Android WebView Sample](https://github.com/taehwandev/Android-BlogExample/tree/master/2016-08-15-webviewexample)

**Android API**

- [Android WebView](https://developer.android.com/reference/android/webkit/WebView.html)
- [Android v7 Toolbar](https://developer.android.com/reference/android/support/v7/widget/Toolbar.html)
- [Android v4 ContentLoadingProgressBar](https://developer.android.com/reference/android/support/v4/widget/ContentLoadingProgressBar.html)
- [Android Share](https://developer.android.com/training/sharing/send.html)

**샘플 코드에 사용한 외부 API**

- [ButterKnife](http://jakewharton.github.io/butterknife/)
- [Kotlin](https://kotlinlang.org/)

**ETC**

- [MVP 패턴 적용](/androiddev/2016/06/14/Android-TODO-MVP-Example.html)

**이전 작성글**

- [Android WebView javascriptInterface 사용하기](/androiddev/2016/08/11/Android-WebView-JavascriptInterface-Example.html)



<br />

## WebVew를 사용하기위한 permission

WebView를 사용하기 위해서는 AndroidManifest.xml에 추가되어야 합니다.

```xml
<uses-permission android:name="android.permission.INTERNET" />
```


<br />

## 주요 코드 정리

WebView로부터 Event를 전달받기 위해서 `onWebViewListener`를 생성하였습니다.

```java
public interface OnWebViewListener {

    // Scroll event 받기
    void onScroll(int l, int t, int oldl, int oldt);

    // load finish
    void onFinish(String url);

    // url 변경 사항이 발생하면 호출
    void onUrlChange(String url);

    // 진행 사항 확인
    void onProgressChanged(int newProgress);
}
```

- [전체 코드](https://github.com/taehwandev/Android-BlogExample/blob/master/2016-08-15-webviewexample/src/main/java/tech/thdev/webviewexample/webkit/listener/OnWebViewListener.java)

위의 listener은 Presenter가 상속받고 있으며, 각각의 커스텀 웹뷰 속성에서 세팅하여 사용하게 됩니다.

- [CustomWebChromeClient.java](https://github.com/taehwandev/Android-BlogExample/blob/master/2016-08-15-webviewexample/src/main/java/tech/thdev/webviewexample/webkit/CustomWebChromeClient.java) : 커스텁 ChromeClient
- [CustomWebView.java](https://github.com/taehwandev/Android-BlogExample/blob/master/2016-08-15-webviewexample/src/main/java/tech/thdev/webviewexample/webkit/CustomWebView.java) : 커스텀 웹뷰
- [CustomWebViewClient](https://github.com/taehwandev/Android-BlogExample/blob/master/2016-08-15-webviewexample/src/main/java/tech/thdev/webviewexample/webkit/CustomWebViewClient.java) : 커스텀 WebView client

<br />

### WebView 커스텀 이유

WebView를 CustomView로 작성한 이유는 간단합니다. Android API 23부터 View에서 `setOnScrollChangeListener`을 적용할 수 있게 되었는데 API 23부터입니다.

그래서 WebView에서 제공되는 <a href="https://developer.android.com/reference/android/webkit/WebView.html#onScrollChanged(int, int, int, int)">`onScrollChanged`</a>을 사용하기 위해서 CustomWebView를 작성하였습니다.

CustomWebView에는 다음의 코드가 포함되어 있습니다.

onScrollChanged가 호출되면 webViewListener의 onScroll에게 전달하게 됩니다.

이를 Presenter에서 받아서 UI에 전달하게 됩니다.

```java
@Override
protected void onScrollChanged(int l, int t, int oldl, int oldt) {
    super.onScrollChanged(l, t, oldl, oldt);

    if (webViewListener != null) {
        webViewListener.onScroll(l, t, oldl, oldt);
    }
}
```

<br />

## FloatingActionButton Behavior 처리

`onScrollChanged`를 전달받아서 다음을 처리하게 되는데 FloatingActionButton에 다음과 같은 Behavior를 정의해두었습니다.

```java
public class ScrollAwareFloatingActionButtonBehavior extends FloatingActionButton.Behavior {
  // ...

  @Override
  public void onNestedScroll(CoordinatorLayout coordinatorLayout, FloatingActionButton child, View target, int dxConsumed, int dyConsumed, int dxUnconsumed, int dyUnconsumed) {
      super.onNestedScroll(coordinatorLayout, child, target, dxConsumed, dyConsumed, dxUnconsumed, dyUnconsumed);

      // child -> Floating Action Button
      if (child.getVisibility() == View.VISIBLE && dyConsumed > 0) {
          child.hide();

      } else if (child.getVisibility() == View.GONE && dyConsumed < 0) {
          child.show();
      }
  }
  // ...
}
```

- [ScrollAwareFloatingActionButtonBehavior 전체 코드](https://github.com/taehwandev/Android-BlogExample/blob/master/2016-08-15-webviewexample/src/main/java/tech/thdev/webviewexample/view/behavior/ScrollAwareFloatingActionButtonBehavior.java)

위와 같이 정의하고 `FloatingActionButton`에는 다음과 같이 사용합니다.

```xml
<android.support.design.widget.FloatingActionButton
       android:id="@+id/fab"
       app:layout_behavior="tech.thdev.webviewexample.view.behavior.ScrollAwareFloatingActionButtonBehavior"
       app:srcCompat="@drawable/reload" />
```

Presenter에서 `onWebViewListener`을 받아서 정의하고, 이를 View에 던져 다음과 같이 처리합니다.

`Presenter`에서 boolean을 정의해서 던져주기에 `show`, `hide`를 호출하여 처리합니다.

```java
@Override
public void webViewScrollChanged(boolean isTop) {
    if (isTop) {
        floatingActionButton.show();
    } else {
        floatingActionButton.hide();
    }
}
```

적용한 부분에 대해서 상세한 설명이 포함된 자료입니다.

- [Handling Scrolls with CoordinatorLayout](https://guides.codepath.com/android/Handling-Scrolls-with-CoordinatorLayout)

Android 4.1에서도 테스트해보았지만 동일하게 동작합니다.

![scroll_behavior](/images/2016/2016-08-15-Android-WebView-Default-Example/scroll_behavior.gif)


<br />

## Progress 처리

Progress는 Toolbar에 다음과 같이 추가해두었습니다.

Toolbar에 EditText와 ContentLoadingProgressBar를 포함하고 있습니다. 높이는 2dp로 숨김 처리를 해두었습니다.

```xml
<android.support.v7.widget.Toolbar
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    app:popupTheme="@style/AppTheme.PopupOverlay">

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <android.support.v4.widget.ContentLoadingProgressBar
            android:id="@+id/web_view_load_progress_bar"
            style="?android:attr/progressBarStyleHorizontal"
            android:layout_width="match_parent"
            android:layout_height="2dp"
            android:layout_alignParentBottom="true"
            android:progressBackgroundTint="@color/colorPrimary"
            android:progressTint="#0D47A1"
            android:visibility="gone" />

        <EditText
            android:id="@+id/et_url_input"
            // ...
            android:selectAllOnFocus="true" />
    </RelativeLayout>

</android.support.v7.widget.Toolbar>
```

- [Android v4 ContentLoadingProgressBar](https://developer.android.com/reference/android/support/v4/widget/ContentLoadingProgressBar.html)
- [Appbar를 포함하는 레이아웃 전체 소스코드](https://github.com/taehwandev/Android-BlogExample/blob/master/2016-08-15-webviewexample/src/main/res/layout/app_bar_main.xml)

<br />

이 Progress는 `onWebViewListener`을 통해서 `Presenter`가 받아서 `View`에 던져주게 됩니다.

View에서는 다음과 같이 처리하였습니다.

100 이상일 경우 `GONE`으로 변경하고 이하일 경우는 `VISIBLE`을 처리합니다.

```java
@Override
public void webViewProgressChanged(int newProgress) {
    loadingProgressBar.setProgress(newProgress);

    if (newProgress >= 100) {
        loadingProgressBar.setVisibility(View.GONE);
    } else if (loadingProgressBar.getVisibility() != View.VISIBLE) {
        loadingProgressBar.setVisibility(View.VISIBLE);
    }
}
```

- [Fragment 전체 코드](https://github.com/taehwandev/Android-BlogExample/blob/master/2016-08-15-webviewexample/src/main/java/tech/thdev/webviewexample/view/main/MainFragment.java#L161)

ProgressBar는 아래와 같이 동작하게 됩니다.

왼쪽에는 Android N(API 24) 오른쪽은 Android JellBean(API 16)입니다. 아래와 같이 Toolbar와 같은 색에 파란색만 겹쳐지는 것을 확인할 수 있고, 오른쪽에서는 기본 색상 값으로 적용된 것을 확인할 수 있습니다.

`ContentLoadingProgressBar`가 아닌 ProgressBar를 사용하면 같은 결과를 만들 수 있습니다.

![device-2016-08-15-203620](/images/2016/2016-08-15-Android-WebView-Default-Example/device-2016-08-15-203620.png)

<br />

## Back 키 처리

WebView 내에서 동작하게 되면 WebView의 canGoBack()을 통해서 이전 웹뷰 페이지로 돌려줄 수 있습니다. 방법이 2가지 정도가 있는데 WebView가 Focus 상태라면 아래와 같은 코드를 이용하여 WebView 내 goBack() 이벤트를 처리할 수 있습니다.

`KeyEvent.getAction`을 가져와서 `KeyEvent.ACTION_DOWN` 상태에서만 처리해주어야 합니다.

OnTouch event와 같이 `ACTION_DOWN, ACTION_MOVE, ACTION_UP`이 각각 호출되게 됩니다. 이 경우 3번 이상 호출될 수 있어 그냥 앱이 종료되는 것처럼 보일 수 있습니다.

그래서 ACTION_DOWN인 경우에만 `canGoBack`을 확인하여 `goBack`을 정의해주어야 합니다.

```java
@Override
public boolean onKey(View view, int i, KeyEvent keyEvent) {
    if (keyEvent.getAction() == KeyEvent.ACTION_DOWN) {
        switch (i) {
            case KeyEvent.KEYCODE_BACK:
                if (webView.canGoBack()) {
                    webView.goBack();
                    return true;
                }
                break;
        }
    }
    return false;
}
```

- [Fragment 전체 코드](https://github.com/taehwandev/Android-BlogExample/blob/master/2016-08-15-webviewexample/src/main/java/tech/thdev/webviewexample/view/main/MainFragment.java#L106)

Activity의 `onBackPressed`에서 다음의 코드를 사용할 수 있습니다.

다음은 fragment가 한 개일 경우에 대한 처리이므로 다음과 같은 코드가 나올 수 있지만 여러 개라면 `findContentFragment` 등을 이용하여 검색 후 처리가 필요합니다.

저는 Navigation Drawer를 함께 사용하기 때문에 다음과 같이 if/else if/else로 구분하여 앱을 종료하게 됩니다.

```java
@Override
public void onBackPressed() {
    DrawerLayout drawer = (DrawerLayout) findViewById(R.id.drawer_layout);
    if (drawer.isDrawerOpen(GravityCompat.START)) {
        drawer.closeDrawer(GravityCompat.START);
    }
    /**
     * WebView can go back event
     */
    else if (fragment.canGoBack()) {
        fragment.goBack();

    } else {
        super.onBackPressed();
    }
}
```

- [Activity 전체 코드](https://github.com/taehwandev/Android-BlogExample/blob/master/2016-08-15-webviewexample/src/main/java/tech/thdev/webviewexample/view/main/MainActivity.java#L59)


<br />

## 코틀린

Base 코드가 코틀린으로 작성되어 있습니다.

Kotlin으로 작성한 코드는 BaseActivity, BaseFragment, BasePresenter, BaseView 등과 Util 일부가 포함되어 있습니다.

코틀린에 대해서 상세한 정리는 다음의 글들을 참고해주세요.

- [Android Kotlin  시작하기](/androiddev/kotlin/2016/07/31/Kotlin-Android-Start.html)
- [Kotlin 기본 문법 정리](/kotlin/2016/08/02/Basic-Kotlin-01.html)
- [Kotlin 안전한 null 처리](/kotlin/2016/08/04/Kotlin-Null-Safety.html)
- [Kotlin 코틀린의 주요 문법들](/kotlin/2016/08/07/Kotlin-Idioms.html)


<br />

## 마무리

최근 작성하는 예제 코드들은 모두 MVP 패턴을 활용하고 있으며, Base 추상 클래스들에 대해서 kotlin으로 작성하고 있습니다.

코틀린을 몰라도 여기에서 작성한 예제들은 MVP 패턴만 알고 있으시다면 이해할 수 있으리라고 생각됩니다. 혹시라도 이해하는데 어려움이 있으시다면 댓글로 부탁드립니다.

- [MVP 패턴 적용](/androiddev/2016/06/14/Android-TODO-MVP-Example.html)

해당 글은 과거에 작성하였던 WebView를 GitHub에 새로 파기 위해서 다시 작성하였습니다.

감사합니다.

<br />

## 예제 코드 살펴보기

- [GitHub URL - Android WebView Sample](https://github.com/taehwandev/Android-BlogExample/tree/master/2016-08-15-webviewexample)
