---
layout: post
title: Android N 화면 사이즈 변경에 따른 대응 방법
categories: [AndroidDev]
tags: [Android, AndroidDev]
fullview: false
comments: true
published: true
---

Android Nougat(7.0)부터 루팅이나 adb 명령을 통하지 않고도, 스크린 사이즈를 변경이 가능해졌습니다.

누가 정식 버전에서는 아래와 같은 메뉴를 통해 화면 사이즈 변경이 가능한데, `재부팅 하지 않고도 동작`합니다.

**시스템 설정 > 화면 > 화면 크기**

![screenSizeSetting]

<br />

이런 좋은 기능은 Android N에서만 제공되지는 않을 것입니다. 일부 제조사에서 이전 버전에 미리 가져갈 수 있습니다.(원래 adb를 통해서 제공되던 기능이라서 어렵지 않게 포팅도 가능할 겁니다.)

가장 최근에 나온 `갤럭시 노트 7(Note 7)`이 이에 해당되는 기기입니다.

노트 7은 배터리 최적화 모드와 일부 최신 OS에서는 화면 사이즈를 직접 변경할 수 있는 옵션을 제공합니다.

이에 따라 생길 수 있는 오류는 다음과 같습니다.

- 일부 개발 시 `화면 사이즈를 저장`하여 사용할 수 있습니다.
  - 그간 화면 사이즈 변경은 쉽게 이루어질 수 없고, 바로 적용되지 않았습니다.
  - 일부 커스텀 롬 또는 `adb`를 통해서 할 수 있었습니다.
- 화면 사이즈를 저장해두고 사용하면 UI의 깨짐이 발생할 수 있으며, 일부 원치 않는 방식으로 동작할 수 있었습니다.
- Android N이 아닌 이상 `configChanges` 이벤트가 발생하지 않습니다.

대략 위와 같습니다. 이에 대해서 간단하게 누가에서 제공하는 화면 사이즈 종류와 갤럭시 노트 7에서 제공하는 화면 사이즈 정보를 정리해보고, 사이즈 변화를 어떤식으로 대응할 수 있을지에 대해서 정리해보았습니다.


<br />

## 화면 사이즈 종류

**누가(API 24) 정식 버전**

누가 정식 버전에서는 아래와 같은 화면 사이즈 변경이 가능합니다.

- 가장 작게
- 기본 사이즈
- 크게
- 조금 크게
- 가장 크게

**위와 같이 5단계의 화면 사이즈가 변경이 가능합니다. 이 부분은 아래에서 실제 코드와 함께 얼마나 변하는지를 살펴보겠습니다.**

노트 7의 배터리 최적화 및 화면 크기 변경 시 사이즈

- 1280x720
- 1920x1080
- 2560x1440

위와 같이 3가지의 값으로 변경이 일어나게 됩니다. **실제로 값을 체크하면 해상도 자체의 변화가 심하게 발생하게 됩니다.**


<br />

## 화면 사이즈 변경으로 인한 문제점은??

안드로이드 nougat에서는 해상도는 아래와 같이 5단계로 변경이 일어납니다.

하지만 실제 값의 변화는 **dpi 값이 크게 변할 뿐** 실제 해상도는 크게 변하지 않습니다.

![AndroidNScreenSize]

다만 **갤럭시 노트 7**에서는 **dpi 값보다는** 해상도 자체가 큰 변화를 일으킵니다.

위에서 설정할 수 있는 3개 중에 하나의 값이 오게 되므로 전혀 다른 값이 오게 되는 것이죠.

<br />

우선 일반적인 해상도 정보를 가져오는 방법입니다.

```java
DisplayMetrics metrics = new DisplayMetrics();
getWindowManager().getDefaultDisplay().getMetrics(metrics);
```

위의 값으로 불러오는데 다음의 경우 문제가 발생할 수 있겠습니다.

- 실시간으로 화면 사이즈를 가져와서 처리하지 않는 경우(Splash, Intro 등의 처음 실행시 최초 한번만 체크)
  - 값을 저장해두고 사용하는 경우에 해당됩니다.
- `onResume`이 아닌 `onCreate`등에서 한 번만 호출해서 사용할 경우

<br />

그렇다고 해도 누가에서 제공하는 화면 사이즈 변화에서는 큰 문제는 없습니다.

