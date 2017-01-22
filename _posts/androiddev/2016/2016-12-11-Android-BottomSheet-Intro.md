---
layout: post
title: Android BottomSheet 사용하기
categories: [AndroidDev]
tags: [Android, Android Design]
fullview: false
comments: true
published: true
---

Android Design Support Library 23.2.0부터 사용이 가능한 Android BottomSheet입니다.

메트리얼 디자인과 함께 소개되었던 부분인데 Support Library 23.2.0에 추가되었습니다.

Google에서는 다음과 같은 방식 2가지로 BottomSheet을 사용 중에 있습니다.

왼쪽에는 일반 `BottomSheet`이고, 오른쪽에는 다이얼로그 형태의 BottomSheet가 적용된 상태입니다.

이번 글에서는 일반 BottomSheet을 소개합니다.

![bottom_sheet_01]


<br />

## BottomSheet API

- [Meterial BottomSheet](https://material.google.com/components/bottom-sheets.html)
- [BottomSheetBehavior API](https://developer.android.com/reference/android/support/design/widget/BottomSheetBehavior.html?utm_campaign=android_launch_supportlibrary23.2_022216&utm_source=anddev&utm_medium=blog)
  - BottomSheet을 컨트롤하기 위한 Behavior API


<br />

## BottomSheet 적용하기

BottomSheet는 Design Support 라이브러리의 `CoordinatorLayout`에 포함되는 경우에 사용이 가능하고, 적용 가능한 레이아웃이 따로 정해져있지 않고, `Behavior` 정의로 간단하게 적용이 가능합니다.

제가 예제로 만든 샘플은 아래와 같습니다.

왼쪽은 리사이클러뷰를 포함한 예제이고, 오른쪽은 TextView를 추가한 예제입니다.

![bottom_sheet_02]

두 예제 모두 `RelativeLayout`에 `Behavior` 정의를 사용하였습니다.

- [BottomSheet API 예제 - GitHub](https://github.com/taehwandev/Android-BlogExample/tree/01-BottomSheetExample)


<br />

### BottomSheet을 적용하기 위한 라이브러리 적용

`Gradle`에 다음을 추가하고, `23.2.0` 이상을 적용해야 합니다.

```
compile "com.android.support:design:23.2.0"
```


<br />

### 레이아웃 정의하기

모든 레이아웃에 `layout_behavior` 정의만 해주면 `BottomSheet` 적용이 가능합니다.

전제 조건은 다음과 같습니다.

- `CoordinatorLayout` 안에 포함되어야 합니다.
- `app:layout_behavior`을 정의해야 합니다.
- `app:behavior_peekHeight`을 통해 기본 높이를 설정할 수 있습니다.
- `app:behavior_hideable`을 통해 항상 표시하는 BottomSheet을 정의할 수 있습니다.
  - 항상 표시되는 경우는 `peekHeight`의 높이만큼 표시됩니다.

```xml
<android.support.design.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="true">

    <RelativeLayout
        android:id="@+id/rl_bottom_sheet"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:behavior_hideable="true"
        app:behavior_peekHeight="200dp"
        app:layout_behavior="@string/bottom_sheet_behavior">
        <!-- 생략 -->
    </RelativeLayout>

</android.support.design.widget.CoordinatorLayout>
```

높이가 고정이고, 숨겨지지 않는 경우는 다음과 같이 정의할 수 있는데

- `app:behavior_hideable="false"`
- `app:behavior_peekHeight="120dp"`

위와 같이 `behavior_hideable="false"`이면 숨겨지지 않고, 항상 노출됩니다.

![bottom_sheet_04]


<br />

### BottomSheetBehavior API 사용하기

레이아웃을 위와 같이 정의하였으면, 이젠 `BottomSheetBehavior`를 연결하고, 이에 대한 콜백을 받을 수 있습니다.

먼저 정의한 레이아웃을 `BottomSheetBehavior`에 연결합니다.

```java
BottomSheetBehavior bottomSheetBehavior = BottomSheetBehavior.from(rlBottomSheet);
```

<br />

#### setBottomSheetCallback 처리

<a href="https://developer.android.com/reference/android/support/design/widget/BottomSheetBehavior.html#setBottomSheetCallback(android.support.design.widget.BottomSheetBehavior.BottomSheetCallback)">setBottomSheetCallback</a>을 정의하면 [BottomSheetCallback](https://developer.android.com/reference/android/support/design/widget/BottomSheetBehavior.BottomSheetCallback.html)을 받을 수 있습니다.

- `onStateChanged` : BottomSheet의 State 값을 받을 수 있습니다.
- `onSlide` : BottomSheet의 `offset` 정보를 받을 수 있습니다. `1 ~ -1`까지

```java
bottomSheetBehavior.setBottomSheetCallback(new BottomSheetBehavior.BottomSheetCallback() {
      @Override
      public void onStateChanged(@NonNull View bottomSheet, int newState) {

      }

      @Override
      public void onSlide(@NonNull View bottomSheet, float slideOffset) {

      }
  });
```

위의 state을 정보를 받아서 BottomSheet의 상태에 따라서 다른 레이아웃을 컨트롤할 수 있습니다.


<br />

#### setState 변경

BottomSheet의 State을 변경할 수 있습니다.

State는 다음과 같습니다.

- `STATE_COLLAPSED` : height 만큼 보이게 됩니다.
- `STATE_EXPANDED` : 가득 차게 처리합니다.
- `STATE_HIDDEN` : 숨김 처리됩니다.

그 외에 State는 다음과 같습니다.

- `PEEK_HEIGHT_AUTO`
- `STATE_DRAGGING`
- `STATE_SETTLING`

다음의 명령을 통해서 처리합니다.

```java
bottomSheetBehavior.setState(STATE);
```

setState에서 `EXPANDED`을 정의하면 다음과 같습니다.

![bottom_sheet_03]

<br />

#### setPeekHeight 변경

코드상으로도 높이를 변경할 수 있는데 다음과 같이 할 수 있습니다.

```java
bottomSheetBehavior.setPeekHeight((int) TypedValue.applyDimension(
                TypedValue.COMPLEX_UNIT_DIP, 300.f, getResources().getDisplayMetrics()));
```


<br />

## FloatingActionButton을 함께 사용하려면

FloatingActionButton을 BottomSheet 위에 올리고, 따라다니게 할 수 있습니다.

- `app:layout_anchor` : BottomSheet의 레이아웃을 지정하면 BottomSheet와 함께 움직이게 됩니다.
- `app:layout_anchorGravity` : BottomSheet와 함께할 위치를 설정할 수 있습니다. 아래 샘플은 `left`에 포함되도록 하기 위해서 오른쪽에 적용한 상태입니다.

```xml
<android.support.design.widget.FloatingActionButton
        android:id="@+id/fab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_margin="@dimen/fab_margin"
        app:layout_anchor="@id/rl_bottom_sheet"
        app:layout_anchorGravity="left"
        app:srcCompat="@android:drawable/ic_dialog_email" />
```


<br />

## 마무리

BottomSheet에 대해서 간단하게 정리하였습니다.

샘플 코드는 [BottomSheet - GitHub 블로그 예제](https://github.com/taehwandev/Android-BlogExample/tree/01-BottomSheetExample)에서 확인이 가능합니다.


[bottom_sheet_01]: /images/2016/2016-12-11-Android-BottomSheet-Intro/bottom_sheet_01.png
[bottom_sheet_02]: /images/2016/2016-12-11-Android-BottomSheet-Intro/bottom_sheet_02.png
[bottom_sheet_03]: /images/2016/2016-12-11-Android-BottomSheet-Intro/bottom_sheet_03.png
[bottom_sheet_04]: /images/2016/2016-12-11-Android-BottomSheet-Intro/bottom_sheet_04.png
