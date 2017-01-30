---
layout: post
title: Android MVP 무작정 따라하기 - Adapter Contract 정의하기 2번째(동영상)
categories: [AndroidDev]
tags: [Android, AndroidDev, MVP]
fullview: false
comments: true
published: true
---

6번째로 이전 글에서 정리하였던 Adapter Contract을 정의하고, 이를 Presenter에서 호출하여 사용할 수 있는 구조를 만들어 보는 방법을 동영상을 통해 정리하였습니다.


<br />

## Sample

- [GitHub AdapterContract 샘플](https://github.com/taehwandev/AndroidMVPSample/tree/03-MVP-AdapterContract)


<br />

## 다음을 정리합니다.

이전 동영상에서 View -> Presenter -> Model -> Presenter -> View -> Adapter을 정의하였습니다.

오늘은 아래 그림과 같이 `View > Presenter > Model > Presenter > Adapter View/Model`을 바로 갱신하게 됩니다.

그래서 `Activity/Fragment`의 `View`를 한 단계 더 분리하고, 이를 좀 더 편하게 관리하기 위함입니다.

![mvp_adapter_contract]


<br />

## 동영상으로 확인하기

다음 동영상은 Kotlin/Java에서의 AdapterContract 정의하는 방법을 담고 있습니다.

<iframe width="640" height="360" src="https://www.youtube.com/embed/Wsv6fFHqO44?rel=0" frameborder="0" allowfullscreen></iframe>


<br />

## 마무리

Presenter와 Adapter Model/View 간을 정리하였습니다.

다음 글에서는 간단하게 Adapter View의 OnClickListener 구현을 정리하고, 이어지는 글에서 다음을 정리하려고 합니다.

- Model 정리 : 현재 작성한 모델을 기준
- Model 정리 - Google Architecture : 구글에서 설명하는 Model을 설명합니다.
- Model 정리 구현 : 직접 구현해보려고 합니다.

각각 단계에서 필요한 경우 동영상을 함께 올리려고 합니다.

감사합니다.


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



[mvp_adapter_contract]: /images/2016/2016-12-26-Android-MVP-Four/mvp_adapter_contract.png