아래에서 확인하겠지만 실제 값의 변화가 크지 않음을 알 수 있습니다.

대부분 pixel 값을 가지고 계산을 하여 레이아웃을 그리거나, 애니메이션 처리를 하는 경우가 될 것입니다.


<br />

하지만 삼성에서는 이 pixel 값이 전혀 다르게 들어오게 됩니다.

- 1920x1080 -> 최대 화면 사이즈로 변경 시 2560x1440
- 2560x1440 -> 보통의 화면 사이즈로 변경 시 1920x1080
- 2560x1440 -> 가장 큰 화면으로 변경 시 1280x720

위와 같이 전혀 다른 새로운 값으로 오게 됩니다.


<br />

**저는 노트 7이 없어서 Nexus 6의 누가로 테스트하였습니다.**


<br />

## 화면 사이즈 변화를 확인해보자

그렇다면 안드로이드 누가에서 실제로 어떤 변화가 있는지, 어떤 식으로 값을 체크하면 좋은지에 대해서 가이드를 해보겠습니다.

해당 방법은 Android N과 무관하게 동작하지만, Target API가 24일 경우의 대응은 아래 `Android 누가 대응` 부분에서 확인해주세요.

테스트는 다음과 같이 진행하였습니다.

- `onResume`에서 `getMetrics`를 통해 사이즈 변화 체크
- `onConfigurationChanged`를 통한 changed event 확인 방법


<br />

## onResume을 통해서 확인

가장 대응하기 쉬운 `onResume`을 통해서 화면 사이즈의 변화를 체크해보았습니다.

일반적으로 가장 대응하기 쉬운 방법이 되겠는데 다음과 같은 코드로 확인할 수 있었습니다.

```java
@Override
    protected void onResume() {
        super.onResume();

        DisplayMetrics metrics = new DisplayMetrics();
        getWindowManager().getDefaultDisplay().getMetrics(metrics);

        Log.i("TAG", "display width : " + metrics.widthPixels + ", height : " + metrics.heightPixels + ", densityDpi : " + metrics.densityDpi);
    }
```

**Nexus 6 기준 기본 해상도는 다음과 같습니다.**

위와 같은 코드로 5가지 설정을 각각 확인하면 아래와 같습니다.

- **가장 작은 화면** : display width : 1440, height : 2417, densityDpi : 476
- **기본 화면** : display width : 1440, height : 2392, densityDpi : 560
- **큰 화면** : display width : 1440, height : 2376, densityDpi : 612
- **조금 큰 화면** : display width : 1440, height : 2360, densityDpi : 666
- **가장 큰 화면** : display width : 1440, height : 2344, densityDpi : 720

해상도 별로 dpi 사이즈와 `heightPixels`의 값이 조금씩 변함을 알 수 있습니다.

해당 코드는 태블릿에서도 확인하였지만 동일하게 `height`만 변함을 확인하였습니다.

대략 넥서스 6 기준으로는 높이는 2417 ~ 2344까지 변함을 알 수 있고, 편차는 73 pixel 정도 차이를 보임을 알 수 있습니다. 이에 따라 실제 큰 변화가 없어서 사실 기존 값을 유지하였을 때 사용성에 문제는 없을 수 있습니다.

**하지만 갤럭시 노트는** display width, height의 pixel 사이즈가 완전히 다르게 오게 되어 값을 변경하지 않으면 100% UI/애니메이션에 문제가 발생할 수 있습니다.

해당 방법은 단순히 `onResume`을 통해서 체크하였습니다. Android N에서는 이런 대응 방식이 좋지 않습니다. 바로 멀티 윈도우 때문이죠. 그래서 바로 아래와 같은 방법으로 좀 더 안전하게 확인이 가능합니다.


<br />

## Android N에서 화면 사이즈 변화를 확인하기

안드로이드 7.0부터는 [Multi Window](/2016/03/29/Android-N-Preview-Multi-Window.html)를 지원합니다. 이에 따라 `configChanges`를 정의하지 않는다면 `onResume`이 계속적으로 불릴 수 있게 됩니다.

안드로이드 N에서는 [Multi Window(Muti Window 대응 방법)](/Android-Multi-Window-Example-One.html)와 화면 사이즈의 변화도 함께 체크해주어야 합니다.

