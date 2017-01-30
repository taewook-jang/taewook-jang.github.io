---
layout: post
title: Android MVP 무작정 따라하기 - Google Architecture Model(영상 포함)
categories: [AndroidDev]
tags: [Android, AndroidDev, MVP]
fullview: false
comments: true
published: true
---

MVP 무작정 따라하기 10 번째 글입니다.

오늘은 Google Architecture 예제인 Model 구현하는 방법을 영상으로 만들어보았습니다.

- [가장 기본적인 Model 정의](https://github.com/googlesamples/android-architecture/tree/todo-mvp/)를 다루어보겠습니다.

이전에 작성한 단순한 모델 정의를 Repository 통해 구현합니다.


<br />

## Repository

Repository 정의는 Memory cache를 할 수 있으며, Remote/Local 데이터를 불러오게 됩니다.

sqlite 사용 시에는 [Loaders](https://developer.android.com/guide/components/loaders.html) 사용으로 비동기식 데이터를 쉽게 로드할 수 있는 방법을 사용하고, RxJava 등의 방법을 사용할 수 있습니다.

이 경우는 이번 글에서는 다루지 않습니다.

기존에 작성한 [SampleImageData.java](https://github.com/taehwandev/AndroidMVPSample/blob/04-MVP-Adapter_View/app_java/src/main/java/tech/thdev/android_mvp_sample/data/SampleImageData.java)를 이용하여 Repository 형태로 변경합니다.

변경할 Repository 형태는 구글이 설명하는 다음의 방법입니다.

![model]


<br />

## 샘플 코드

- [AndroidMVPSample](https://github.com/taehwandev/AndroidMVPSample/tree/05-MVP-Model)


<br />

## 동영상

동영상에는 Java/Kotlin이 포함됩니다.

<iframe width="640" height="360" src="https://www.youtube.com/embed/qnU23Og1yj0" frameborder="0" allowfullscreen></iframe>


<br />

## 마무리

Android MVP 무작정 따라하기는 이번 글을 마지막으로 기본적인 내용을 모두 다루었습니다.

앞으로 작성하는 글은 다음과 같은 형태입니다.

- MVP 어느 정도 범위로 적용할 수 있을까?
- View와 Model을 좀 더 명확하게 구분할 수 있는 방법은?
- Model 정의를 구글 Architecture/ios Viper처럼 만들어야 할까?
- Google Architecture 정의하는 ViewModel 이란?

지금과 마찬가지로 일부 영상을 포함할 수 있도록 하려고 합니다.


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


[model]: /images/androiddev/2017/2017-01-24-Android-MVP-Google-Architecture-Model/model.png
