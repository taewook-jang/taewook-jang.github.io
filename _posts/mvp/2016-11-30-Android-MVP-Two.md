---
layout: post
title: Android MVP 무작정 따라하기 - Presenter/View 생성하기 Other
categories: [AndroidDev]
tags: [Android, AndroidDev, MVP]
fullview: false
comments: true
published: true
---

안드로이드 MVP 무작정 따라하기 2 번 째입니다.

지난 글에서 Android MVP 적용하는 방법 중 구글에서 추천하는 [Presenter/View](http://thdev.tech/androiddev/2016/11/28/Android-MVP-One.html)을 다루었었습니다.

오늘은 기존 글에서 추가로 소개하기로 하였던 Presenter/PresenterImpl을 구현하는 방법을 살펴보려고 합니다.


<br />

## 샘플 코드는?

- [Android MVP Sample](https://github.com/taehwandev/AndroidMVPSample)


<br />

## Presenter interface 구현하기 전에

Presenter interface을 구현하는 방법입니다.

기존 글에서는 구글에서 추천하는 방법인 `Contract`을 통해서 interface을 구현하는 방법을 살펴보았고, 구글의 MVP 패턴 적용하는 방법을 살펴보았습니다.

그리고 제가 사용하고 있는 패턴을 설명드렸습니다.

이번 글에서는 많이 사용되는 2 번째 방법을 소개합니다.

- Presenter : interface로 구현하며, View를 포함
- PresenterImpl : Presenter interface을 상속받아서 구현

이번 글에서도 기존과 같은 방식으로 접근하지만 interface 생성하는 방법을 달리하였습니다.

![my_architecture]


<br />

## Presenter interface 구현

구글은 `Contract`을 통해서 `View`/`Presenter`의 interface 2개를 작성하였지만, 다음의 방법은 `Presenter`에 `View interface`만을 작성하였습니다.

그래서 구현하면 다음과 같습니다.

```java
interface SamplePresenter {
	// Presenter 구현

	void loadItem();

	interface View {
		// 해당 Presenter에서 사용할 View 구현
		void updateView(List<Items> items);
	}
}
```


<br />

## PresenterImpl 구현

다음과 같이 implements를 통해서 상속받고, 이를 아래와 같이 구현합니다.

다만 이름은 implements의 구현체의 이름으로 `Impl`을 사용합니다.

**다음의 코드는 Kotlin으로 작성하였습니다.**

```kotlin
class SamplePresenterImpl(val view: SamplePresenter.View) : SamplePresenter {

	// 상속 받은 interface 구현
	override fun loadItem() {
		// ...
		val list = ArrayList<String>()
		view.updateView(list)
	}
}
```


<br />

## View 구현

SamplePresenter인 `SamplePresenterImpl`을 초기화하여 사용합니다.

사용법은 Contract을 통한 생성과 동일합니다.

```java
class SampleActivity extends AppCompatActivity implements SampleContract.View {

	private SamplePresenter presenter;

	@Override
  protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

		presenter = new SamplePresenterImpl(this);

		presenter.loadItem();
	}

	@Override
	public void updateView(List<Items> items) {
		// UI 갱신
	}
}
```


<br />

## Presenter interface가 없는 경우

추가로 `Presenter` interface을 사용하지 않는 경우입니다.

Presenter에 대한 interface을 사용하지 않기 때문에 `View`에 대한 interface만을 정의합니다.

```java
interface SampleView {
	void updateView(List<Items> items);
}
```

그리고 다음과 같이 Presenter을 구현합니다.

```kotlin
class SamplePresenter(val view: SampleView) {

	fun loadItem() {
		// ...
		val list = ArrayList<String>()
		view.updateView(list)
	}
}
```

그리고 다음과 같이 `View`의 사용이 가능하게 됩니다.

```java
class SampleActivity extends AppCompatActivity implements SampleContract.View {

	private SamplePresenter presenter;

	@Override
  protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

		presenter = new SamplePresenter(this);

		presenter.loadItem();
	}

	@Override
	public void updateView(List<Items> items) {
		// UI 갱신
	}
}
```


<br />

## 마무리

지난 글에 이어서 MVP를 작성하는 방법 3 가지를 정리해보았습니다.

`interface` 정의가 있는 경우와 없는 경우의 장/단점이 있으므로 적절하게 사용하시면 좋을 것 같습니다.

그 외에는 메소드 형태로 `attachView`을 적용하는지, 아니면 초기화 시에 바로 `View`를 넘겨주는지 등에 따른 방법이 서로 다를 수 있습니다.

저는 `attachView`를 통해서 넘겨주는 방식을 선호하고 있습니다.

이유는 View에 대한 `attachView`가 명확하다고 할 수 있겠네요. 다만 `null` 체크 등은 들어가야 합니다.

```java
if (view != null) {
	// ...
}
```

그래서 kotlin으로 한다면 다음과 같이 짧은 코드가 가능하죠.

```kotlin
view?.updateView()
```

다음 글에서는 MVC 패턴으로 작성한 예제 코드를 MVP로 변환하는 과정을 단계별로 담아보려고 합니다.


<br />

## 샘플 코드는?

- [Android MVP Sample](https://github.com/taehwandev/AndroidMVPSample)


<br />

## MVP 무작정 따라하기

무작정 따라 하기는 MVP 패턴을 이해할 수 있도록 하나씩 차근차근 작성해보는 예제입니다.

그래서 각각의 브런치와 글들을 통해서 조금씩 확장해가려고 합니다.

- [Android MVP 무작정 따라하기 - Intro](http://thdev.tech/androiddev/2016/10/12/Android-MVP-Intro.html)
- [Android MVP 무작정 따라하기 - MVC 구조 이해하기](http://thdev.tech/androiddev/2016/10/23/Android-MVC-Architecture.html)
- [Android MVP 무작정 따라하기 - Presenter/View 생성하기](http://thdev.tech/androiddev/2016/11/28/Android-MVP-One.html)
- [Android MVP 무작정 따라하기 - Presenter/View 생성하기 Other](http://thdev.tech/androiddev/2016/11/30/Android-MVP-Two.html)
- [Android MVP 무작정 따라하기 - Presenter 분리하기](http://thdev.tech/androiddev/2016/12/23/Android-MVP-Three.html)
- [Android MVP 무작정 따라하기 - Adapter Contract 정의하기](http://thdev.tech/androiddev/2016/12/26/Android-MVP-Four.html)
- [Android MVP 무작정 따라하기 - Adapter Contract 정의하기 2번째(동영상)](http://thdev.tech/androiddev/2016/12/27/Android-MVP-Four-Two.html)
- [Android MVP 무작정 따라하기 - Adapter OnClick 정의하기(동영상)](http://thdev.tech/androiddev/2016/12/29/Android-MVP-Four-Three.html)
- [Android MVP 무작정 따라하기 - Model 정의하기](http://thdev.tech/androiddev/2016/12/29/Android-MVP-Model-One.html)
- [Android MVP 무작정 따라하기 - Google Architecture의 Model](http://thdev.tech/androiddev/2017/01/09/Android-MVP-Model-Two.html)
- [Android MVP 무작정 따라하기 - Google Architecture Model(영상 포함)](http://thdev.tech/androiddev/2017/01/29/Android-MVP-Google-Architecture-Model.html)


[my_architecture]: /images/2016/2016-11-28-Android-MVP-One/My_Architecture.png
