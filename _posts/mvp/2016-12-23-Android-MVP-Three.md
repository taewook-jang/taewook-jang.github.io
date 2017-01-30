---
layout: post
title: Android MVP 무작정 따라하기 - Presenter 분리하기(동영상)
categories: [AndroidDev]
tags: [Android, AndroidDev, MVP]
fullview: false
comments: true
published: true
---

Android MVP 무작정 따라하기 4번째 입니다.

이전 시간에 Presenter를 총 3가지 방법으로 분리하는 방법에 대해서 정리하였습니다.

- Google Architecture에서 정의하는 Contract 정의하는 방법
	- [링크](http://thdev.tech/androiddev/2016/11/28/Android-MVP-One.html)
- Presenter/PresenterImpl로 구분하는 방법
	- [링크](http://thdev.tech/androiddev/2016/11/30/Android-MVP-Two.html)
- View에 대한 interface만 정의하는 방법
	- [링크](http://thdev.tech/androiddev/2016/11/30/Android-MVP-Two.html)

선호에 따라서 사용하는 방법은 서로다를 수 있습니다.

저는 구글 Architecture에서 정의하고 있는 Contract를 정의하는 방법을 택하여 사용하고 있습니다.

그래서 오늘은 무작정 따라하기 4번째로 MVC로 작성한 예제 코드를 MVP의 Presenter 분리하는 방법을 `영상`으로 만들어보았습니다.

**샘플코드**

- [GitHub 02-MVP-Intro](https://github.com/taehwandev/AndroidMVPSample/tree/02-MVP-Intro)


<br />

## 영상을 보기 전에

MVC의 예제에서 다음의 코드들을 Presenter로 분리하였습니다.

MainActivity의 코드 중 `setImageItems`의 모델 사용하는 부분을 Presenter로 분리

```java
// Adapter를 생성
imageAdapter = new ImageAdapter(this);
// Adapter에 itemList를 data를 통해서 불러와서 저장
imageAdapter.setImageItems(SampleImageData.getInstance().getImages(this, 10));
// RecyclerView에 adapter를 세팅
recyclerView.setAdapter(imageAdapter);
```

MainActivity의 오른쪽 상단 버튼인 reload 발생할 경우의 코드에 대해서 `Presenter`로 분리

```java
// reload 액션이 발생
if (id == R.id.action_reload) {
    // 기존 itemList clear
    imageAdapter.clear();
    // 새로운 ImageList 불러와서 교체
    imageAdapter.setImageItems(SampleImageData.getInstance().getImages(this, 10));
    // UI Change
    imageAdapter.notifyDataSetChanged();
    return true;
}
```


<br />

## 영상

<iframe width="640" height="360" src="https://www.youtube.com/embed/rFLykveIFpg?rel=0" frameborder="0" allowfullscreen></iframe>


<br />

## Java 주요 코드

### 주요코드 - MainContract 정의.java

`View`와 `Presenter`에 대한 `interface`를 정의합니다.

```java
public interface MainContract {

    interface View {

        void addItems(ArrayList<ImageItem> items, boolean isClear);

        void notifyAdapter();
    }

    interface Presenter {

        void attachView(View view);

        void detachView();

        void setSampleImageData(SampleImageData sampleImageData);

        void loadItems(Context context, boolean isClear);
    }
}
```

### 주요코드 - MainPresenter.java

MainPresenter를 다음과 같이 정의합니다.

`View`와 `SampleImageData`(모델)을 다음과 같이 셋팅합니다.

```java
private MainContract.View view;

private SampleImageData sampleImageData;

@Override
public void attachView(MainContract.View view) {
    this.view = view;
}

@Override
public void setSampleImageData(SampleImageData sampleImageData) {
    this.sampleImageData = sampleImageData;
}
```

그리고 loadItems을 다음과 같이 정의하였습니다.

```java
@Override
public void loadItems(Context context, boolean isClear) {
		ArrayList<ImageItem> items = sampleImageData.getImages(context, 10);
		view.addItems(items, isClear);
		view.notifyAdapter();
}
```

### 주요코드 - MainActivity.java

presenter을 생성하고, loadItems를 호출하게 됩니다.

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    // 생략

    mainPresenter = new MainPresenter();
    mainPresenter.attachView(this);
    mainPresenter.setSampleImageData(SampleImageData.getInstance());

		// 생략
    mainPresenter.loadItems(this, false);
}
```

UI 갱신을 위한 `notifyAdapter`와 `addItems`을 아래와 같이 작성합니다.

```java
@Override
public void addItems(ArrayList<ImageItem> items, boolean isClear) {
	 if (isClear) {
			 imageAdapter.clear();
	 }
	 imageAdapter.setImageItems(items);
}

@Override
public void notifyAdapter() {
	 imageAdapter.notifyDataSetChanged();
}
```


<br />

## Kotlin의 주요 코드

### 주요코드 - MainContract 정의.kt

kotlin에서 사용가능한 interface 정의를 다음과 같이 합니다.

`var view`와 `var ImageData`를 정의합니다.

```kotlin
interface MainContract {
    interface View {

        fun updateItems(items: ArrayList<ImageItem>, isClear: Boolean)

        fun notifyAdapter()
    }

    interface Presenter {
        var view: View
        var imageData: ImageData

        fun loadItems(context: Context, isClear: Boolean)
    }
}
```

### 주요코드 - MainPresenter 정의.kt

그리고 `lateinit`을 통해서 변수를 선언합니다.

java에서는 이때 `setView`/`getView`가 자동으로 생성됩니다.

```kotlin
lateinit override var view: MainContract.View
lateinit override var imageData: ImageData
```

그리고 `loadItems`을 아래와 같이 생성합니다.

updateItems과 notifyAdapter을 각각 호출해주어 UI를 갱신합니다.

```kotlin
override fun loadItems(context: Context, isClear: Boolean) {
    imageData.getSampleList(context, 10).let {
        view.updateItems(it, isClear)
        view.notifyAdapter()
    }
}
```

### 주요코드 - MainActivity 정의.kt

```kotlin
private lateinit var presenter: MainPresenter

override fun onCreate(savedInstanceState: Bundle?) {
    // 생략

    presenter = MainPresenter().apply {
        view = this@MainActivity
        imageData = ImageData
    }

    // 생략

    presenter.loadItems(this, false)
}
```

그리고 `presenter`로 부터 View에 대한 콜백을 다음과 같이 처리합니다.

```kotlin
override fun updateItems(items: ArrayList<ImageItem>, isClear: Boolean) {
    imageAdapter?.apply {
        if (isClear) {
            imageList?.clear()
        }
        imageList = items
    }
}

override fun notifyAdapter() {
    imageAdapter?.notifyDataSetChanged()
}
```


<br />

## 마무리

MVP로 한발 다가설 수 있도록 무작정 따라하기를 연재해나가고 있습니다.

하지만 아직은 어렵습니다. 익숙해지기 전까진 힘들죠.

MVC가 보기에는 편합니다.

오늘 내용으로 보시면 아시겠지만, 몇 줄 적지 않았던 코드는 길게 늘어났고, 클래스도 많아졌습니다.

아직은 MVP라고 하기엔 거리가 멀어 보이기도 합니다.

단순히 생각해보면

Activity에서 Model을 분리했다..?라는데 아니라고 보일지도 모릅니다.

어렵지만 익숙해지면 코드 분리가 확실하게 됩니다.

최소한 `View/Model` 간의 결합도를 낮출 수 있고, `View/Model`에 대한 테스트도 가능합니다.

그래서 다음 글에서는 Adapter에 대한 View와 Model을 한번 분리해보도록 하겠습니다.

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