우선 [Android 7.0 Behavior Changes](https://developer.android.com/about/versions/nougat/android-7.0-changes.html)의 문서를 살펴보면 화면 사이즈 변화를 [Screen Zoom](https://developer.android.com/about/versions/nougat/android-7.0-changes.html#screen-zoom)이라고 표현하고 있으며, 그에 대한 내용을 제공해주고 있습니다.

저는 Android N의 Screen 대응 방식에 `density`를 추가하여 아래와 같은 `android:configChanges`를 추가하였습니다. `density`의 변화를 알 수 있으며, 그에 따른 변화를 대응할 수 있게 됩니다.

> screenLayout|screenSize|smallestScreenSize|density

위와 같은 코드는 아쉽지만 Android Target API 24에서만 동작하게 됩니다.

API 24 이전이라면 `onResume`을 통해서만 확인이 가능합니다.

```xml
<activity
        android:name=".view.main.MainActivity"
        android:configChanges="screenLayout|screenSize|smallestScreenSize|density"
        android:label="@string/app_name"
        android:theme="@style/AppTheme.NoActionBar">
        <!-- 생략 -->
</activity>
```

다음과 같이 확인할 수 있지만 `onResume`을 호출하지 않아서 안전한 처리가 가능합니다.

```java
@Override
public void onConfigurationChanged(Configuration newConfig) {
    super.onConfigurationChanged(newConfig);

    Log.d("TAG", "config : " + (newConfig.orientation) + ", newConfig.screenLayout : " + newConfig.screenLayout);
    Log.e("TAG", "newConfig.densityDpi : " + newConfig.densityDpi);
    Log.i("TAg", "uiMode : " + newConfig.uiMode);
    Log.i("TAg", "smallestScreenWidthDp : " + newConfig.smallestScreenWidthDp);
    Log.i("TAg", "screenWidthDp : " + newConfig.screenWidthDp + ", screenHeightDp : " + newConfig.screenHeightDp);
}
```

Android N부터는 사실 기존에 지키지 않던 onResume의 활용을 적극적으로 지켜주어야 합니다.(그런 것을 생각해보면 삼성이 멀티윈도우 API를 참 대응 잘하는 느낌입니다.)


<br />

## 마무리

Android 누가의 화면 사이즈 변화에 대해서 정리하려고 했었는데 이제야 정리하게 되었습니다. 바로 갤럭시 노트 7 덕분이네요. 화면 사이즈를 저런 식으로 변화를 해줄지는 몰랐습니다.

좀 아쉬운 건 Android N부터 제공되는 API라서 하위 버전에서는 `onResume`을 통해서 체크할 수 있다는 점입니다.

아쉽지만 그렇게라도 대응을 해주어야 사용성에 지장이 없는 것이죠.(대응하지 않으면 UI 깨짐을 보실 수 있습니다.)

사실 Target API 24로 올렸을 때 가장 좋은 방식으로 대응이 가능하겠지만, 모든 앱이 Target API 24를 사용 가능한 사항은 아닙니다. 그래도 해야할건 해야하니...


<br />

원래 화면 사이즈는 adb 명령을 통하거나, CM 롬 등의 커스텀 롬에서 제공하던 기능입니다. adb 명령으로 동작한다는 건 안드로이드 자체에서 이미 화면 사이즈를 대응하고 있었다는 뜻이기도 합니다.

그래서 중국 제조사의 폰에서는 언제든지 이런 기능을 활성화 가능하고, Android 6.0 이상에서도 언제든지 적용 가능한 부분이 됩니다.
특히나 삼성은 이러한 기능을 통해 배터리 절약도 함께 하고 있으니... 다른 제조사에서도 적극적으로 추가할 수 있다는 말이 됩니다.


<br />

안드로이드 화면 사이즈에 변화를 대응하기 쉽게 dp를 사용하고 있으므로, 큰 문제는 없지만 일부 개발에서 화면의 pixel 사이즈를 직접 가져와서 사용하게 되는 건 피할 수 없습니다. 그에 따른 대응을 적절하게 해주시는게 필요합니다.

[screenSizeSetting]:   /images/2016/2016-08-27-Android-N-ScreenSizeChangeEvent/device-2016-08-27-162247.png
[AndroidNScreenSize]:  /images/2016/2016-08-27-Android-N-ScreenSizeChangeEvent/device-2016-08-27-164413.png
