---
layout: post
title: 화면을 가져올 수 있는 Android MediaProjection API
isShow: false
---

안드로이드 5.0(Lollipop : API 21)부터 Screen 데이터를 가져올 수 있는 API가 추가되었습니다.

구글에서도 이를 이용한 게임 녹화 앱을 만들었습니다.
 **[Youtue Game app(미국에서만 다운로드 가능합니다)](https://play.google.com/store/apps/details?id=com.google.android.apps.youtube.gaming&hl=en)**

MediaProjection API이고, 이 API는 안드로이드 5.0(API 20)을 minSdk로 설정하면 사용할 수 있습니다.

<br />

## Android MediaProjection

 MediaProjection에 대한 문서가 다른 API에 비해서 잘 정리되어 있지는 않습니다. 별도의 예제를 찾아서 MediaProjection 사용 방법을 익혀야 합니다.

[MediaProjection API 문서](http://developer.android.com/reference/android/media/projection/package-summary.html)

위의 링크의 API 문서에는 아래와 같은 3가지 API가 정의되어 있습니다.

- [MediaProjection](http://developer.android.com/reference/android/media/projection/MediaProjection.html)
  - A token granting applications the ability to capture screen contents and/or record system audio.
  - 화면의 데이터를 가져오거나 음성 데이터를 가지고 가져오기 위한 API 입니다. 실제 동작은 영상만 가능합니다.
- [MediaProjection.Callback](http://developer.android.com/reference/android/media/projection/MediaProjection.Callback.html)
  - Callbacks for the projection session.
  - MediaProjection 처리에 대한 Callback을 받을 수 있습니다.(Stop 등)
- [MediaProjectionManager](http://developer.android.com/reference/android/media/projection/MediaProjectionManager.html)
  - Manages the retrieval of certain types of MediaProjection tokens.
  - MediaProjection에 대한 권한을 처리합니다.
- [VirtualDisplay](http://developer.android.com/reference/android/hardware/display/VirtualDisplay.html)
  - MediaProjection API에는 포함되어 있지 않지만 중요한 API입니다. 생성된 가상화면을 사용할 수 있는 API입니다.

<br />
위의 4개 API를 통해서 MediaProjection을 적용해볼 수 있습니다.<br />
구글에서 배포중인 MediaProjection에 관현 예제가 있으니 함께 참고하시면 되겠습니다.<br />

- [MediaProjectionDemo](https://android.googlesource.com/platform/development/+/master/samples/ApiDemos/src/com/example/android/apis/media/projection/MediaProjectionDemo.java)
  - MediaProjection을 640*360, 960*540, 1366*768, 1600*900 으로 테스트하는 MediaProjection 예제입니다.
  - 사이즈는 캡쳐한 화면의 크기를 말합니다.

<br />
제가 직접 작성한 예제를 통해서 자세하게 살펴보겠습니다.

<br />

## MediaProjection API 사용법

세부 API를 살펴보기 전에 전체 사용법을 간단하게 살펴보겠습니다.<br />
크게 2단계를 통해 설명하도록 하겠습니다.<br /><br />

**1단계**<br />
MediaProjectionManager는 getSystemService를 통해 service를 생성하고, 사용자에게 권한을 요구하게 됩니다.

```java
// 시스템의 Projection service를 획득합니다.
MediaProjectionManager mpm = (MediaProjectionManager) getSystemService(Context.MEDIA_PROJECTION_SERVICE);

// 실제 권한을 사용자에게 통보하고 권한을 요구하게 됩니다.
startActivityForResult(mpm.createScreenCaptureIntent(), REQUEST_CODE);
```

**2단계**<br />
사용자에게 권한을 획득 받음 다음 onActivityResult를 통해 MediaProjection을 사용하게 됩니다.<br />

 - Callback을 처리
 - VirtualDisplay 생성

```java
// 종료와 시작을 정의하기 위해서 전역으로 정의해주었습니다.
/**
 * MediaProjection을 사용하기 위한 객체입니다.
 */
private MediaProjection mediaProjection;

/**
 * 실제 화면의 크기를 정하고, Surface를 통해 화면을 그리게 됩니다
 */
private VirtualDisplay virtualDisplay

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
  if (requestCode == REQUEST_CODE) {

    // 사용자가 권한을 허용해주었는지에 대한 처리
    if (resultCode != RESULT_OK) {
      // 사용자가 권한을 허용해주지 않았습니다.
      return;
    }

    // 사용자가 권한을 허용해주었기에 mediaProjection을 사용할 수 있는 권한이 생기게 됩니다.
    mediaProjection = projectionManager.getMediaProjection(resultCode, data);
    if (mediaProjection != null) {
      // MediaProjection에 대한 Event 정보를 받으려면 아래와 같이 적용하시면 됩니다.
      mediaProjection.registerCallback(new MediaProjectionCallback(), null);
      virtualDisplay = mediaProjection.createVirtualDisplay("VirtualDisplay name", 화면 넓이, 화면 높이, 화면 density, flag, surface(화면을 출력할 곳), null /* Callbacks */, null /* Handler */);
    }
  }

	super.onActivityResult(requestCode, resultCode, data);
}
```

<br />
2단계로 시스템에서 제공하는 화면을 가져올 수 있습니다.<br />
오늘은 [TextureView](http://developer.android.com/reference/android/view/TextureView.html)를 이용하여 실시간 화면을 출력하는 예제를 작성해보도록 하겠습니다.

<br />
**상세 코드 확인**<br />
간단하게 코드를 알아보았으니 API의 각각 상세를 살펴보겠습니다.<br />
API를 사용하는 순서대로 다음의 순서대로 설명하도록 하겠습니다.

a. [MediaProjectionManager](http://developer.android.com/reference/android/media/projection/MediaProjectionManager.html) : 권한 획득을 위함
b. [MediaProjection](http://developer.android.com/reference/android/media/projection/MediaProjection.html) : 실제 MediaProjection을 사용하기 위한 객체
c. [MediaProjection.Callback](http://developer.android.com/reference/android/media/projection/MediaProjection.Callback.html) : MediaProjection을 사용하는 중 발생한 Callback
d. [VirtualDisplay](http://developer.android.com/reference/android/hardware/display/VirtualDisplay.html) : 가상의 화면을 생성하기 위한 객체(MediaProjection을 통해 생성)

<br />
**a. MediaProjectionManager**

MediaProjectionManager는 system의 Service를 생성하고, 사용자에게 권한을 요구하기 위한 API 입니다.<br />
사용자에게 권한을 요구하기 전에 SystemService를 생성해주어야 합니다.<br />
Context.[MEDIA_PROJECTION_SERVICE](http://developer.android.com/reference/android/content/Context.html#MEDIA_PROJECTION_SERVICE)를 이용하여 MediaProjectionManager를 생성하게 됩니다.
<br />

```java
MediaProjectionManager getSystemService(Context.MEDIA_PROJECTION_SERVICE);
```

MediaProjectionManager가 생성되면 사용자에게 통보하여 권한 획득을 요청 하는 단계가 필요합니다.

```java
// API는 아래와 같습니다.
Intent createScreenCaptureIntent()

// 실제 요청은 다음의 코드를 이용하게 됩니다.
startActivityForResult(mpm.createScreenCaptureIntent(), REQUEST_CODE);
```

사용자는 다음과 같은 창이 표시되었을때 허용/취소을 할 수 있게 됩니다.<br />

/// image

사용자가 동의하면 resultCode는 RESULT_OK가 떨어지고 아니라면 그 외의 값이 떨어지게 됩니다. 그에 대한 처리는 다음과 같이 할 수 있습니다.

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
  if (requestCode == REQUEST_CODE) {

    // 사용자가 권한을 허용해주었는지에 대한 처리
    if (resultCode != RESULT_OK) {
      // 사용자가 권한을 허용해주지 않았습니다.
      return;
    }

    // ...
  }

	super.onActivityResult(requestCode, resultCode, data);
}
```

위와 같이 REUSLT_OK가 처리되었다면 다음의 코드를 통해 MediaProjection을 생성해야 합니다.<br />
onActivityResult에서 전달받은 resultCode와 Intent data를 셋팅하면 됩니다.

```java
MediaProjection getMediaProjection(int resultCode, Intent resultData)
```

MediaProjectionManager를 통해 부여 받은 권한으로 MediaProjection이 생성됩니다.

<br />
**b. [MediaProjection](http://developer.android.com/reference/android/media/projection/MediaProjection.html)**

MediaProjection으로 가상의 화면을 생성하고, MediaProjection Callback을 처리하도록 셋팅을 해줄 수 있습니다.<br />

Callback을 처리합니다. Handler는 굳이 필요하지 않다면 null처리하시면 됩니다.

```java
// MediaProjection.Callback을 셋팅하고 처리합니다.
void registerCallback(MediaProjection.Callback callback, Handler handler)

// MediaProjection.Callback을 해제 합니다.
void unregisterCallback(MediaProjection.Callback callback)
```

VirtualDisplay를 생성합니다.<br />
name : 가상의 화면에 사용할 이름입니다. 임의로 지정 가능하지만 empty 상태로 설정하면 안됩니다.<br />
width : 캡쳐할 화면의 넓이(Pixels)<br />
height : 캡쳐할 화면의 높이(Pixels)<br />
dpi : 캡쳐할 화면의 dpi 사이즈(dpi) : DisplayMertics를 통해 얻어온 값을 사용하면 됩니다.<br />

```java
DisplayMetrics metrics = new DisplayMetrics();
getWindowManager().getDefaultDisplay().getMetrics(metrics);
int screenDensity = metrics.densityDpi;
```

flag : 가상 화면을 생성하는데 필요한 flag 입니다. 이 flag의 선언에 따라서 다른 가상 화면을 허용할지 말지를 결정할 수 있습니다. 전체 리스트는  [DisplayManager](http://developer.android.com/reference/android/hardware/display/DisplayManager.html)를 참고하세요.

- VIRTUAL_DISPLAY_FLAG_AUTO_MIRROR
  - 사용자 동의를 얻어 처리합니다.
- VIRTUAL_DISPLAY_FLAG_OWN_CONTENT_ONLY
  - 동의된 사용자에게만 미러링을 제공합니다.
- VIRTUAL_DISPLAY_FLAG_PRESENTATION
  - Presentation display을 생성합니다.
- VIRTUAL_DISPLAY_FLAG_PUBLIC
  - Public display을 생성합니다.
- VIRTUAL_DISPLAY_FLAG_SECURE
  - Secure display을 생성합니다.
  - 대략적으로 설명하면 다음과 같습니다.
  - A 앱에서 미러링을 사용하고, B에서도 사용을 하게 해줄것인가에 대한 여부를 결정합니다. VIRTUAL_DISPLAY_FLAG_OWN_CONTENT_ONLY로 선언하게 되면 허용하지 않게됨으로 MediaProjection이 중지될 수 있습니다.
  - surface : 캡쳐한 가상 화면을 출력할 Surface를 셋팅합니다.(오늘 예제에서 확인이 가능합니다.)
- callback : MediaProjection.Callback과는 다르며, [VirtualDisplay.Callback](http://developer.android.com/reference/android/hardware/display/VirtualDisplay.Callback.html)을 셋팅합니다.<br />
  - 다음의 상태를 알수 있습니다.
  - onPaused() : 시스템이나 Surface가 detached 되었을 경우 호출됩니다.(setSurface(null))
  - onResumed() : 시작되었을 경우
  - onStopped() : 시스템에서 정지되었을 경우(완전 종료에 해당)
- handler : Handler를 셋팅합니다.

전체적인 변수 설명은 위와 같으며, 메소드는 아래와 같습니다.

```java
VirtualDisplay createVirtualDisplay(String name, int width, int height, int dpi, int flags, Surface surface, VirtualDisplay.Callback callback, Handler handler)

// 다음과 같이 사용합니다.
mediaProjection.createVirtualDisplay()
```

<br />
**c. [MediaProjection.Callback](http://developer.android.com/reference/android/media/projection/MediaProjection.Callback.html)**

MediaProjection.Callback은 MediaProjection을 사용하는 중에 호출됩니다.<br />
onStop()에 대한 이벤트를 제공합니다.

```java
void onStop()
```

<br />
**d. [VirtualDisplay](http://developer.android.com/reference/android/hardware/display/VirtualDisplay.html)**

MediaProjection의 createVirtualDisplay를 통해서 생성된 VirtualDisplay입니다.<br />
VirtualDisplay는 다음의 API를 제공합니다.

```java
// 가상 display를 return 합니다.
Display getDisplay()
// 가상 display에서 사용하는 surface를 return 합니다.
Surface getSurface()
// destroy
void release()
// size 변경
void resize(int width, int height, int densityDpi)
// Surface를 셋팅합니다.
void setSurface(Surface surface)
```

<br />

## Example

MediaProjection을 테스트하기 위해 작성한 예제입니다.<br />
Android 4.0(API 14)에 적용된 [TextureView](http://developer.android.com/reference/android/view/TextureView.html)를 활용하였습니다.<br />
