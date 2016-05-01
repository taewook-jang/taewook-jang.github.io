---
layout: post
title: MediaProjection 예제에 MVP 패턴을 적용해보았다.
published: false
---

지난번에 작성하였던 MediaProjection을 MVP 패턴을 적용해보았습니다.

[안드로이드 화면 캡쳐 API - MediaProjection 사용해보기](http://thdev.tech/Android-MediaProjection-Exmple/)

[GitHub MediaProjection 예제](https://github.com/taehwandev/MediaProjectionExample)

Model View Presenter을 구분하여 리팩토링을 진행해보았습니다.

<br />

## MVC(Model View Controller)

View에서 Controller에 요청하고, Model에서 처리하거나, View에서 모델에 직접적인 데이터를 처리하게 됩니다.

예를 들면 **Activity > Adapter > Activity**

Adapter에서 onClick, onTouch를 Activity가 받아서 직접 처리하는 형태가 될 수 있겠습니다.

![MVC](/images/2016/2016-05-04-MediaProjection-MVP-Pattern/MVC.png)

<br />

## MVP(Model View Presenter)

MVP는 Model View Presenter을 말합니다.

MVC에서는 View에서 많은 부분을 처리하다 보니 깔끔한 코드라고 하기엔 부족한 것 같습니다.

<br />

**MVP의 장점**

Activity가 완전히 View로서의 역할을 할 수 있게 되었습니다.

**예)** Button을 누른다면 다음과 같이 분리된 코드가 나옵니다.

**View(Button Event) -> Presenter -> View(Activity)**

Model이 필요한 경우에 처리를 할 수 있지만 제가 작성한 MediaProjection에서는 Model의 역할이 명확하게 구분되어 있지는 않습니다.

그림으로 정리하면 아래와 같습니다.

![MVP](/images/2016/2016-05-04-MediaProjection-MVP-Pattern/MVP.png)

<br />

## 주요 코드

MVP를 적용해본 MediaProjection 예제입니다.

[MediaProjection Example](https://github.com/taehwandev/MediaProjectionExample)

크게 MediaProjectionLibrary가 있고, MediaProjection 예제가 담겨 있습니다.

이번에는 MediaProjectionLibrary에 대해서 간단하게 정리하겠습니다.

### Presenter/View - MediaProjectionAccessPresenterView

interface로 처리하고, View에서 상속받아 사용합니다.

```java
void onMediaProjectionAccessStatus(@MediaProjectionConstant.ProjectionStatusType int type)
```

### View - MediaProjectionAccessActivity

추상 클래스로 작성되었고, Presenter를 가지고 있으며, MediaProjectionAccessPresenterView 상속받아 View를 처리하게 됩니다.(추상 클래스로 해당 클래스에선 받지 않습니다.)

MediaProjection의 권한을 받고, 그에 대한 Event를 처리하게 됩니다.

```java
MediaProjectionAccessPresenter mediaProjectionAccessPresenter
```

분리하기 전에는 다음과 같았습니다.

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
  if (requestCode == REQUEST_CODE) {
    // 사용자가 권한을 허용해주었는지에 대한 처리
    if (resultCode != RESULT_OK) {
      // 사용자가 권한을 허용해주지 않았습니다.
      return;
    }
    ...
  }
  super.onActivityResult(requestCode, resultCode, data);
}
```

하지만 이제 이 코드가 Presenter로 이동하게 되었기에 다음과 같이 정의하고, 실제 코드는 Presenter가 가지게 됩니다.

```java
mediaProjectionAccessPresenter.initMediaProjection(resultCode, data);
```

<br />

### Presenter - MediaProjectionAccessPresenter

PresenterView를 가지고 있고, Event에 대한 처리가 완료되면 View에게 이를 통보하게 됩니다.

View(Activity)에서는 이 View 이벤트를 전달받아 UI 처리를 진행하면 됩니다.

```java
// Activity에서 사용할 Presenter 처리를 가집니다.
public MediaProjectionAccessPresenter(Activity activity, MediaProjectionAccessView view, MediaProjectionManager mediaProjectionManager)
```

MediaProjection 처리에 필요한 데이터(넓이, 높이, activity, Projection name)의 정보를 가진 Params을 세팅 받습니다.

```java
public void setControllerParams(MediaProjectionControllerParams controllerParams)
```

MediaProjection에 대한 onActivityResult를 처리하게 됩니다.

처리가 완료되면 View에 Status 정보를 넘겨주게 됩니다.

```java
public void initMediaProjection(int resultCode, Intent data)
```

destroy 처리가 완료되면 View에 Status 정보를 넘겨주게 됩니다.

```java
public void destroyMediaProjection()
```

<br />

## 마무리

간단하게나마 Model View Presenter를 구현해보았습니다.

이게 맞는지는 모르지만... 다음에 MVP를 기반으로 한 AndroidTest 예제도 올려보려고 합니다.
