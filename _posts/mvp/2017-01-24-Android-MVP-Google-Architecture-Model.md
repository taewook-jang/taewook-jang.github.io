---
layout: post
title: Android MVP 무작정 따라하기 - Google Architecture Model(영상 포함)
categories: [AndroidDev]
tags: [Android, AndroidDev, MVP]
fullview: false
comments: true
published: false
---

MVP 무작정 따라하기 10 번째 글입니다.

오늘은 구글 Architecture 예제인 Model을 구현하는 방법을 영상을 통해 직접 구현해보도록 하겠습니다.

- [가장 기본적인 Model 정의](https://github.com/googlesamples/android-architecture/tree/todo-mvp/)

위의 가장 기본적인 Model 정의를 구현해보도록 하겠습니다.

현재까지 작성한 모델은 단순하게 생성한 것이고, Google Architecture 정의인 Repository를 구현합니다.


<br />

## Repository

이전 글에서 설명하였던 Repository에 대해서 제가 작성 중인 샘플에 적용하면 다음과 같습니다.

기존 Model의 이름을 Repository로 변경하였으며, Local/Remote 데이터가 한단계 더 추가됩니다.

이런 Repository는 여러개가 생성될 수 있습니다.

![model]

이번 글에서는 Repository를 정리하는 부분을 동영상을 통해 확인할 수 있습니다.


<br />

## 샘플 코드

- [AndroidMVPSample](https://github.com/taehwandev/AndroidMVPSample)


<br />

## 동영상

동영상에는 Java/Kotlin이 포함됩니다.


<br />

## 마무리

Android MVP 무작정 따라하기는 이번글을 마지막으로 기본적인 내용을 모두 다루었습니다.

앞으로 작성하는 글은 다음과 같은 형태입니다.

- MVP 어느정도 범위로 적용할 수 있을까?
- View와 Model을 좀 더 명확하게 구분할 수 있는 방법은?
- Model 정의를 구글 Architecture/ios Viper 처럼 만들어야 할까?
- Google Architecture 정의하는 ViewModel이란?

등을 살펴보고 MVP 작성한 부분에 대한 테스트 코드 작성을 순차적으로 작성해보려고 합니다.

지금과 마찬가지로 일부 동영상이 포함되도록 진행해보겠습니다.


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
- [Android MVP 무작정 따라하기 - Google Architecture Model(영상 포함)]()


[model]: /images/androiddev/2017/2017-01-24-Android-MVP-Google-Architecture-Model/model.png
