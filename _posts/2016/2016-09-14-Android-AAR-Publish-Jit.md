---
layout: post
title: Android Module을 JitPack을 이용하여 배포하기
categories: [AndroidDev]
tags: [Android Dev, Android, JitPack, Maven]
fullview: false
comments: true
published: true
---

커니님이 JitPack을 이용한 aar 배포하는 방법을 정리해서 따라 해보았습니다.

따라 해보면서 몇 가지 추가 사항에 대해서 정리해보려고 합니다.

우선 커니님이 작성한 JitPack은 다음의 글을 통해 확인이 가능합니다.

- [커니의 안드로이드 이야기 - 오픈 소스 라이브러리 쉽게 배포하기 - JitPack](http://kunny.github.io/tip/2016/09/09/deploy_artifacts_with_jitpack/)


<br />

## Bintray와 JitPack 배포 방법 비교

- `Bintray` : `JCenter` 공식 배포가 가능하며, 몇 가지 설정을 직접 해주어야 합니다.(Bintray 배포를 위한 설정)

```
// build.gradle에 다음과 같이 추가됩니다.
buildscript {
    repositories {
        jcenter()
    }
}
```

- `JitPack` : `Maven`을 통하 배포를 하며, `gitHub`에 업로드한 `Library`를 별도의 설정 없이 배포가 가능합니다. 실제 라이브러리를 사용하기 위해서는 다음과 같은 설정을 추가해주어야 합니다.

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

Bintray를 통한 배포 방법은 지난 2번의 글에 이미 작성을 해두었습니다. 아래와 같은 2가지 글 중 첫 번째 글만 보시면 배포하시는데 도움이 되실 것입니다.

- <a href="http://thdev.tech/androiddev/2016/09/01/Android-Bintray(JCenter)-Publish.html">Android Module을 Bintray(JCenter)에 배포하는 방법</a>
- <a href="http://thdev.tech/androiddev/2016/09/01/Android-Bintray(JCenter)-Publish-02.html">Android Module을 Bintray(JCenter)에 배포하기 삽질!...</a>


<br />

## JitPack을 배포하기 위한 준비

JitPack을 배포하기 위해서는 다음이 필요합니다.

- GitHub 회원 가입
- GitHub에 `모듈`을 포함한 `Repository 업로드`하기(Android Studio로 배포하시면 됩니다.)
  - Android Studio를 통한 GitHub에 공유하는 방법은 다음의 글을 참고하시면 되겠습니다.
   - [Android Studio GitHub 공유하기 및 사용하기](/androiddev/2016/09/13/Android-Studio-GitHub-Share.html)

<br />

## JitPack 배포를 위한 라이브러리 추가

JitPack에 Android `aar` 라이브러리를 배포하기 위해서는 GitHub에 코드를 올려야 합니다.

그전에 몇 가지 설정을 해주어야 하는데 다음과 같습니다.

- [JitPack.io에 안드로이드 배포하기](https://jitpack.io/docs/ANDROID/)

위의 글에 짧게 배포를 위한 준비와 사용 방법이 담겨 있습니다.

많은 설정도 필요 없고 가장 기본적으로 다음과 같이 설정을 추가하시면 되겠습니다.

root의 `build.gradle`에 아래와 같이 `maven-gradle-plugin:1.x` 버전을 추가해줍니다.

현재 가장 최신 버전이 1.5입니다.

- [Gradle Android Maven plugin](https://github.com/dcendents/android-maven-gradle-plugin)

```
buildscript {
    dependencies {
        classpath 'com.github.dcendents:android-maven-gradle-plugin:1.5'
    }
}
```

그리고 아래와 같이 추가하시면 되겠습니다.

역시 `Library` 프로젝트에 포함된 `build.gradle`에 다음을 추가해주시면 되겠습니다.

```
apply plugin: 'com.android.library'
apply plugin: 'com.github.dcendents.android-maven'
```

그리고 아래와 같이 `group`을 설정해주시면 됩니다.

자신의 GitHub 계정 주소를 사용하시면 되는데 `com.github`로 시작하면 되고, 끝에는 자신의 github 계정 이름을 추가해주시면 됩니다.

GitHub 계정은 로그인하신 후 자신의 계정에 접근해보시면 표시되는 `닉네임`입니다.

```
group = 'com.github.taehwandev'
```

여기까지 하면 JitPack 배포 준비가 완료되었습니다. Bintray에 비해서 설정해주어야 할 부분이... 거의 없이 이걸로 끝입니다.

이제 GitHub에 공유 또는 수정 사항을 `push` 해줍니다.


<br />

## 깃허브에서 태그 따기

push가 완료되면 GitHub에서 `Release(TAG)`를 따주면 됩니다. `새로운 Release` 따주시면 JitPack에서 새로운 버전을 탐지하여 배포가 일어나게 됩니다.

자신의 GitHub의 방금 업로드한 `Repository`로 이동합니다.

그러면 아래와 같이 메뉴가 나타나게 됩니다. 여기에서 `release` 부분을 눌러줍니다.

![release-01]

처음 접근하시면 아래와 같이 `Create new release` 버튼이 보이게 됩니다.

![release-new]

이미 여러 개 생성되신 상태라면 아래와 같이 표시되고, 오른쪽의 `Draft a new release` 버튼을 눌러주시면 되겠습니다.

![release-02]

여기에서 `Tag version`을 입력해줍니다.

예를 들어 배포되는 라이브러리의 버전이 `1.0.0`이라면 1.0.0을 `Tag version`에 써주시면 되겠습니다.

그 외에 다른 내용은 추가해주지 않으셔도 됩니다.

![release-03]

추가하게 되면 결과적으로 다음과 같이 확인이 가능합니다.

![release-result]


여기까지 해주시면 JitPack 동기화 준비가 끝이 납니다.


<br />

## JitPack에 배포하기

GitHub에 배포하고, Release 버전을 추가해주었으니 이제는 JitPack에 배포를 해야 합니다.

배포의 과정이 어려운 것은 없고 간단하게 처리가 가능합니다.

[JitPack](https://jitpack.io/) 홈페이지 접속하면 아래와 같이 `Git repo url`을 추가할 수 있는 란이 있습니다.

이 공간에 자신의 GitHub 라이브러리 배포를 위한 URL을 입력합니다.

저는 `JitPackExample`이라서 아래와 같이 추가하였습니다.

https://github.com/taehwandev/JitPackExample

![publish-01]

그러면 다음과 같이 `Status`와 `Log`의 상태가 확인이 됩니다.

가장 상위에 있는 버전만 배포 시도가 일어나고, 이전에는 배포가 되지 않습니다.

![publish-02]

하단의 `Build` 버튼을 누르면 배포된 것들만 모아서 볼 수 있습니다.

![publish-03]

그리고 `Log`를 통해 배포 상태를 확인할 수 있는데 빌드 과정 중 발생한 오류에 대한 정보도 함께 제공됩니다.

![publish-04]

빌드가 정상적으로 끝이 났다면, `Get it` 버튼이 활성화되고, 이를 누르면 다음과 같이 결과 화면을 볼 수 있습니다.

JitPack의 라이브러리를 배포하게 되면 다음과 같이 설정해주어야 할 부분이 발생합니다.

![publish-result]

<br />

배포가 완료되면 다음과 같이 사용됩니다.

```
compile 'com.github.taehwandev:프로젝트 이름:Release 버전 정보'
```


<br />

## JitPack으로 배포된 라이브러리 사용하기

우선 rootProject의 `build.gradle`에 다음을 추가해주시면 됩니다.

```
buildscript {
    repositories {
        jcenter()
        maven { url "https://jitpack.io" }
    }
}
```

그리고 다음의 `dependencies`를 추가해주면 됩니다.

```
dependencies {
      compile 'com.github.taehwandev:JitPackExample:1.0.2'
}
```


<br />

## 마무리

Bintray보다 배포가 쉬운 JitPack에서의 배포 방법을 살펴보았습니다.

Bintray 보다 설정이 쉬워서 배포하기 쉽지만 사용할 때 `maven` url을 추가해주어야 합니다.

배포 URL을 추가하는게 귀찮긴 하지만 사용 방법이 여러 개가 있으니...

커니님이 작성한 JitPack 배포하는 방법은 아래의 링크를 참고하시면 되겠습니다.

- [커니의 안드로이드 이야기 - 오픈 소스 라이브러리 쉽게 배포하기 - JitPack](http://kunny.github.io/tip/2016/09/09/deploy_artifacts_with_jitpack/)


<br />

## Bintray 배포하기

Bintray를 통한 배포 방법은 지난 2번의 글에 이미 작성을 해두었습니다. 아래와 같은 2가지 글 중 첫 번째 글만 보시면 배포하시는데 도움이 되실 것입니다.

- <a href="http://thdev.tech/androiddev/2016/09/01/Android-Bintray(JCenter)-Publish.html">Android Module을 Bintray(JCenter)에 배포하는 방법</a>
- <a href="http://thdev.tech/androiddev/2016/09/01/Android-Bintray(JCenter)-Publish-02.html">Android Module을 Bintray(JCenter)에 배포하기 삽질!...</a>





[release-01]: /images/2016/2016-09-14-Android-AAR-Publish-Jit/release-01.png
[release-02]: /images/2016/2016-09-14-Android-AAR-Publish-Jit/release-02.png
[release-03]: /images/2016/2016-09-14-Android-AAR-Publish-Jit/release-03.png
[release-new]: /images/2016/2016-09-14-Android-AAR-Publish-Jit/release-new.png
[release-result]: /images/2016/2016-09-14-Android-AAR-Publish-Jit/release-result.png

[publish-01]: /images/2016/2016-09-14-Android-AAR-Publish-Jit/publish-01.png
[publish-02]: /images/2016/2016-09-14-Android-AAR-Publish-Jit/publish-02.png
[publish-03]: /images/2016/2016-09-14-Android-AAR-Publish-Jit/publish-03.png
[publish-04]: /images/2016/2016-09-14-Android-AAR-Publish-Jit/publish-04.png
[publish-result]: /images/2016/2016-09-14-Android-AAR-Publish-Jit/publish-result.png
