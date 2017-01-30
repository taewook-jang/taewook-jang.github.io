---
layout: post
title: Android 윈도우(Overlay) 권한 획득하기
categories: [AndroidDev]
tags: [Android]
date: 2017-01-30
fullview: false
comments: true
published: true
---

Android Marshmallow에서는 기본 권한 획득 외에도 `SYSTEM_ALERT_WINDOW` 사용을 위한 별도 권한을 추가해주어야 합니다.

[SYSTEM_ALERT_WINDOW](http://developer.android.com/intl/ko/reference/android/Manifest.permission.html#SYSTEM_ALERT_WINDOW)는 WindowManager를 이용하여 최상위 뷰에 화면을 노출하는 뷰의 옵션입니다.

`Service`를 이용하여 Overlay 하는 경우에는 다음과 같이 락/노티피케이션 위에 노출됩니다.

![lock_overlay](/images/2016/2016-05-08-Android-Overlay-Permission/lock_overlay.png)


<br />

## Marshmallow 변경 사항

WindowManager에는 사용할 수 있는 레이아웃 옵션이 여러 개 있습니다.

이러한 LayoutParams 옵션에 따라서 노출되는 범위가 서로 다르며, 크게 2가지를 사용합니다.

- [TYPE_TOAST](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#TYPE_TOAST) : Toast 표시 부분에 UI 노출.
- [TYPE_SYSTEM_ALERT](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#TYPE_SYSTEM_ALERT) : 최상위에 노출되며, 시스템 락과 노티피케이션 위에 노출.(Service 활용 시)

이중 `SYSTEM_ALERT_WINDOW`에 해당하는 `TYPE_SYSTEM_ALERT`을 Android Marshmallow 이상에서 사용할 경우 별도의 권한 요청을 추가해야 합니다.

**참고 : Play store 다운 앱의 경우 draw 권한이 true 상태로 설치되며, 사용자가 변경이 가능합니다.**

다음의 옵션이 Play store에서 받을 경우 `Yes` 직접 설치할 경우 `No`이므로 다음의 Overlay 권한 획득 요청이 필요합니다.

![Screenshot_20160508-121059](/images/2016/2016-05-08-Android-Overlay-Permission/Screenshot_20160508-121059.png)

해당 부분을 눌러주면 아래와 같이 설정 변경이 가능합니다.

![Screenshot_20160508-121104](/images/2016/2016-05-08-Android-Overlay-Permission/Screenshot_20160508-121104.png)


<br />

## Marshmallow 대응

여기에서 테스트한 예제는 다음의 Github 링크를 통해 확인 가능합니다.

- [GitHub : Android-Overlay-Permission-Example](https://github.com/taehwandev/Android-BlogExample/tree/03-Overlay-Permission-Example)

`TYPE_SYSTEM_ALERT` 사용할 경우 Manifest에 다음을 추가합니다.

```xml
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
```

Marshmallow(6.0(API 23))부터는 다음과 같은 코드가 추가로 필요합니다.

Play store에서 true로 설정해주더라도, 사용자가 언제든 끌 수 있습니다.

대응하지 않으면 오류 나는 부분도 예제 코드에 포함되어 있으므로, 참고하시면 되겠습니다.

Android M 이상을 체크하고, 설정 API인 <a href="http://developer.android.com/intl/ko/reference/android/provider/Settings.html#canDrawOverlays(android.content.Context)">canDrawOverlays</a>을 통해 Overlay 사용이 가능한지를 체크합니다.

그 이하 버전은 기존에 하였던 것과 같이 그냥 실행하면 되겠습니다.

```java
public void startOverlayWindowService(Context context) {
   if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M
           && !Settings.canDrawOverlays(context)) {
       getView().onObtainingPermissionOverlayWindow();

   } else {
       getView().onStartOverlay();
   }
}
```

`onObtainingPermissionOverlayWindow`에는 아래와 같은 코드가 지정되어 있으며, [Settings.ACTION_MANAGE_OVERLAY_PERMISSION](http://developer.android.com/intl/ko/reference/android/provider/Settings.html#ACTION_MANAGE_OVERLAY_PERMISSION)에 현재 패키지 명을 넘겨 설정화면을 노출하게 됩니다.

```java
@TargetApi(Build.VERSION_CODES.M)
@Override
public void onObtainingPermissionOverlayWindow() {
    Intent intent = new Intent(Settings.ACTION_MANAGE_OVERLAY_PERMISSION, Uri.parse("package:" + getPackageName()));
    startActivityForResult(intent, REQ_CODE_OVERLAY_PERMISSION);
}
```

위의 코드를 실행하면 아래와 같은 화면을 확인할 수 있으며, 사용자가 직접 설정합니다.

![overlay_image](/images/2016/2016-05-08-Android-Overlay-Permission/overlay_image.png)

그리고 윈도우 매니저를 통해 다음의 코드를 실행합니다.

```java
/**
 * Window View 를 초기화 한다. X, Y 좌표는 0, 0으로 지정한다.
 */
private void initWindowLayout(LayoutInflater layoutInflater) {
    windowView = layoutInflater.inflate(R.layout.window_view, null);
    windowViewLayoutParams = new WindowManager.LayoutParams(
            WindowManager.LayoutParams.WRAP_CONTENT, WindowManager.LayoutParams.WRAP_CONTENT,
            30, 30, // X, Y 좌표
            WindowManager.LayoutParams.TYPE_SYSTEM_ALERT,
            WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE | WindowManager.LayoutParams.FLAG_LAYOUT_IN_SCREEN,
            PixelFormat.TRANSLUCENT);
    windowViewLayoutParams.gravity = Gravity.TOP | Gravity.START;
    windowManager.addView(windowView, windowViewLayoutParams);
}
```


<br />

## 마무리

Android Marshmallow부터는 `SYSTEM_ALERT_WINDOW` 사용을 위해서 권한 요청이 필요합니다.

- Play store 통해서 설치하면 Yes 설정
- 직접 설치하면 No 설정

위와 같지만 사용자는 옵션을 끌 수 있습니다.

그렇기에 이에 따른 권한 설정을 추가해주어야 합니다.

작성한 예제를 실행하면 아래와 같이 동작할 수 있습니다.

그리고 사용자를 위한 설명 명확도를 위해서 다음과 같은 화면을 추가해줄 수도 있습니다.

**페이스북에서 제공하는 샘플 자료**

![Screenshot_20160508-121133](/images/2016/2016-05-08-Android-Overlay-Permission/Screenshot_20160508-121133.png)


<br />

## 기타 참고 자료

WindowManager를 활용한 onTouchEvent 처리를 위한 방법은 [WindowManager의 onTouchEvent 처리하기](http://thdev.net/617) 참고하시면 되겠습니다.

샘플 코드 : GitHub - Android Blog Example - [GitHub : Android-Overlay-Permission-Example](https://github.com/taehwandev/Android-BlogExample/tree/03-Overlay-Permission-Example)

![android-overlay-permission](/images/2016/2016-05-08-Android-Overlay-Permission/android-overlay-permission.gif)
