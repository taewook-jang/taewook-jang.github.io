---
layout: post
title: Android MVP 무작정 따라하기 - Model 정의하기
categories: [AndroidDev]
tags: [Android, AndroidDev, MVP]
fullview: false
comments: true
published: true
---

안드로이드 MVP 무작정 따라하기 8 번째입니다.

오늘은 MVP 패턴에서 사용할 수 있는 다양한 Model을 살펴보려고 합니다.


<br />

## Model 이란?

Model은 Data를 말합니다.

단순 데이터가 아닌 데이터를 관리/수집/수정 등을 하게 되는 부분입니다.

데이터를 Cache해야 한다면 이 모델에서 Cache 할 수도 있고, 그냥 단순 데이터 전달만 할 수도 있습니다.



<br />

## Google Architecture의 Model

제가 작성한 모델을 설명하기 전에 Google Architecture을 간단하게 설명하겠습니다.

구글 Architecture는 아래와 같은 그림으로 설명할 수 있습니다.

![mvp_model_01]

위에서 사용한 예제는 [Android Architecture - TODO MVP](https://github.com/googlesamples/android-architecture/tree/todo-mvp/)입니다.

각각의 역할은 다음과 같습니다.

- Repository : Remote/Local을 구분하며, Memory cache를 포함
- Remote : 서버를 통한 데이터를 불러온다
- Local : 단말기 상의 SQL, Realm 등을 통한 데이터를 불러온다

좀 더 자세한 내용은 다음에 작성하는 글에서 추가로 정리하겠습니다.


<br />

## 구현한 모델은?

- [AndroidMVPSample](https://github.com/taehwandev/AndroidMVPSample)

현재까지 MVP에서 정의한 모델은 단순 리턴을 하며, Model만 구분을 해두었습니다.

싱글톤을 통해서 데이터 클래스에 접근하도록 만들었습니다.

데이터 형태에 따라서 여러 개가 만들어질 수도, 단일 클래스로 생성될 수도 있습니다.

```java
public class SampleImageData {
    private SampleImageData() {}
    private static SampleImageData sampleImageData;

    public static SampleImageData getInstance() {
        if (sampleImageData == null) {
            sampleImageData = new SampleImageData();
        }
        return sampleImageData;
    }

    public ArrayList<ImageItem> getImages(Context context, int size) {
        ArrayList<ImageItem> items = new ArrayList<>();
        // 생략
        return items;
    }
}
```

저는 위와 같이 하나의 클래스에 싱글톤으로 만들었습니다.

가장 기본 형태의 모델이라고 생각됩니다.


<br />

## Presenter에서는

- [AndroidMVPSample](https://github.com/taehwandev/AndroidMVPSample)

위에서 작성한 모델을 Presenter에서 셋팅하는 주체는 View입니다.

Presenter 초기화하는 대상이 View이기 때문에 View 초기화함과 동시에 Model을 초기화하게 됩니다.

```kotlin
presenter = MainPresenter().apply {
		view = this@MainActivity
		imageData = ImageData
		adapterModel = imageAdapter
		adapterView = imageAdapter
}
```

위와 같이 초기화를 하고, Presenter에서 다음과 같이 사용하게 됩니다.

getItems를 통해서 데이터를 불러오고 이를 사용하게 됩니다.

```java
@Override
public void loadItems(Context context, boolean isClear) {
    ArrayList<ImageItem> items = sampleImageData.getImages(context, 10);
    if (isClear) {
        adapterModel.clearItem();
    }
    adapterModel.addItems(items);
    adapterView.notifyAdapter();
}
```


<br />

## 마무리

간단하게 Android MVP의 모델을 살펴보았습니다.

가장 기본적인 샘플이라서 별도의 추가 내용은 없습니다.

다음 글에서는 MVP의 구글 Architecture 방식을 추가로 살펴보고, 구현까지 해보도록 하겠습니다.


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


[mvp_model_01]: /images/2016/2016-12-29-Android-MVP-Model-One/mvp_model_01.png
