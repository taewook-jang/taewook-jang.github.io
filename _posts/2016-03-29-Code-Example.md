---
layout: post
title: example
---

안드로이드 N에서 정식으로 Multi Window를 제공합니다. N에 숨겨진 멀티 윈도 기능도 있는데 그 기능을 사용하는 방법도 함께 적어보겠습니다.

Google [lan Lake](https://medium.com/@ianhlake)가 medium에 작성한 "[5 tips for preparing for Multi-Window in Android N](https://medium.com/google-developers/5-tips-for-preparing-for-multi-window-in-android-n-7bed803dda64#.x5dd4ku7n)"
을 참고하시면 좋을 것 같습니다. 저는 주요 코드와 설명을 작성하도록 하겠습니다.(5가지 팁 중에 중요한 내용 포함...)

##Android N Multi Window##
Android N에 포함된 Multi Window는 Andoid M에도 있었습니다. 초기 버전에만 존재하는데 Nexus 6p에서만 확인이 되었던 적이 있습니다. 개발자 옵션에 잠시 포함되었다가 11월 패치쯤에 해당 기능이 사라졌었습니다.

동작 방식은 태스크에 표시된 미리 보기 창에 멀티윈도우 옵션을 제공하고, 이를 동작하는 정도였습니다.

이러한 기능이 좀 더 안정적으로 Android N에 정식으로 포함되었습니다. Android N에서는 폰과 Tablet에 제공하게 되는데 Tablet에서는 좀 더 PC처럼 사용할 수 있는 기능이 OFF로 숨겨져 있습니다. ***(이 기능은 아래쪽에 설명하도록 하겠습니다.)***


##Multi Window Support##
Preview 문서 : [Multi Window Support](http://developer.android.com/preview/features/multi-window.html)

**스마트폰** : 스마트폰에서는 상/하 모드로 Split-Screen mode를 지원합니다.
구글 문서에 나와 있는 사진입니다. 아래와 같이 화면 분할이 되고, 기존 Overview screen(태스크?) 모양이 2개의 네모로 분리됩니다.
![Screenshot 2016-03-28 22.18.59.png]({{ site.baseurl }}/images/2016/2016-03-29-Android-N-Preview-Multi-Window/Screenshot 2016-03-28 22.18.59.png)

Tablet은 별도의 설명이 없습니다. 기본 기능은 모두 스마트폰과 동일하게 동작합니다. 다만 태블릿에서는 화면 회전을 지원하지 않을 경우 분할 모드를 지원하지 않습니다.

SDK를 통해 멀티윈도우를 제어할 수 있습니다. 창의 최소 사이즈를 정할 수 있고, 멀티 윈도우를 사용하지 않도록 지정할 수 있습니다.


**TV Device** : Picture-in-picture(PIP) 모드를 지원합니다.

TV Device에서 사용할 수 있는 Picture-in-picture(PIP)는 영상을 시청 중에 다른 행동을 할 수 있습니다. 영상을 틀어놓고 사진을 볼 수 있는데 일반적인 TV/모니터의 PIP 기능이라고 볼 수 있겠습니다.(팝업 플레이어? 정도라고 생각할 수도 있겠네요)
[Picture-in-picture](http://developer.android.com/preview/features/picture-in-picture.html)
구글 문서에 나와있는 사진입니다.
![Screenshot 2016-03-28 22.17.54.png]({{ site.baseurl }}/images/2016/2016-03-29-Android-N-Preview-Multi-Window/Screenshot 2016-03-28 22.17.54.png)


##Activity Lifecycle##
Multi-window mode를 지원한다고 해서 별도로 달라지는 Lifecycle은 없다고 합니다. 기본 (Android Activity의 Lifecycle)[http://developer.android.com/training/basics/activity-lifecycle/index.html]을 따른다고 합니다.

그래서 현재 동영상을 시청하는데 있어서 멀티윈도우가 정상 동작하지 않습니다. 멀티윈도우 상태에서 동영상을 시청하려고 틀어놓고 다른 행동을 하려고 시도하였는데 이렇게 동작하였습니다.(문서상 내용도 비슷합니다.)

- 동영상 APP을 실행하여 동영상을 플레이합니다.
- 멀티 윈도우의 다른 화면을 눌러줍니다.
- 동영상은 재생이 멈춥니다.

하나씩 Android Lifecycle대로 설명하면 다음과 같습니다.
- 동영상을 정상으로 Player 하였습니다.(onResume 상태입니다.)
- **멀티윈도우의 다른 화면을 눌러줍니다.** 이때 동영상의 화면은 onPause()를 타게 됩니다.
- 선택한 다른 화면이 onResume()를 타고 정상적으로 사용 가능해집니다.

**Google Note.**
화면이 보이기 때문에 onStop()이 불리지는 않습니다. 문서의 Note에는 다음과 같은 Tip을 제공하고 있습니다.
- Player 하기 위해서 onPause()에서는 일시정지/정지 하지 않도록 한다.
- onStop()으로 일시정지/정지하도록 한다

위와 같이 처리해주면 onPause()가 되더라도 동영상 플레이는 유지되고, 화면이 보이는 상태이기 때문에 onStop()이 호출되지는 않습니다.
다시 돌아왔을 때도 onResume()에서 동영상 재생을 하지 않고, onStart()에서만 start를 하도록 해주면 onPause와 onResume이 왔다 갔다 하는 상항에서도 문제없이 플레이가 가능해집니다.

아직은.. Google App도 이렇게 동작하지 않아서 확인은 불가능합니다.. 저도 아직 직접 확인해보지 않은 내용이라서... 일단 문서상...

[Handling Runtime Changes](http://developer.android.com/guide/topics/resources/runtime-changes.html)에 관한 내용도 담고 있습니다. 자세한 내용은 문서를 참조해주세요.


##Configuring Your App for Multi-Window Mode##
Multi-Window 모드는 개발자가 직접 컨트롤 할 수 있습니다. 멀티윈도우를 지원하지 않을 수도 있고(현재 Preview에서는 모두 다 지원하도록 되어 있어서 정식 버전에 어떻게 적용될지 아직은 모르겠습니다.) 지원하면서 최소 사이즈를 지정할 수 있습니다.

AndroidManifest.xml에서 다음을 정의하면 Multi-Window를 지원/해제 할 수 있습니다.

```java
android:resizeableActivity=["true" | "false"]
```

이 코드를 &lt;actiity&gt; 또는 &lt;application&gt;에 각각 넣을 수 있습니다.
application이 false 이면 해당 앱은 false가 기본으로 처리되어 멀티 윈도우가 동작하지 않습니다.
activity 기반으로도 각각 페이지별 true/false를 적용하게 되면 동작이 다르게 되는 것이죠.

그래서 저는 이런 방법을 시도해보았습니다.
- A Activity에서는 true
- B Activity에서는 false

아직 Preview라서 그런지 첫 번째 Activity에 맞게 B Activity가 동작을 하였습니다. 다시 말하면 B는 true/false이 든 상관없이 동작하였습니다.

저런 식의 화면 구성을 하고 싶어 하시는 분이 있을 것 같습니다. 특정 화면은 집중을 요해서 전체 화면으로 구성을 하고 싶은 경우입니다. 그런데 이 경우는 별로 추천하지는 않을 것 같지만.. 현재 &lt;activity&gt;에 true/false를 지원하는 것을 보아 가능할 것 같습니다.

Picture-in-picture 역시 아래와 같습니다.
```java
android:supportsPictureInPicture=["true" | "false"]
```
