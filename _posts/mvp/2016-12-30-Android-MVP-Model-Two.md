---
layout: post
title: Android MVP 무작정 따라하기 - Google Architecture의 Model
categories: [AndroidDev]
tags: [Android, AndroidDev, MVP]
fullview: false
comments: true
published: false
---


<br />

## Google Architecture의 Model

제가 샘플에 추가한 예제의 Model은 기본적인 데이터를 불러오는 부분입니다.

이 때 apk에 포함된 drawable을 불러오도록 만들었습니다.

Google Architecture에서는 Repository라는 이름을 사용하여 정리하고 있습니다.

그림은 다음과 같이 확인이 가능합니다.

![mvp_model_01]

이 Repository에서는 `Remote data source/Local data source` 등이 포함될 수 있는데 위의 그림에서 빨강색 부분에 해당됩니다.

위에서 사용한 예제는 [Android Architecture - TODO MVP ContentProviders](https://github.com/googlesamples/android-architecture/tree/todo-mvp-contentproviders/)입니다.

이 Repository에는 Remove/Local 데이터를 관리하고 불러오게 됩니다.

Remote data에서는 HTTP/Socket 을 통해서 데이터를 받아오고 이를 Presenter에 Callback 또는 return 하게 됩니다.

각각의 역할은 다음과 같습니다.

- Repository : Remote/Local을 구분하며, Memory cache를 포함
- Remote : 서버를 통한 데이터를 불러온다
- Local : 단말기 상의 SQL, Realm 등을 통한 데이터를 불러온다

각각을 정리해보면

- Presenter > Repository을 통해서 호출한다
- Repository에서 상황에 따라서 서버/로컬 데이터를 선택적으로 불러온다
- Local/Remote에서는 Callback/return을 통해서 데이터를 return 한다

Google Architecture에서는 위와 같은 것으로 정의합니다.

Remote/Local에서도 RxJava와 함께 사용하거나, Async/Await을 통해 접근하게되면 `return` 형태가 만들어지게 됩니다.

좀 더 자세한 내용은 이어지는 글에서 추가로 정리하겠습니다.
