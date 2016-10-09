---
layout: post
title: Android에 MVP를 적용하자 - 기본부터보기
categories: [AndroidDev]
tags: [Android, AndroidDev, MVP]
fullview: false
comments: true
published: false
---

Android에서 적용하는 MVP를 순차적으로 정리해보려고합니다.

기존에는 TODO MVP 등의 자료를 활용하여 MVP가 무엇인지, 중간 단계부터 보았는데 이번 글에서는 기본부터 차근차근 살펴보려고 합니다.


<br />

## MVP를 적용하기 위해선?

MVP의 각각을 잘 알아야 합니다.

![mvp]

- model : `Data`를 가져온다.
- View : `이벤트가 발생`하고, `Presenter`로 전달
  - View와 관련된 내용만 담는다.
- Presenter : `View`에서 전달 받은 `이벤트`에 대하여 Data를 가져오고 이를 `가공`하여 `View`에 전달한다.
  - `Model`과 `View`에 대한 `이벤트 전달`, `가공`을 한다


<br />

## MVP를 시작하기 전에 참고 자료

저는 TOD MVP를 살펴보았고,

- [Android TODO MVP 어떻게 적용할까?](http://thdev.tech/androiddev/2016/06/14/Android-TODO-MVP-Example.html)

다음으로 Kotlin의 베이스로 작성한 MVP 패턴을 소개하였습니다.

- [Android Kotlin으로 작성한 Presenter/View 살펴보기](http://thdev.tech/androiddev/kotlin/2016/09/22/Android-Base-Presenter_View.html)

혼자 만들려고쓴 것이라서 처음 보시는 분이라면 MVP를 이해하기 쉽지는 않은것 같습니다.

Android에서 Activity와 Fragment에서 적용하는 MVP를 처음부터 순차적으로 적용해보고, 따라해보실 수 있도록 만들어보았습니다.

다음의 글에 대한 MVP 예제는 GitHub - Android MVP의 branch에서 확인이 가능합니다.




<br />

## Android Activity에 적용하는 MVP

첫 번째로 Activity에서 적용하는 MVP입니다.

우선 일반적인 MVP를 적용해보겠습니다.

[mvp]: /images/2016/2016-05-03-MediaProjection-MVP-Pattern/MVP.png
