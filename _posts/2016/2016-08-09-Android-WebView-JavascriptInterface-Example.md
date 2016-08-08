---
layout: post
title: Android WebView javascriptInterface 사용하기
categories: [AndroidDev]
tags: [Android, AndroidDev]
fullview: false
comments: true
published: false
---

안드로이드 웹뷰를 이용하여 Web에서 javascriptInterface를 호출하고, WebView에서 Web의 javascript를 호출하는 방법을 정리하려고 합니다.

해당 프로젝트는 Android minSdk 17이상으로 생성하였고, Target API는 24입니다.

minSdk를 17 이상으로 적용한 이유는 Android <a href="https://developer.android.com/reference/android/webkit/WebView.html#addJavascriptInterface(java.lang.Object, java.lang.String)">addJavascriptInterface</a>를 사용하는데 보안상 최소 17이상을 권장하고 있어서 입니다.

>This method can be used to allow JavaScript to control the host application. This is a powerful feature, but also presents a security risk for apps targeting JELLY_BEAN or earlier. Apps that target a version later than JELLY_BEAN are still vulnerable if the app runs on a device running Android earlier than 4.2. The most secure way to use this method is to target JELLY_BEAN_MR1 and to ensure the method is called only when running on Android 4.2 or later. With these older versions, JavaScript could use reflection to access an injected object's public fields. Use of this method in a WebView containing untrusted content could allow an attacker to manipulate the host application in unintended ways, executing Java code with the permissions of the host application. Use extreme care when using this method in a WebView which could contain untrusted content.

`@JavascriptInterface`을 사용한다면 Android API 17 이하에서도 문제 없이 동작합니다. 기존 WebView의 JavascriptInterface 사용방법과는 다르게 구현하므로 문제 없습니다.


<br />

##Sample Project 정보

- Android Studio 2.2+(추후 업데이트시 해당 프로젝트 전체가 변경될 수 있음)
- minSdk = 17
- TargetSdk = 24+(최신 버전에 따라 대응))
- Kotlin 사용(Base 및 Util에서 사용)
- ButterKnife 사용


<br />

##CustomWebView 생성하기

우선 CustomWebView를 만들었습니다.


<br />

##마무리
