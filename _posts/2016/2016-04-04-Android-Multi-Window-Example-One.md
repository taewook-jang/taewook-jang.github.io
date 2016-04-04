---
layout: post
title: Android N 멀티윈도우 - 어떻게 대응해야 할까?
---

안드로이드 N에서는 Multi window를 지원하게 됩니다.
이번 글에서는 멀티 윈도우의 적용 방법은 어떻게 될지 살펴보겠습니다.

살펴보기 전에 지금까지 정리하였던 멀티 윈도우 관련 내용은 아래와 같습니다.

[Android N Multi Window 살펴보기](http://thdev.tech/Android-N-Preview-Multi-Window/)

[Android N Multi Window Freeform mode 에뮬레이터에 적용해보기](http://thdev.net/657)

<br />

##안드로이드 멀티 윈도우##

 안드로이드 N 멀티 윈도우에 관련된 API는 다음 링크를 확인해주세요.
[Android N Multi Window API](http://developer.android.com/preview/features/multi-window.html)

 멀티 윈도우를 적용 시 주의해야 할 점을 코드상으로 테스트해보았습니다.
 <br />
 **onResume()/onPause()**
 일반적인 화면 간 이동을 하였을 때 타는 메서드입니다.
 Multi window 간 화면 전환을 하기 때문에 아래와 같이 동작합니다.

 ![Screenshot_20160404-234814.png]({{ site.baseurl }}/images/2016/2016-04-09-Android-Multi-Window-Example-One/Screenshot_20160404-234814.png)

 맨 아래부터 앱이 실행되면서 순서대로 동작합니다.
 onCreate -> onStart -> onResume 가 순서대로 동작합니다.

 이후 창간 전환을 시도하였습니다.
 onPause -> onResume -> onPause ...

 위와 같은 사이클을 계속적으로 타게 됩니다.
 <br />
 **창 크기 변경**
 가운데 이동을 통해 창 크기를 변경하게 됩니다.
 창의 크기가 변경되면 다음과 같이 동작하게 됩니다.

 ![Screenshot_20160404-235109.png]({{ site.baseurl }}/images/2016/2016-04-09-Android-Multi-Window-Example-One/Screenshot_20160404-235109.png)

 위와 같이 항상 3번만 동작하게 됩니다.

 onCreate -> onStart -> onResume

 창의 사이즈가 변경되면
 onPause -> onDestroy -> onCreate -> onStart -> onResume

 위와 같은 동작을 하게 됩니다.
 <br />
 **창 크기 변경 시 configChanges 적용**
  configChanges를 적용하지 않으면 바로 위의 **창 크기 변경** 에서 본 것과 같이 동작하게 됩니다. 단순히 onDestroy -> onCreate를 다시 타게 되는데요.

 xml에 다음을 적용하면 onDestroy 타지 않게 됩니다.

```xml
android:configChanges="screenSize|smallestScreenSize|screenLayout|orientation"
```

 ![Screenshot_20160405-000516.png]({{ site.baseurl }}/images/2016/2016-04-09-Android-Multi-Window-Example-One/Screenshot_20160405-000516.png)


현재는 위의 그림과 같이 newConfig 1로 표시되고 있는데 API에서는 아래와 같습니다.

```java
Configuration.SCREENLAYOUT_SIZE_SMALL
```

 화면의 위의 그림과 같이 최대 창 크기(멀티윈도우 기준 최대치)와 최저 창 크기(멀티 윈도우 기준 최저 크기) 일 경우에도 현재는 1의 값을 확인할 수 있습니다.
<br />

##주요 코드

  xml에서는 아래와 같이 코드를 적용하였습니다. 별도 resizableActivity를 정의하지 않아도 동작하게 됩니다.

  ***반대로 동작하지 않을 경우***
  screenOrientation을 강제 지정하게 되면 동작하지 않습니다.(현재는 Application에만 적용되어야 동작하지 않습니다.)

```xml
<!-- Multi window support example -->
<activity
    android:name=".multiwindow.MultiWindowActivity"
    android:resizeableActivity="true"
    android:configChanges="screenSize|smallestScreenSize|screenLayout|orientation"
    android:theme="@style/AppTheme.NoActionBar" />

<!-- Multi window freeform mode example -->
<!-- 테스트 하려고 넣었지만 실제 동작하지 않네요 -->
<activity
    android:name=".multiwindow.FreeformModeActivity"
    android:resizeableActivity="true"
    android:theme="@style/AppTheme.NoActionBar">
    <layout
        android:defaultHeight="500dp"
        android:defaultWidth="600dp"
        android:gravity="top|end"
        android:minimalSize="450dp" />
</activity>
```

<br />

##java 코드 확인 방법
Activity 기반에서 다음의 코드를 추가하여 현재 상태를 확인할 수 있습니다.

```java
inMultiWindow()
```

제가 테스트한 코드는 다음과 같습니다.

```java
multiWindowAdapter.addItem("onResume() inMultiWindow " + inMultiWindow(), true);
```

 API 문서상으로는 다음의 API도 있는데 저는 따로 테스트해보지 않았습니다.

```java
onMultiWindowChanged()
```

그런데 위의 2개 API가 모두 boolean 값을 return 하고 있어서 크게 차이가 나지는 않습니다.

<br />
<br />

##마무리
 멀티 윈도우에 대해서 API 문서를 살펴보고, 실제 코드 상으로 테스트까지 진행해보았습니다.(Freeform 모드를 적용해보기도 하였습니다.)

 [Android N Multi Window 살펴보기](http://thdev.tech/Android-N-Preview-Multi-Window/)

 [Android N Multi Window Freeform mode 에뮬레이터에 적용해보기](http://thdev.net/657)

  정리해둔 onResume/onPause에 대해서 꼭 필수로 정의가 필요합니다.
  또한 화면 사이즈가 다시 그려지는 부분에 대해서 대응이 필요해 보입니다.

  현재 Preview에서는 onDestroy와 onCreate가 다시 타게 됩니다. [5 tips for preparing for Multi-Window in Android N](https://medium.com/google-developers/5-tips-for-preparing-for-multi-window-in-android-n-7bed803dda64#.x5dd4ku7n)에서 보았던 configChanges를 다음과 같이 꼭 추가하여 작업해주시기 바랍니다.

```xml
android:configChanges="screenSize|smallestScreenSize|screenLayout|orientation"
```
