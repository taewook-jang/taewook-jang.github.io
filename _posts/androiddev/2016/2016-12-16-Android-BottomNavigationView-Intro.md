---
layout: post
title: Android BottomNavigationView 사용하기
categories: [AndroidDev]
tags: [Android, Android Design]
fullview: false
comments: true
published: true
---

안드로이드 Support Library 25 버전부터 BottomNavigationView을 사용할 수 있게 되었습니다.

BottomNavigationView는 다음과 같이 하단에 포함되는 View를 말합니다.

![bottom_sample_01]

- 해당 예제 코드는 [GitHub의 블로그 예제](https://github.com/taehwandev/Android-BlogExample/tree/02-BottomNavigationExample)에 포함되어 있으며, `Kotlin`과 `Java`로 구성되어 있으며, MVP 구조로 작성하였습니다.


<br />

## BottomNavigationView API 문서

- [Material design - BottomNavigation](https://material.google.com/components/bottom-navigation.html)
- [BottomNavigationView API](https://developer.android.com/reference/android/support/design/widget/BottomNavigationView.html)
- [Medium: Joe Birch - Exploring the Android Design Support Library: Bottom Navigation View](https://medium.com/@hitherejoe/exploring-the-android-design-support-library-bottom-navigation-drawer-548de699e8e0#.5fmqss50n)
- [GitHub: 작성한 샘플 자료 - Blog Sample](https://github.com/taehwandev/Android-BlogExample/tree/02-BottomNavigationExample)
	- 제가 작성한 샘플 예제를 포함합니다.
- [GitHub: roughike](https://github.com/roughike/BottomBar)
	- BottomNavigationView의 예제들입니다.
- [Medium: Nikola Despotoski - Bottom Navigation Behavior](https://medium.com/@nullthemall/bottom-navigation-behavior-388b9b206667#.82r4hv9e7)
	- BottomNavigationView의 Behavior을 정리해둔 내용이며, 간단하게 적용이 가능합니다.


<br />

## BottomNavigationView 적용하기

`BottomNavigationView`는 크게 2가지 `View` 정의가 필요합니다.

- BottomNavigationView에서 사용할 `menu.xml`을 정의합니다.
- BottomNavigationView을 사용할 `xml`에 layout resource에 `BottomNavigationView`을 정의합니다.

gradle에 다음과 같이 `support design` 라이브러리를 추가합니다.

버전은 25.0.0 이상을 사용하여야 합니다.

```
compile "com.android.support:design:25.0.0"
```


<br />

## menu.xml 정의하기

BottomNavigationView에서 사용할 메뉴를 정의합니다.

이때 주의할 부분이 있습니다. `item`을 최소 3개 이상 추가하셔야 합니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
    <item
        android:id="@+id/action_one"
        app:showAsAction="ifRoom"
        android:enabled="true"
        android:icon="@android:drawable/ic_dialog_info"
        android:title="@string/selection_home" />
    <item
        android:id="@+id/action_two"
        app:showAsAction="ifRoom"
        android:icon="@android:drawable/ic_dialog_info"
        android:title="@string/selection_daily_box_office" />
    <item
        android:id="@+id/action_three"
        app:showAsAction="ifRoom"
        android:enabled="true"
        android:icon="@android:drawable/ic_dialog_info"
        android:title="@string/selection_weekly_box_office" />
</menu>
```

2개만 추가한다면 다음과 같은 화면을 볼 수 있게 됩니다.

- 가이드상 3개 이상에서 사용하여야 합니다.

![bottom_sample_02]


<br />

## layout resource에 정의하기

BottomNavigationView을 아래와 같이 적용이 가능합니다.

위에서 정의한 menu는 다음과 같이 추가할 수 있습니다.

- `app:menu="@menu/xml 파일명"`

item의 background 정의를 다음과 같이 할 수 있습니다.

- `app:itemBackground="color code"`

```xml
<android.support.design.widget.CoordinatorLayout>
	<!-- 생략 -->
	<android.support.v4.view.ViewPager
        android:id="@+id/container"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />

<android.support.design.widget.BottomNavigationView
        android:id="@+id/bottom_navigation"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:itemBackground="@color/colorPrimary"
        app:itemIconTint="@color/bottom_section_color"
        app:itemTextColor="@color/bottom_section_color"
        app:layout_behavior="tech.thdev.app.view.BottomNavigationBehavior"
        app:menu="@menu/bottom_navigation_main" />
			</android.support.design.widget.CoordinatorLayout>
```

각각 이미지와 텍스트에 대한 색상 값을 정의할 수 있습니다.

- `app:itemIconTint="color code"`
- `app:itemTextColor="color code"`

이때 아래와 같이 표현되도록 하려면 color 코드를 별도로 정의해주셔야 합니다.

color을 별도로 다음과 같이 정의한 상태라서 아래와 같이 표현됩니다.

- `state_checked`가 선택된 상태를 말합니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_checked="true" android:color="#51032d" />
    <item android:color="#CFD8DC"  />
</selector>
```

위와 같이 정의한 파일을 `itemIconTint`와 `itemTextColor`에서 사용하게 됩니다.

![bottom_sample_01]


<br />

## menu item click 정의하기

menu로 정의하긴 했지만 실제 메뉴가 아닌 `BottomNavigationView`을 통해서 정의한 부분입니다.

그래서 다음과 같이 Java 코드에서 item에 대한 `click`을 정의해주어야 합니다.

`item`의 `getItemId`을 불러와서 action 정의를 처리하시면 되겠습니다.

```java
bottomNavigationView.setOnNavigationItemSelectedListener(new BottomNavigationView.OnNavigationItemSelectedListener() {
            @Override
            public boolean onNavigationItemSelected(@NonNull MenuItem item) {
                switch (item.getItemId()) {
                    case R.id.action_one:
                        return true;
                    case R.id.action_two:
                        return true;
                    case R.id.action_three:
                        return true;
                }
                return false;
            }
        });
```

<br />

## BottomNavigationView을 적용하는데 있어 피해야 할 스타일

구글의 문서 정의에서 볼 수 있는 부분으로 아래와 같이 아이템의 색상을 단색이 아닌 여러 색을 처리하지 않는 것을 권장하고 있습니다.

![components_bottomnavigation_style8]

그 외에

- 1줄을 넘어가는 텍스트 사용하지 말 것
- 바텀 메뉴 누를 경우 ViewPager 상에 swipe 에니메이션 처리하지 말 것
- 스크롤이 발생할 경우 바텀 메뉴는 숨김 처리하며 다시 위로 올라오면 노출할 것
- 가이드상 3개 이상에서 사용하여야 합니다.
- 4~5개일 경우는 이미지로만 노출할 것

등의 조건이 달립니다.


<br />

## Bottom Behavior 정의하기

위의 피해야 할 스타일 중 마지막인

- 스크롤이 발생할 경우 바텀 메뉴는 숨김 처리하며 다시 위로 올라오면 노출할 것

위의 조건은 다음의 에니메이션 처리와 같아야 합니다.

![Behavior]

이를 위해서 다음의 글을 참고하여 해당 Behavior 정의를 그대로 사용하였습니다.

- [Medium: Nikola Despotoski - Bottom Navigation Behavior](https://medium.com/@nullthemall/bottom-navigation-behavior-388b9b206667#.82r4hv9e7)

그래서 다음과 같이 `Behavior` 정의만 하면 되겠습니다.

```xml
<android.support.design.widget.BottomNavigationView
		app:layout_behavior="tech.thdev.app.view.BottomNavigationBehavior" />
```


<br />

## Swipe 발생 시 하단의 버튼 선택하기

가이드랑은 맞지 않지만 Swipe 발생할 경우 다음과 같이 작업이 가능합니다.

BottomNavigation의 API를 보면 딱 Swipe에 대한 대응 같은 건 존재하지 않고, `current Item` 선택하는 것도 다음과 같이 처리가 가능합니다.

내부의 `getMenu`를 불러오고 해당 `position`의 아이템을 가져와 `setChecked` 상태를 변경하는 방법입니다.

```java
/**
 * BottomNavigation Prev
 */
private MenuItem prevBottomNavigation;

@Override
public void onPageSelected(int position) {
		if (prevBottomNavigation != null) {
				prevBottomNavigation.setChecked(false);
		}
		prevBottomNavigation = bottomNavigationView.getMenu().getItem(position);
		prevBottomNavigation.setChecked(true);
}
```


<br />

## 완성된 샘플

완성된 샘플은 아래와 같습니다.

ViewPager를 활용해서 만들었고, 원래 가이드대로의 ViePager 이동에 대한 부분은 처리하지 않았습니다.

가이드상으로는 ViewPager 간의 이동이 아닌 페이지 단위로 이동이 일어나야겠죠. + 에니메이션

![bottomNavigation]

위의 샘플은 `Target API 25`로 작성하였고, `Support Library 25`가 추가된 상태입니다.

- [Open API 서비스 - 영화관 입장관리 통합전산망](http://www.kobis.or.kr/kobisopenapi/homepg/main/main.do)의 일별 박스 오피스와 주간 박스오피스 2가지 API를 사용하였습니다.

해당 사이트에 접근하셔서 API 키를 발급받아

`local.properties`에 다음과 같이 코드를 추가해주시면 되겠습니다.

```
kobis_api_key="발급받은 API Key"
```


<br />

## 마무리

BottomNavigationView가 구글의 옛날 가이드 대로라면 좋은 메뉴가 아닙니다.

- SW 내비게이션 버튼이 존재하고, 이와 겹칠 수 있어서 기존에는 사용하지 말라고 가이드 한 적이 있습니다.

하지만... Material 디자인이 나오고, 많은 변화가 있는 듯합니다.

실제로 적용해보니 적용하는 부분은 어렵지 않은데 커스텀을 통해서 만드는 것과 차이가 크지 않긴 합니다.

제한이 많기도 합니다. 위에 설명 중 2개만 추가했을 때 공백이 발생할 수 있음을 말하였듯이 맞지 않는 부분도 존재합니다.

그리고 `CoordinatorLayout`을 사용한 상태로 `BottomNavigationView`을 추가해주면  `layout_anchor`을 추가해주지 않으면 하단에 붙지 않습니다.

`FloatingActionButton`을 함께 사용하기도 까다롭긴 합니다.

좀 더 쉽게 적용할 수 있게 나왔으면 합니다.

추가로 Google에서 작성한 `BottomNavigationView`의 내부를 열어보면 `Presenter`를 포함하고 있음을 보아 MVP 구조로 작업되어 있으니, 살펴보시면 좋을 것 같습니다.



- [GitHub: Android BottomNavigation-Sample](https://github.com/taehwandev/Android-BlogExample/tree/BottomNavigation-Sample)


[bottom_sample_01]: /images/2016/2016-12-16-Android-BottomNavigationView-Intro/bottom_sample_01.png
[bottom_sample_02]: /images/2016/2016-12-16-Android-BottomNavigationView-Intro/bottom_sample_02.png

[components_bottomnavigation_style8]: /images/2016/2016-12-16-Android-BottomNavigationView-Intro/components_bottomnavigation_style8.png

[Behavior]: /images/2016/2016-12-16-Android-BottomNavigationView-Intro/Behavior.gif

[bottomNavigation]: /images/2016/2016-12-16-Android-BottomNavigationView-Intro/bottomNavigation.gif
