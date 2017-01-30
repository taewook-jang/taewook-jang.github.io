---
layout: post
title: Android MVP 무작정 따라하기 - Adapter Contract 정의하기
categories: [AndroidDev]
tags: [Android, AndroidDev, MVP]
fullview: false
comments: true
published: true
---

Android MVP 무작정 따라하기 5번째로 Adapter에 대한 Contract 정의하는 방법을 설명합니다.

이번 글에서는 Adapter에 대한 Contract 정의를 하고, 이를 상속받아 사용하는 방법을 정리합니다.


<br />

## Adapter Contract 정의란?

이전 글에서 `Activity`/`Fragment`에서 사용할 `Contract`을 정의하고, 분리해보았습니다.

하지만 아래와 같이 처리합니다.

![mvp_adapter]

복잡해 보이지만 정리하면 다음과 같습니다.

- `View` : `Presenter`에게 데이터를 요청
- `Presenter` : `Model`에게 데이터 요청
- `Model` : 데이터를 `Presenter`로 전달
- `Presenter` : `model`로부터 전달받은 데이터를 `View`에게 전달
- `View` : `View`에서 가지고 있던 `Adapter`에게 데이터를 추가하고, `notify`를 처리


<br />

### Presenter에서 직접 data를 전달하지 않으면?

MVP 방식으로 분리만 하면 위에서 설명한 그림과 같이 동작합니다.

그럼 단점은 무엇일까요?

### 단점

Presenter에서 Adapter의 데이터를 저장하고, 불러오는 모든 부분에 항상 View가 함께 해야 합니다.

- Presenter에서 Adapter에 데이터를 셋팅하는 경우
  - View에서 이를 전달받아 Adapter에 저장
- 사용자의 onClick 이벤트를 받아 Presenter에서 처리한다면?
  - View에서 onClick 시에 필요한 데이터를 Adapter로부터 전달받아야 하므로, 필요한 함수(`getItem`)가 만들어져야 한다.

위와 같은 상황이 아니더라도 모두 `View`에서 이를 대신 받아서 전달해야 합니다.

그럼 복잡도가 높아지고, 귀찮아집니다.


<br />

## 그래서 좀 더 좋은 방법은?

좀 더 좋은 방법은 예전 GDE 스티브님 [Adapter, 누구냐 넌? — Data? View?](https://medium.com/@jsuch2362/adapter-%EB%88%84%EA%B5%AC%EB%83%90-%EB%84%8C-data-view-2db7eff11c20#.klca01cu9)에 소개된 방법입니다.

여기에 저는 `Contract`를 추가하였습니다.

그래서 다음과 같이 Adapter에 대한 Contract을 다시 생성하였습니다.

```java
interface AdapterContract {
  interface View {}

  interface Model {}
}
```


<br />

## Contract을 Adapter에 상속받아 구현

View/Model을 구분해서 Contract을 생성하였으니, Adapter에서 이를 상속받아서 구현해줍니다.

```kotlin
class SampleAdapter : RecyclerView.Adapter(), AdapterContract.View, AdapterContract.Model {
  // 생략
}
```

그리고 기존 View에서 정의하였던, `addItem`, `notify`에 대해서 `AdapterContract`에서 정의하도록 하고, 이를 Adapter에서 구현 만 합니다.


<br />

## Presenter에서는?

Presenter에서는 `AdapterContract.View`와 `AdapterContract.Model`을 구현합니다.

```java
class SamplePresenter implement SampleContract.Presenter {
  private AdapterContract.View adapterView;
  private AdapterContract.Model adapterModel;

  public void setAdapterView(AdapterContract.View adapterView) {
    this.adapterView = adapterView;
  }

  public void setAdapterModel(AdapterContract.Model adapterModel) {
    this.adapterModel = adapterModel;
  }
}
```

이렇게 구현해주면, 이후에는 `adapterView`/`adapterModel`을 통해서 직접 Adapter를 접근하게 됩니다.

기존에서와 같이 `View`를 통해서 접근할 필요성이 없어집니다.

그래서 다시 그리면 아래와 같습니다.

![mvp_adapter_contract]


<br />

## 마무리

다음 글에서는 라이브 코딩을 통해서 AdapterContract을 생성하고, 위와 같은 그림으로 변경하는 작업을 해보도록 하겠습니다.

감사합니다.


<br />

## MVP 무작정 따라하기

무작정 따라 하기는 MVP 패턴을 이해할 수 있도록 하나씩 차근차근 작성해보는 예제입니다.

그래서 각각의 브런치와 글들을 통해서 조금씩 확장해가려고 합니다.


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


[mvp_adapter]: /images/2016/2016-12-26-Android-MVP-Four/mvp_adapter.png
[mvp_adapter_contract]: /images/2016/2016-12-26-Android-MVP-Four/mvp_adapter_contract.png
