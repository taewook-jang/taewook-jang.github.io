---
layout: post
title: Android Studio 2.2에서 Layout Preview 한글 깨짐 현상 해결 방법
categories: [AndroidDev]
tags: [Android Dev, Android Studio]
fullview: false
comments: true
published: true
---

미국시간 기준 9월 19일에 Android Studio 2.2 정식 버전이 배포되었습니다.

![android-studio-update]


<br />

## Android Studio 한글 깨짐 문제

Android Studio의 한글 깨짐 문제가 발생하였습니다.

이미 Preview 때부터 그랬는데 정식 버전이 되었지만 그대로... 가지고 와버렸습니다.

![android-studio-preview-not-support-korean]

위와 같이 Android Layout 미리 보기에서 한글이 깨지는 현상이 발생합니다.


<br />

## 해결 방법

해결 방법은 [전병권](https://www.facebook.com/skyisle) 님이 [GDG Korea - Android](https://gdgkr.slack.com/)에서 2.2 RC에서 해결하는 방법을 올려주셨었습니다.

다음과 같은 방법으로 해결이 가능합니다.

맥에서 다음의 명령어를 이용하시면 패치가 가능합니다.

```
curl https://gist.githubusercontent.com/skyisle/4d98cbcdc259601fba0f07602667b1b9/raw/0da59a462366f2d5165e112648a549cb705e9e15/korean_font.diff | patch -p1 -d /Applications/Android\ Studio.app/Contents/plugins/android/lib/layoutlib/data/fonts/
```

뒤쪽의 `-d` 이후 경로는 자신의 설치 경로에 따라 조금씩 다릅니다. 확인하셔서 적용하시면 되겠습니다.

윈도우에서는 다음의 경로를 찾으셔서 직접 수정해주셔야 합니다.

Android Studio 2.2가 설치된 경로에서 다음의 경로를 추가로 따라가셔서 `fonts.xml`을 열어 추가해주시면 되겠습니다.

```
plugins/android/lib/layoutlib/data/fonts/fonts.xml
```

참고로 fonts의 `fonts.xml`에는 다음과 같은 내용이 담겨있습니다.


```xml
<family lang="ko">
  <font weight="400" style="normal" index="1">NotoSansCJK-Regular.ttc</font>
</family>
<family lang="und-Zsye">
  <font weight="400" style="normal">NotoColorEmoji.ttf</font>
</family>
```

전병권 님은 여기에 다음을 추가하여 패치해주셨습니다.

결국 한국어 폰트가 존재하지 않아서 생기는 문제였습니다.

```xml
<family lang="ko">
  <font weight="400" style="normal" index="1">NanumGothic.ttf</font>
</family>
```


<br />

## 마무리

패치를 해주시고 나면 다음과 같이 확인이 가능합니다.

한글이 잘 나옵니다.

![android-studio-result]




[android-studio-update]: /images/2016/2016-09-21-Android-Studio-Layout-Preview-Not-Korean/android-studio-update.png

[android-studio-preview-not-support-korean]: /images/2016/2016-09-21-Android-Studio-Layout-Preview-Not-Korean/android-studio-preview-not-support-korean.png

[android-studio-result]: /images/2016/2016-09-21-Android-Studio-Layout-Preview-Not-Korean/android-studio-result.png
