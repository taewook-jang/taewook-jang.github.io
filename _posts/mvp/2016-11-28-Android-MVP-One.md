---
layout: post
title: Android MVP 무작정 따라하기 - Presenter/View 생성하기
categories: [AndroidDev]
tags: [Android, AndroidDev, MVP]
fullview: false
comments: true
published: true
---

안드로이드 MVP 무작정 따라하기 1 번째로 Presenter와 View를 나누는 방법에 대해서 정리합니다.


<br />

## 샘플 코드는?

- [Android MVP Sample](https://github.com/taehwandev/AndroidMVPSample)


<br />

## Presenter 정의는?

- `View` : Presenter에서 전달받은 View의 이벤트입니다.
- `Presenter` : View에서 전달된 이벤트에 대한 처리를 한다(View와 무관한 처리만 한다)

그림으로 보면 아래와 같습니다.

![mvp-default]


<br />

## Presenter을 구분하는 방법들

제가 알고 있는 방법은 3가지 정도가 있습니다. 그 안에 구현하는 방식 역시 여러 방법이 있습니다.

모두 다를 순 없고 크게 2가지 정도를 다루어 보려고 합니다.

이번 글에서는 Google Architecture을 따르고, 다음 2번째 글에서는 PresenterImpl을 추가하는 방법을 추가로 다루어보려고 합니다.

- View에 대한 interface만 정의하는 방법
	- interface View : View에 대한 interface만 정의
	- Presenter : interface 정의 없이 함수를 생성하여 사용
	- View : interface View을 상속받아서 정의
- [Google architecture](https://github.com/googlesamples/android-architecture)를 따른다.
	- Contract : View와 Presenter에 대한 interface을 작성
	- Presenter : Contract.Presenter을 상속받아서 구현
	- View : Contract.View을 상속받아서 구현
- PresenterImpl을 구현
	- Presenter : Presenter와 View에 대한 interface을 구현
	- PresenterImpl : Presenter을 상속받아서 구현
	- View : Presenter.View을 상속받아서 구현

구현하는 방법은 크게 3가지 정도로 나뉠 수 있습니다.

정답은 없고, 편한 방법과 다른 사람과 공유했을 때의 장/단점을 모두 따져서 작성하시면 좋겠습니다.

interface 정의시의 장법은 처음 보는 사람이 파악이 쉽다이고, 단점은 역시나 interface 정의가 너무 많다가 될 수 있습니다.

결국 `View`와 `Presenter`간의 통신을 위한 리스너 역할의 `interface View`에 대한 정의는 처리가 되어야 합니다.


<br />

## Google Architecture을 따르면...

`MVP` 따라 하기를 진행하기 전 조금은 어려운 내용을 먼저 정리해보았습니다.

우선 저의 예제에서는 `Google Architecture`에서 정의하는 `View/Presenter`을 100% 따르지는 않고, `interface 정의(Contract 정의)`하는 방법만을 따르고 있습니다.

구글에서 정의하는 `Presenter`의 생성 방법은 다음과 같습니다.

![google_architecture]

- `Presenter`의 생성은 `View`가 아닌 실제 `View`가 만들어지는 시점의  `Activity/Fragment/View`등에서 생성을 하고 해당 `Presenter`에 `setView`을 실행한다.
- `setView`가 호출되는 시점에 `자기 자신(this)`을 `setPresenter`함수를 통해서 실제 `Presenter`가 사용되어야 할 `View`에 전달한다.
- `View`에서는 `setPresenter`을 통해서 전달받은 `Presenter`을 가지고 이후 `loadItem`, `OnClickListener` 등의 처리를 합니다.


### 여기서 의문점

- `Activity`만 있는 경우는?
	- `Activity`만 있는 경우인데 별도의 `View`를 생성해야 하느냐?
	- 그냥 자기 자신이 받아도 되는가?


### 의문에 대한 답은?

- 구글이 제안하는 방법대로라면
 	- 별도의 `View`가 있다고 생각하시는게 좋겠죠? 코드의 통일성을 위해서
 	- 자기 자신이 받는 것은 없다고 보시면 됩니다.


### 제가 제안하는 방법은?

위의 의문점에 대해서 저는 다음과 같이 생각합니다.
- `Activity`만 있는 경우에도 별도의 `view`가 아닌 그냥 자기 자신이 가지도록 하면 됩니다.
	- 예제 중에서는 별도의 `View`을 생성하는 경우도 있습니다.
- `Activity` 저는 자기 자신이 받아도 된다고 생각합니다.

**다시 한번 정리하면**

- 결국 자기 자신이 `new Presenter`을 처리할 수 있어야 합니다.
	- 그럼 `setPresenter`라는 메소드가 필요하지 않게 됩니다.
- `Activity/Fragment/View`등에서 필요한 경우 `Presenter`을 생성하고, 자기 자신이 사용할 수 있어야 합니다.


<br />

## 다음의 샘플에서는?

위와 같은 의문점과 제가 제안하는 방법을 도식화하면 다음과 같습니다.

![my_architecture]

- `Activity/Fragment/View`에서 필요한 `Presenter`을 직접 생성
- `setView`을 전달한다
- `loadItem`을 직접 호출
- `View`을 통해서 처리 결과에 따른 `View`을 갱신한다


<br />

## Contract 구현하기

이번에는 구글의 `Contract 정의`를 따른 예정이므로 Contract을 구현합니다.

`Contract`을 사용하는 이유는 간단합니다. `View`와 `Presenter`을 각각 정의하기 위함이며, 이해를 돕기 위한 이유도 있습니다.

하나의 `interface`에 `View/Presenter`을 정의하고, 이를 각각의 `View`와 `Presenter`에서 정의하는 방식입니다.

먼저 `View`와 `Presenter`을 각각 다음과 같이 구현할 수 있습니다.

```java
public interface SampleContract {
	interface View {
		 // View method
	}

	interface Presenter {
		// Presenter method
		void setView(View view);
	}
}
```


<br />

## Presenter 상속 정의

Presenter는 다음과 같은 방식으로 구현합니다.

`SampleContract.Presenter`을 상속받아서 구현하며, `SampleContract.View`을 가지게 됩니다.

```kotlin
class SamplePresenter : SampleContract.Presenter {
	// SampleContract.Presenter에서 정의한 내용을 구현

	private var view: SampleContract.View? = null

	override fun setView(view: SampleContract.View) {
		this.view = view
	}
}
```

위와 같으며 `View`에서 데이터가 필요하다면 다음의 과정을 거치게 됩니다.

- `View` : `loadItem`을 호출합니다.
- `Presenter` : `loadItem`이 발생하면 새로운 데이터를 호출합니다.
- `Presenter -> View` : `loadItem`이 성공적으로 완료되면 `updateView`을 호출합니다.
	- 이때 `SampleContract.View`의 정의되어 있는 `updateView`을 호출합니다.
- `View` : 전달된 `updateView`에 따라서 실제 View를 갱신합니다.


<br />

## View 상속 정의

`SampleContract.View`를 상속받으면 다음과 같이 정의해주시면 되겠습니다.

```java
class SampleActivity extends AppCompatActivity implements SampleContract.View {

	private SampleContract.Presenter presenter;

	@Override
  protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

		presenter = new SamplePresenter();
		presenter.setView(this);

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

오늘은 MVP를 나누는 방법에 대해서 정리하였습니다.

View/Presenter의 정의 방법이 여러 개가 있다보니 먼저 Google Architecture에서 정의하였으면 하는 방법을 정리해보았습니다.

다음 글에서는 `Presenter/PresenterImpl`을 정의하는 방법을 살펴본 다음 기존 MVC 패턴으로 작성한 예제 코드를 `View/Presenter`까지 우선 나누어 보도록 하겠습니다.

저는 오늘의 방법을 선호하고, 실제로 모든 예제를 다음의 방법으로 정리하여 사용 중에 있습니다.

가장 기본적이지만 어려운 내용을 먼저 정리해보았습니다.

감사합니다.


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


[mvp-default]: /images/2016/2016-10-12-Android-MVP-Intro/mvp-default.png
[google_architecture]: /images/2016/2016-11-28-Android-MVP-One/Google_Architecture.png
[my_architecture]: /images/2016/2016-11-28-Android-MVP-One/My_Architecture.png
