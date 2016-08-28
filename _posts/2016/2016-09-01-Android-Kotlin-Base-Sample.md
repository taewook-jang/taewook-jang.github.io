---
layout: post
title: Android Kotlin으로 Base 코드 작성하기
categories: [AndroidDev, Kotlin]
tags: [Android Kotlin, Android, Kotlin]
fullview: false
comments: true
published: false
---

Kotlin을 공부하면서 Android용 Base를 작업해보았습니다. 기존에는 포함을 하였다가 별도 배포를 시도해보려고 분리하였습니다. 아직은 배포를 하지 않았고, 별도 Repository로 관리하고 있습니다.

배포를 하여 dependencies를 통해서 사용하도록 만들어볼 계획입니다.

Kotlin을 이용하여 Android에서 사용할 Base 코드를 작성하여 사용중에 있습니다. Base 코드는 모두 Kotlin을 활용하여 작성하고 있으며, 일부 Util을 포함하려고 만들었습니다.

Base를 코틀린으로 작성한 이유는 간단합니다.

- Kotlin에서 작업의 용의성
- Java에서 불러쓰더라도 문제 없음

사실은 공부를 위해서 다음과 같이 작업하여 사용하고 있습니다. 부족하지만 일부 코드를 쉽게 접근하여 사용할 수 있으며 ButterKnife도 기본적으로 가져갈 수 있어 Java의 Activity에서 사용하더라도 문제가 없습니다.
