---
layout: post
title: Android Module을 JitPack을 이용하여 배포하기
categories: [AndroidDev]
tags: [Android Dev, Android, JitPack, Maven]
fullview: false
comments: true
published: false
---

커니님이 JitPack을 이용하여 배포하는 방법을 정리해주셔서 따라해보았습니다.

따라해보면서 알아야 할 몇가지 사항에 대해서 추가로 정리해보려고 합니다.

우선 커니님이 작성한 JitPack은 다음의 링크를 참고하시면 되겠고, 저는 다음의 글을 참고하여 추가 사항에 대해서 몇가지 정리해 두려고 합니다.

- [커니의 안드로이드 이야기 - 오픈 소스 라이브러리 쉽게 배포하기 - JitPack](http://kunny.github.io/tip/2016/09/09/deploy_artifacts_with_jitpack/)


<br />

## Bintray와 JitPack 배포 방법 비교

- `Bintray` : `JCenter` 공식 배포가 가능하며, 설정이 복잡

```
// build.gradle에 다음과 같이 추가됩니다.
buildscript {
    repositories {
        jcenter()
    }
}
```

- `JitPack` : `Maven`을 통하 배포를 하며, gitHub에 업로드한 Library를 간단하게 배포가 가능 하지만 실제 사용시에는 다음과 같이 추가해주어야 한다.

```
// build.gradle에 다음과 같이 추가됩니다.
buildscript {
    repositories {
        jcenter()
        maven { url "https://jitpack.io" }
    }
}
```


<br />

## Bintray 배포하기

Bintray에 대한 자세한 배포 방법은 다음의 글들을 참고해주시면 되겠습니다.

- <a href="http://thdev.tech/androiddev/2016/09/01/Android-Bintray(JCenter)-Publish.html">Android Module을 Bintray(JCenter)에 배포하는 방법</a>
- <a href="http://thdev.tech/androiddev/2016/09/01/Android-Bintray(JCenter)-Publish-02.html">Android Module을 Bintray(JCenter)에 배포하기 삽질!...</a>


<br />

## JitPack을 배포하기 위한 준비

JitPack을 배포하기 위해서는 다음이 필요합니다.

- GitHub 회원 가입
- GitHub에 `모듈`을 포함한 `Repository 배포`하기(Android Studio로 배포하시면 됩니다.)
- 

## JitPack 배포를 위한 라이브러리 추가


JitPack을 배포하기 위해서는 별도 JitPack 홈페이지 가입이 필요하지 않습니다.


그냥 몇가지 설정 추가와 라이브러리 형태의 모듈만 있다면 간단하게 JitPack에 배포가 가능합니다.

- [Android JitPack Example](https://github.com/jitpack/android-example/)



<br />

## 마무리




- [커니의 안드로이드 이야기 - 오픈 소스 라이브러리 쉽게 배포하기 - JitPack](http://kunny.github.io/tip/2016/09/09/deploy_artifacts_with_jitpack/)
