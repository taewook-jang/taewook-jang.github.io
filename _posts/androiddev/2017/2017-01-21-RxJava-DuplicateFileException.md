---
layout: post
title: RxJava DuplicateFileException 예외처리
categories: [Git]
tags: [Git, Git Branch]
fullview: false
comments: true
published: false
---

RxJava 1.x와 2.x 버전을 함께 사용하게 될 수 있습니다.

이 경우 빌드 중 아래와 같은 오류가 발생할 수 있습니다.

```
Error:Execution failed for task ':app:transformResourcesWithMergeJavaResForDebug'.
        > com.android.build.api.transform.TransformException: com.android.builder.packaging.DuplicateFileException: Duplicate files copied in APK META-INF/rxjava.properties
File1: /Users/tae-hwan/.gradle/caches/modules-2/files-2.1/io.reactivex/rxjava/1.2.3/7fe1a94c1aeb958acc876fe616922cc191f3222c/rxjava-1.2.3.jar
File2: /Users/tae-hwan/.gradle/caches/modules-2/files-2.1/io.reactivex.rxjava2/rxjava/2.0.1/57f850a6b317e5582f1dbaff10a9e7d7e1fcdcfb/rxjava-2.0.1.jar
```

RxJava
