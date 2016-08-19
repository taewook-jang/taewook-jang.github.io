---
layout: post
title: Android Studio heap 메모리 늘리기
categories: [DevTools]
tags: [Android, Android Studio]
fullview: false
comments: true
published: true
---

Android Studio의 heap 메모리를 늘려서 쾌적한 개발을 진행할 수 있습니다.

기본 메모리 설정이 1GB 정도로 되어 있기에 맥북에서 개발을 하다 보면 펜이 돌기 십상입니다. 그럴 때 heap 메모리를 조금 늘려주면 펜이 도는 일이 줄어들 수 있습니다.

<br />

**주의 : Android Studio를 업데이트하는 과정에서 `studio.vmoptions`가 초기화될 수 있습니다. 그럴 경우 다음의 과정을 다시 진행해주어야 합니다.**

<br />

**다음의 글은 맥을 기준으로 설명합니다.**

## 맥에서 메모리 설정 변경하기

윈도우에서는 `Android Studio` 설치 경로에 가시면 `bin` 폴더가 존재합니다. `bin` 폴더 안에 `studio.exe.vmoptions`라는 파일이 있습니다. 이를 열어주시면 되고, 맥에서는 아래와 같습니다.

Android Studio가 설치된 폴더로 이동합니다.

아마 `응용 프로그램`에 존재하게 될 겁니다. 해당 폴더에서 `Android Studio`를 오른쪽 마우스를 눌러줍니다.

그럼 다음과 같이 `패키지 내용 보기(Show Packages Contents)`가 보이게 됩니다.

![Screenshot 2016-08-14 10.27.21.png](/images/2016/2016-08-19-AndroidStudio-heap-Memory-Upgrade/Screenshot 2016-08-14 10.27.21.png)

열었다면 다음과 같이 `Contents` 폴더가 하나 보이게 됩니다.

![Screenshot 2016-08-14 10.27.26](/images/2016/2016-08-19-AndroidStudio-heap-Memory-Upgrade/Screenshot 2016-08-14 10.27.26.png)

다음으로 폴더가 여러 개 보이는데 이 중에서 `bin`폴더를 열어줍니다.

![Screenshot 2016-08-14 10.27.31](/images/2016/2016-08-19-AndroidStudio-heap-Memory-Upgrade/Screenshot 2016-08-14 10.27.31.png)

`studio.vmoptions`라는 아이템이 있습니다. 이를 실행하거나 메모 어플에 `드래그 앤 드롭`합니다.

![Screenshot 2016-08-14 10.27.35](/images/2016/2016-08-19-AndroidStudio-heap-Memory-Upgrade/Screenshot 2016-08-14 10.27.35.png)

만약 `드래그 앤 드롭`으로 실행한 게 아니라면 다음과 같이 실행 가능한 응용 프로그램을 찾으려고 할 겁니다.

![Screenshot 2016-08-14 10.27.47.png](/images/2016/2016-08-19-AndroidStudio-heap-Memory-Upgrade/Screenshot 2016-08-14 10.27.47.png)

실행하려는 응용 프로그램 중 xcode 등의 편집이 가능한 프로그램을 지정해주시면 됩니다.

저는 `ATOM 에디터`를 기본으로 지정해주었습니다.

- Xms : 초기 메모리 할당
- Xmx : 최대 메모리
- MaxPermSize : 영구 영역(Permanent Generation)에 해당되며, 기본 사이즈는 350m
- ReservedCodeCacheSize : 예약된 코드 캐시 사이즈, 기본 사이즈는 240m

```
-Xms256m
-Xmx1280m
-XX:MaxPermSize=350m
-XX:ReservedCodeCacheSize=240m
-XX:+UseCompressedOops
```

자신의 시스템에 맞게 설정해주는 게 좋습니다. 저는 16GB 이상의 메모리를 가지고 있어서 다음과 같이 설정하였습니다.

```
-Xms2048m
-Xmx4096m
-XX:MaxPermSize=1024m
-XX:ReservedCodeCacheSize=768m
-XX:+UseCompressedOops
```

영구 영역에 해당되는 `MaxPermSize`과 `ReservedCodeCacheSize`는 지정하지 않는 경우도 있지만 저는 위와 같이 지정해주었습니다.


<br />

## Android Studio에서 확인하기

해당 `studio.vmoptions`을 수정하였다면 Android Studio는 꼭 껐다가 다시 켜주어야 합니다. 그리고 `맥에서는 바로 가기 링크를 통해 접근하게 되면 메모리가 늘어나지 않으니 원본 앱을 이용해서 접근`하시기 바랍니다.

Android Studio에서 메모리를 설정하기 위해서는 다음과 같이 하시면 됩니다.

Android Studio의 설정으로 이동합니다.

설정에서 아래와 같은 순서로 이동합니다

1. Appearance & Behavior
2. Appearance
3. Sho memory indicator 체크
4. 적용 또는 확인

![Screenshot 2016-08-14 10.27.47.png](/images/2016/2016-08-19-AndroidStudio-heap-Memory-Upgrade/Screenshot 2016-08-14 10.27.47.png)

그럼 안드로이드 Studio 한편에 다음과 같이 확인이 가능합니다.

기본 heap 사이즈와 현재 사용하고 있는 실제 사이즈, 최대 사이즈까지 모두 명시됩니다.

추가로 해당 부분을 눌러주면 GC가 돌게 되는데 AS의 메모리 초기화를 하게 됩니다.

![Screenshot 2016-08-14 10.28.55.png](/images/2016/2016-08-19-AndroidStudio-heap-Memory-Upgrade/Screenshot 2016-08-14 10.28.55.png)


<br />

## 마무리

간단하게 Android Studio의 메모리 늘리는 방법을 살펴보았습니다.

자신의 시스템에 맞는 메모리 최대 용량을 설정해주시는 게 좋겠습니다.

대략 1024, 2048, 4096 등으로 지정해주시면 될 것 같습니다.
