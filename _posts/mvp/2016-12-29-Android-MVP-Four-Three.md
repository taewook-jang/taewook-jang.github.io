---
layout: post
title: Android MVP 무작정 따라하기 - Adapter OnClick 정의하기(동영상)
categories: [AndroidDev]
tags: [Android, AndroidDev, MVP]
fullview: false
comments: true
published: true
---

안드로이드 MVP 무작정 따라하기 7 번째 입니다.

지난 동영상에서 `AdapterContract` 정의를 하였고, 이어서 OnClickListener 정의를 해보려고 합니다.


<br />

## AdapterContract View - OnClickListener 정의

OnClickListener 정의는 AdapterContract View에 추가 정의를 통해 간단하게 구현할 수 있습니다.

기존 AdapterContract.View에는 다음과 같이 정의하였습니다.

```java
interface ImageAdapterContract {

    interface View {

        void notifyAdapter();
    }
}
```

여기에 `setOnClickListener(OnClickListener listener)` 정의를 추가함으로써 Presenter에서 바로 Adapter의 OnClickListener 이벤트를 전달받고, 이를 처리할 수 있게 됩니다.

이유는 간단합니다.

- AdapterModel/AdapterView를 Presenter에서 들고 있기 때문에 굳이, View에서 이런 이벤트를 받을 필요는 없습니다.

역시나 귀찮은 부분이 따르므로 아래의 그림과 같이 추가될 수 있습니다.

**기존 그림에서 setOnClickListener 부분을 추가하였습니다.**

![mvp_01]


<br />

## AdapterContract View를 통해서 초기화하는 이유는?

AdapterContract.View에서 OnClick을 초기화하고, 이를 ViewHolder에서 정의하는 이유는 다음과 같습니다.

- 이미 Presenter에서는 Adapter View/Model을 알고 있다.
- 실제 View/Model을 한 번에 가지고 있는 Adapter이기 때문에 이를 굳이 View에서 처리할 필요는 없다

위와 같은 이유입니다.

결국 다시 View에서 setOnClickListener을 하고, 이 이벤트를 받아서 Presenter에 넘겨서 처리를 하는 것보단, 바로 Presenter가 받아서 이를 처리하고, View 이벤트를 분리하는게 편리하다고 생각했기에 오늘의 방법을 정리해보았습니다.


<br />

## 라이브 코딩

라이브 코딩은 언제나 그렇듯이 Java/Kotlin으로 담겨있습니다.

<iframe width="640" height="360" src="https://www.youtube.com/embed/Gev3IUMMOwk?rel=0" frameborder="0" allowfullscreen></iframe>


<br />

## 마무리

지금까지 Presenter를 분리하고, Presenter와 Adapter가 함께 있을 때 접근 방법을 살펴보았습니다.

다음 글에서는 가장 기본적인 Model을 정의해보고, Google Architecture에서 정의하고 있는 Model인 Repository(ios 개발에서는 Viper와 유사) 정의하는 방법 등을 다루어 보도록 하겠습니다.


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


[mvp_01]: /images/2016/2016-12-28-Android-MVP-Four-Three/mvp_01.png
