---
layout: post
title: Android MVP 무작정 따라하기 - Intro
categories: [AndroidDev]
tags: [Android, AndroidDev, MVP]
fullview: false
comments: true
published: true
---

Android에서 테스트 코드를 작성하기 위한 방법 중 하나로 MVP를 선택합니다.

MVP는 다음과 같이 생겼습니다.

![mvp]

`Model`, `View`, `Presenter` 간의 상호 의존성을 떨어트리기 위한 용도임과 동시에 Test Code 작성을 위한 최적의? 구조 중 하나입니다.

기존 안드로이드 코드 작성 시에는 `View`에 모든 코드가 포함되어 있다거나, 중복 코드를 `Util` 형태로 사용하는 경우도 많았습니다.

그에 비해 `MVP`는 각각의 독립된 코드의 구현이 가능한 형태입니다.


<br />

## MVP를 적용하기 위해선?

저는 `Test 코드` 작성을 위해서 `MVP`를 시작하였습니다.

의도는 좋지만, 그에 비해서 어떤 식으로 접근해야 할지에 대해서 난해합니다.

`MVP`를 통해서 `Test 코드 작성`하는 것도 좋고, `View`를 완전히 분리시킬 수 있다는 점도 좋습니다.

일단 이런 이야기는 `MVP`에 익숙한 사람이나 할 수 있는 말입니다.

그래서 이번 글에서는 `MVP`의 동작 방법에 대해서 그림과 함께 우선 정리해보려고 합니다.


<br />

## 그간 MVP에 대해서 정리한 글

저는 무작정 [google android architecture](https://github.com/googlesamples/android-architecture)를 따라 하였고, `GDE 스티브(정승욱)`님의 [Adapter, 누구냐 넌? — Data? View?](https://medium.com/@jsuch2362/adapter-%EB%88%84%EA%B5%AC%EB%83%90-%EB%84%8C-data-view-2db7eff11c20#.klca01cu9)을 따라 해보기도 하였습니다.

- [Android TODO MVP 어떻게 적용할까?](http://thdev.tech/androiddev/2016/06/14/Android-TODO-MVP-Example.html)

중간중간 추천하는 자료들도 많이 살펴보았는데 그 중 `GDE 커니`님이 소개해준 MVP Dribbble - [Bourbon](https://github.com/hitherejoe/Bourbon)

이런 자료들의 조합이 현재 제가 만든 `Kotlin` 기반의 `MVP Base`를 만들기도 하였습니다.

- [Android Kotlin으로 작성한 Presenter/View 살펴보기](http://thdev.tech/androiddev/kotlin/2016/09/22/Android-Base-Presenter_View.html)


<br />

## MVP란?

그러면 `MVP`는 어떤 단어의 조합일까요?

일단 MVP에 대해서 알기 위해서는 MVP의 각 단어의 역할과 목적이 중요합니다. - `이건 MVC도 마찬가지입니다.`

그래서 다음과 같이 `View`, `Presenter`, `Model`의 약자를 먼저 정리해보면...

- `model` : `Data`와 관련된 처리를 담당
  - `Data`의 전반적인 부분을 `model`에서 담당하고, `네트워크`, `로컬 데이터` 등을 포함
- `View` : 사용자의 실질적인 `이벤트가 발생`하고, 이를 처리 담당자인 `Presenter`로 전달
  - 완전한 `View`의 형태를 가지도록 설계합니다. 계산을 하거나, 데이터를 가져오는 등의 행위는 `Presenter`에서 처리하도록
- `Presenter` : `View`에서 전달받은 `이벤트`를 처리하고, 이를 다시 `View`에 전달
  - `View`와는 무관한 `Data`등을 가지고, 이를 가공하고, `View`에 다시 전달하는 역할


<br />

## MVP의 기본 패턴

MVP를 처음 접하고, 테스트 코드를 작성하지 않는다면 다음과 같은 느낌을 받을 수 있습니다.

- 이거 뭐 그냥 `빙글 빙글` 돌려놓은 것 아닐까?

그래서 아래와 같은 그림으로 이를 표현할 수 있습니다.

![mvp-default]

<br />

이를 순서로 나열하면...

1. `View` : `View`에서 `터치 이벤트` 발생
2. `View` -> `Presenter` : `Presenter`로 `이벤트` 전달
3. `Presenter` : `View`에서 요청한 `이벤트` 처리
4. `Presenter` -> `View` : 처리한 결과를 `View`로 전달
5. `View` : 처리된 결과를 바탕으로 UI를 갱신

위와 같은 형태입니다. 1~5번까지 처리가 완료되면 이후 같은 동작이 계속 적으로 일어납니다.

일반 코드라면 한 페이지 안에서 모두 처리가 되니깐 눈으로 보긴 편합니다.

따라갈 필요도 없고, 함수만 따라다니면 보기도 쉬우니깐요.

그래서 `빙글 빙글` 돌려놓은 것 아닐까?라는 답이 나올 수밖에 없습니다.


<br />

## MVP에 모델을 더하면?

위에 기본적으로 작성한 MVP에 모델을 더하면 다음과 같이 표현됩니다.

![mvp-model]

글로 작성하면 아래와 같습니다.

1. `View` : `View`에서 `터치 이벤트` 발생
2. `View` -> `Presenter` : `Presenter`에 이벤트 전달
3. `Presenter` : `이벤트`의 형태에 따라 캐시 데이터를 가져오거나, `Model`에 요청
4. `Presenter` -> `Model` : `Presenter`에서 데이터를 요청받음
5. `Model` : `데이터`를 로컬 또는 서버에서 가져온다
6. `Model` -> `Presenter` : `Model`로부터 데이터를 통보받는다
7. `Presenter` : 전달받은 데이터를 가공
8. `Presenter` -> `View` : 가공한 데이터를 `View`에 전달
9. `View` -> `Presenter`로 전달받은 데이터를 View에 갱신

여기까지의 단계를 거치게 됩니다.

상황에 따라서 Presenter는 `Model`을 사용할 수도 하지 않을 수도 있지만 기본 형태는 위와 같습니다.


<br />

## 마무리

앞으로 이러한 MVP 정의를 기본부터 차근차근 적용해볼 수 있도록 정리해보려고 합니다.

이해하기 어려우니 우선 따라 해보는게 가장 좋은 것 같습니다.

좀 더 쉽게 접근할 수 있도록 `MVP` 무작정 따라 하기를 만들어볼 예정입니다.

- Activity
- Fragment
- RecyclerAdapter

정도를 다루어볼 예정입니다.


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


[mvp]: /images/2016/2016-05-03-MediaProjection-MVP-Pattern/MVP.png

[mvp-default]: /images/2016/2016-10-12-Android-MVP-Intro/mvp-default.png
[mvp-model]: /images/2016/2016-10-12-Android-MVP-Intro/mvp-model.png
