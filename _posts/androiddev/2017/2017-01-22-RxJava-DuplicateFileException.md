---
layout: post
title: RxJava 1.x/2.x DuplicateFileException 예외처리
categories: [AndroidDev]
tags: [Android, RxJava]
fullview: false
comments: true
published: true
---

RxJava 1.x와 2.x 버전이 있습니다.

모든 라이브러리가 아직 RxJava 2.x 버전 대응을 한 것은 아닙니다.

그에 따라서 1.x와 2.x 버전을 함께 사용하게 됩니다.

Gradle 동기화 중에는 오류가 발생하지 않지만, `run`을 진행하면 아래와 같은 오류가 발생할 수 있습니다.

이 오류를 해결하는 방법을 간단하게 정리합니다.

> Error:Execution failed for task ':app:transformResourcesWithMergeJavaResForDebug'.
        > com.android.build.api.transform.TransformException: com.android.builder.packaging.DuplicateFileException: Duplicate files copied in APK META-INF/rxjava.properties
File1: /Users/tae-hwan/.gradle/caches/modules-2/files-2.1/io.reactivex/rxjava/1.2.3/7fe1a94c1aeb958acc876fe616922cc191f3222c/rxjava-1.2.3.jar
File2: /Users/tae-hwan/.gradle/caches/modules-2/files-2.1/io.reactivex.rxjava2/rxjava/2.0.1/57f850a6b317e5582f1dbaff10a9e7d7e1fcdcfb/rxjava-2.0.1.jar


<br />

## 작성한 Gradle

작성한 Gradle은 다음과 같습니다. `RxJava 2`와 `sqlbrite`입니다.

sqlbrite는 아직 Rx 2.x 버전을 지원하지 않습니다. 그래서 RxJava 1.x 버전에 dependencies가 걸려있습니다.

```
apply plugin: 'com.android.application'

android {
    compileSdkVersion 24
    buildToolsVersion "24.0.3"

    // 생략
}

dependencies {
    compile "io.reactivex.rxjava2:rxjava:$rootProject.ext.rxjavaVersion"
    compile "com.squareup.sqlbrite:sqlbrite:$rootProject.ext.sqlbriteVersion"
}
```

위를 Gradle 싱크 상에는 문제가 없지만 `Run`을 통해 packaging 진행을 하면 오류가 발생합니다.

> com.android.build.api.transform.TransformException: com.android.builder.packaging.DuplicateFileException: Duplicate files copied in APK META-INF/rxjava.properties


<br />

## 해결 방법

RxJava 1.x와 2.x에서 META-INF 파일 중복으로 나오기 때문에 `packagingOptions에 exclude` 처리합니다.

참고로 .gradle 폴더의 caches 폴더에 위와 같은 .jar를 확인해볼 수 있습니다.

그래서 기존 gradle에 다음과 같이 추가하여 해결할 수 있습니다.

```
apply plugin: 'com.android.application'

android {
    compileSdkVersion 24
    buildToolsVersion "24.0.3"

    // 생략

    packagingOptions {
    exclude 'META-INF/rxjava.properties'
    }
}

dependencies {
    compile "io.reactivex.rxjava2:rxjava:$rootProject.ext.rxjavaVersion"
    compile "com.squareup.sqlbrite:sqlbrite:$rootProject.ext.sqlbriteVersion"
}
```


<br />

## 마무리

위와 같이 작업해주면 오류가 해결되고, packaging 처리와 앱 동작에 문제가 없게 됩니다.

감사합니다.
