---
layout: post
title: Android Module을 Bintray(JCenter)에 배포하는 방법
categories: [AndroidDev]
tags: [Android Dev, Android, Bintray, JCenter]
fullview: false
comments: true
published: true
---

Eclipse에서 Android Studio로 넘어오면서 gradle을 사용하게 되었습니다.

별도의 jar를 다운로드해 추가할 필요 없이 build.gradle에 dependencies 추가만 해주면 Android Studio가 해당 URL의 aar을 자동으로 받아 임포트 시켜줍니다.

이 dependencies에 추가되는 라이브러리를 직접 배포하기 위한 방법을 정리합니다.

TODO - 정리하기 전에

- Github를 통해 소스 코드를 배포하고 있습니다.
- Sample을 테스트하였고, 정리하였습니다.
- Module을 배포/관리해보고 싶으시다면 참고 해주세요!


<br />

## 관련 글

- <a href="http://thdev.tech/androiddev/2016/09/01/Android-Bintray(JCenter)-Publish.html">Android Module을 Bintray(JCenter)에 배포하는 방법</a>
- <a href="http://thdev.tech/androiddev/2016/09/03/Android-Bintray(JCenter)-Publish-02.html">Android Module을 Bintray(JCenter)에 배포하기 삽질!...</a>


<br />

## AAR 배포

aar은 안드로이드 스튜디오를 통해 생성되는 라이브러리입니다. Android Archive Library의 약자입니다.

이 `aar`은 어떤 식으로 배포가 되고 있는지 먼저 정리해보겠습니다.

현재 `aar`의 배포 방법은 약 3가지 정도로 정리가 가능합니다.

- 개인 서버를 통해 maven 형태의 aar 배포 서버 운영 및 활용(소스 코드 공개가 힘든 경우)

이 경우 직접 maven 서버를 운영하고 해당 url을 직접 적어주게 됩니다. 사실 2가지 주요 서비스인 JCenter와 Maven Contral을 제외하면 대부분이 이 방법입니다.

```
// build.gradle에 다음과 같이 추가됩니다.
buildscript {
    repositories {
        maven {
            url  "url"
        }
    }
}
```

- [Bintray](https://bintray.com/)의 JCenter을 통해 배포하는 방법

```
// build.gradle에 다음과 같이 추가됩니다.
buildscript {
    repositories {
        jcenter()
    }
}
```

- [Sonatype](http://www.sonatype.org/)의 Maven Central을 통해 배포하는 방법

```
// build.gradle에 다음과 같이 추가됩니다.
buildscript {
    repositories {
        mavenCentral()
    }
}
```

공식 서버인 Bintray의 JCenter와 Sonatype의 Maven Central 2가지 형태를 제외하고, 나머지는 모두 커스텀에 해당됩니다. 제가 소스 코드 공개가 힘든 경우라고 적었지만 공식 서비스를 제외한다면 모두 커스텀 URL을 사용하여 배포 및 사용하게 됩니다.


<br />

## 로컬에서 모듈 import 하여 사용하기

서버가 없어도 aar은 사용될 수 있습니다. 바로 module인 library를 직접 import 해서 사용하는 방법입니다. 별도의 배포가 없다면 대부분이 이 방법을 사용하게 됩니다.

이 방법은 Android Studio의 프로젝트 안에 추가 library을 생성하고, 실제 사용할 project의 build.gradle에 다음의 코드를 추가하는 방법을 사용하게 됩니다.

```
dependencies {
  compile project(":module name")
}
```

위와 같이 처리가 가능한데, 이 경우 aar이 빌드 되고, 해당 aar 빌드 파일을 import 하여 동작하게 됩니다.

하지만 모듈이 먼저 빌드 되고, 프로젝트가 빌드 되어야 해서 빌드 시간이 오래 걸릴 수 있는 단점이 있습니다.


<br />

**오늘의 정리**

오늘은 Bintray의 JCenter를 배포하는 방법을 다루어 보려고 합니다.


<br />

## Bintray

JCenter는 [Bintray](https://bintray.com/)에서 운영 중인 Meven Repository입니다.

공식 서버 중 하나이며, Android Studio에서 제공하는 support library 역시 JCenter를 통해서 배포 및 관리되고 있습니다.

Bintray의 메인 홈페이지에서도 볼 수 있지만 주요 고객사로 구글, 아마존, 넷플릭스, MS, VMWare 등의 메이저 회사들이 포함되어 있습니다.

![bintray-distribution]

Bintray은 Maven Repository만을 운영하는 서비스는 아닙니다. 아래와 같은 서비스가 많이 있으며, 모두 배포 및 관리가 가능한 무료/유료 서비스에 해당됩니다.

![bintray-service]


<br />

## Bintray에 upload 하려면

Bintray를 통해 upload 하려면 몇 가지 과정이 필요한데 다음과 같이 목차 형태로 정리해보았습니다.

- [Bintray 회원가입](https://bintray.com/)(Github/Google/Twitter/직접 가입)
- Bintray에서 Maven Repository 생성하기
- Android Studio에서 Module(library) 생성하기
- Bintray에 업로드를 위한 build.gradle 셋팅
- Bintray 사용을 위한 API 키 확인([Profile](https://bintray.com/profile/edit)을 통해 확인 가능)
- JCenter 배포를 위한 jar/docs 포함하기(jar이 없으면 JCenter 배포를 안해주네요...)
- Bintray에 업로드
- JCenter에 동기화(JCenter에 직접 올라가는 것이 아니라서 별도로 버튼을 눌러주어야 합니다.)
- JCenter를 통해 배포되면(**첫 배포시 1일 이상 소요될 수 있음**) dependencies에 추가하여 사용 가능

**ETC**

그 외 mavenCentral과도 연결하여 Bintray로 배포함과 동시에 추가 배포도 가능한 형태로 제공됩니다.

해당 방법은 이번 글에서는 다루지는 않습니다.


<br />

## Bintray 회원가입

[Bintray](https://bintray.com/) 홈페이지는 무료로 가입이 가능합니다. 서비스 이용은 개인이면 모두 오픈 조건으로 무료로 사용 가능하며, 회사 또는 private을 원하는 사용자를 위한 유료 서비스도 추가 제공됩니다.

가격은 최소 월 $45, 고급형은 $150 그리고 기업을 위한 서비스가 별도로 제공됩니다. 가격 정보 및 서비스 조건은 다음의 [Bintray-pricing](https://bintray.com/account/pricing?tab=account&type=pricing)에서 확인하시면 됩니다.

[Bintray](https://bintray.com/) 홈페이지에서 `Sign In` 버튼을 눌러 회원 가입 또는 로그인을 진행 가능합니다.

![bintray-signup-01]

저는 Github를 기본 계정으로 사용하기 위해서 Github를 사용하여 승인하였고, 추가로 Name, Username, Email을 설정해줌으로써 회원가입 절차가 완료됩니다.

![bintray-signup-03]


<br />

## Bintray Repository 생성하기

로그인 상태라면 아래와 같은 화면을 확인할 수 있습니다. 당연히 처음 가입하셨다면 `Owned Repository`가 0이 표시됩니다. 저는 이미 테스트한다고 추가한 상태라서 다음과 같이 표시됩니다.

아래의 화면에서 왼쪽 `Add` 버튼을 눌러서 Repository를 생성할 수 있습니다.

![bintray-main]

**메인 화면이 아닌 경우**

만약 메인화면이 아닌 상태라면 로그인 상태에서 오른쪽의 `Username`을 눌러서 `Your Profile`을 눌러서 페이지 이동하시면 됩니다.

![bintray-profile]

프로필 페이지에서도 Repository를 추가할 수 있는데 `Add New Repository`을 눌러 주시면 되겠습니다.

![bintray-profile-main]

**Repository 추가하기**

아래의 화면에서 몇 가지 옵션을 추가해주시면 됩니다. 여기에서 가장 중요한 건 `Name`입니다. aar을 배포하기 위한 저장소가 필요한데 이 저장소의 이름을 정의하는 것입니다. 여러 개의 maven Repository을 가져도 상관없으니 편하신 이름을 정의해주시면 되겠습니다.

![bintray-new-repository]

저는 Repository의 이름을 `sample`이라고 지정하였습니다. 보통? `maven`이라고 지정하기도 하는데 관리하기 편하신 이름을 정의하시면 되겠습니다.


**Type 지정**

저장소의 Type을 지정합니다. 많은 저장소가 제공되고 있는데 Android에서는 `maven` 저장소를 통해 배포되고 JCenter를 통해서 실제 aar 모듈을 사용하게 됩니다.

그래서 `Maven`을 선택하여 주시면 되겠습니다.

![bintray-new-repository-type]


**License/Description 지정**

License와 Description은 별도로 선택/작성해주지 않으셔도 괜찮습니다.

저는 `Apache-2.0`을 기본 License으로 지정하였고, 여러 개를 지정할 수도 있습니다.

![bintray-new-repository-licenses]


모두 완료되면 하단의 `Create` 버튼을 눌러서 완료하면 자동으로 Repository 페이지로 이동합니다.

![bintray-repository-created-success]

잠시 후 이동이 완료되면 기본 Repository가 생성되었습니다.

우선 웹에서 생성해주셔야 할 부분은 여기까지입니다.

![bintray-repository]

<br />

### 참고 사항

생성된 Repository를 접근하는 방법을 간단하게 설명하려고 합니다.

Repository를 접근하는 방식은 UI가 포함된 위의 이미지와 같은 접근 방법과 dl을 통한 접근이 가능합니다. dl로 접근하는 경우는 dependencies를 추가하는 package 이름의 접근 방법에 해당됩니다.

기본 Bintray에서는 다음과 같이 접근이 가능합니다.

https://bintray.com/Username/Repository-name/

dl 접근시에는 다음과 같습니다.

http://dl.bintray.com/Username/Repository-name/

**실제 접근 페이지는**

제가 만든 내용을 기준으로 접근한다면 다음과 같이 정의할 수 있습니다.

- 사용자명(회원가입 시) : taehwandev
- Repository 이름 : sample

그래서 UI 페이지 접근시에는

https://bintray.com/taehwandev/sample

dl은 아래와 같이 접근이 가능합니다.

http://dl.bintray.com/taehwandev/sample

이 URL이 중요한 이유는 Bintray로 배포되고, JCenter로 배포되기까지 걸리는 시간이 약 1일 이상 소요될 수 있습니다. Bintray에 배포된 aar이 접근이 잘되고, 사용할 수 있는 형태인지를 체크하기 위해서 `dl` 주소가 필요합니다.

**이 부분은 배포 이후 살펴보도록 하겠습니다.**


<br />

## Android Studio에서 Module(library) 생성하기

**모듈 추가에 대해서만 작성합니다. Github 배포 방법과 프로젝트 생성 방법은 제외합니다.**

maven을 통해 배포는 library 형태의 모듈에서만 가능합니다.

Android Studio에서 메뉴의 `File` > `New` -> `New Module`을 통해서 새로운 모듈을 생성하거나, 프로젝트 최상단 부분에서 `오른쪽 마우스`를 눌러서 다음과 같이 `New` > `Module` 버튼을 눌러서 새로운 모듈을 추가할 수 있습니다.

![as-module-create-01]

기본적인 모듈을 선택하는 화면입니다. 여기에서는 `Android Library`을 눌러 줍니다.

![as-module-create-02]

<br />

**모듈 이름 생성 시 주의 사항**

대부분의 라이브러리는 `appcompat-v7`식으로 `-`를 포함하고 있습니다.

**원래는 이런 식으로 하지 않아도 될 것 같은데 제가 `gradle` 지식이 부족하여 다음과 같이 하였습니다.**

모듈 이름을 작성할 경우 유의하여 생각하셔야 하는 부분으로 크게 2가지로 구분됩니다.

- 배포되는 라이브러리에 `-`를 포함하고 싶다
- 단순히 이름으로 정의하고 싶다

단순히 모듈 이름을 `base`라고 지어주면 상관없지만 `-`를 포함할 때가 문제입니다.

그래서 `Library name`을 정의하실 때 아래 그림과 같이 `-`를 포함하고 싶다면 처음부터 포함시켜주는게 좋습니다. 그렇지 않으면 모듈 이름은 `bintray-library`이지만 실제 빌드 되는 `aar`은 `bintraylibary`의 형태가 만들어지게 됩니다.

**원래는 모듈 이름을 이렇게 지어도 실제 `aar`의 파일 이름과 배포의 `aar` 등을 모두 별도로 설정 가능할 것 같지만 제가 아직 방법을 찾지 못하였습니다.**

![as-module-create-03]

마지막으로 Sample 화면을 선택하시면 되는데 저는 `Add No Activity`를 선택하여 시작하였습니다.

![as-module-create-04]

**Github share**

Github share는 다음과 같이 `VCS` > `Import into Version Control` > `Share project on GitHub` 메뉴를 통해서 진행 가능합니다.

![github-share]

<br />

Github share의 경우 조금 다를 수 있지만 주요 부분만 간단하게 설명하겠습니다.

아래와 같이 처음 사용하면 GitHub 설정을 위한 패스워드를 지정 또는 사용하게 됩니다. 처음이시면 비밀번호를 입력하는 창이 조금 다를 수 있지만 `해당 PW는 로컬에 저장`되는 PW입니다. 언제든 Reset... 할 수 있습니다.

![github-share-01]

진행 중 실제 Github의 로그인을 요구하게 됩니다. 저는 이미 로그인 상태라서 2차 패스워드를 요구하지만 로그인해주시면 됩니다.(아래 창은 2차 패스워드가 설정되어 있을 경우에만 뜹니다.)

![github-share-02]

Share를 하기 위한 프로젝트 설정입니다. `New repository name`은 생성된 프로젝트의 이름이고, Remote name은 기본값을 지정해주시면 됩니다.

![github-share-03]

업로드할 파일들을 선택이 기본으로 동작합니다. OK를 눌러주시면 자신의 GitHub 페이지에서 확인이 가능합니다.

![github-share-04]


<br />

## Bintray에 업로드를 위한 build.gradle 셋팅

Bintray을 사용하기 위해서는 다음의 모듈을 사용하게 됩니다.

- [Gradle Android Maven plugin](https://github.com/dcendents/android-maven-gradle-plugin)
- [Gradle Bintray plugin](https://github.com/bintray/gradle-bintray-plugin)
- [Gradle Bintray sample](https://github.com/bintray/bintray-examples)
  - 제가 작성한 샘플이며, 샘플에서는 gradle-bintray-plugin-examples/[android-maven-example](https://github.com/bintray/bintray-examples/tree/master/gradle-bintray-plugin-examples/android-maven-example)을 참고하시면 됩니다.
- [직접 작성한 Bintray upload sample](https://github.com/taehwandev/BintraySample/blob/master/bintray-library/build.gradle)


<br />

### dependencies 추가

[root Project의 build.gradle](https://github.com/taehwandev/BintraySample/blob/master/build.gradle)에 `dependencies`을 추가해주어야 합니다.

`buildscript`의 `dependencies`에 `Maven plugin`과 `Bintray plugin`을 추가해주세요.

먼저 [maven plugin](https://github.com/dcendents/android-maven-gradle-plugin)은 다음이 필요합니다.

```
buildscript {
    repositories {
        mavenCentral()
    }

    dependencies {
        classpath 'com.github.dcendents:android-maven-gradle-plugin:1.5'
    }
}

apply plugin: 'com.android.library'
apply plugin: 'com.github.dcendents.android-maven'
```

그리고 [bintray plugin](https://github.com/bintray/gradle-bintray-plugin)은 다음이 필요합니다.

```
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.jfrog.bintray.gradle:gradle-bintray-plugin:1.7'
    }
}
apply plugin: 'com.jfrog.bintray'
```

위에 두 개를 합치면 최종적으로 다음과 같습니다.

```
buildscript {
    repositories {
        mavenCentral() // 추가
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.2.0-beta3'

        // Bintray sample
        classpath 'com.github.dcendents:android-maven-gradle-plugin:1.5'
        classpath 'com.jfrog.bintray.gradle:gradle-bintray-plugin:1.7'
    }
}
```


<br />

### 모듈에 bintray 스크립트 구현

`bintray`을 추가하여 `bintrayUpload`를 진행할 수 있도록 준비합니다.

[Bintray 작성한 예제는 다음과 같습니다.](https://github.com/taehwandev/BintraySample/blob/master/bintray-library/build.gradle#L63)

`library`의 `build.gradle`에 `Bintray`는 아래와 같이 작성을 하게 됩니다.

주요 코드 부분을 설명하기 전에 전체 코드는 다음과 같습니다.

```
apply plugin: 'com.android.library'
apply plugin: 'com.github.dcendents.android-maven'
apply plugin: 'com.jfrog.bintray'


group = rootProject.ext.packageName
version = rootProject.ext.libVersionName

bintray {
    user = project.hasProperty('bintrayUser') ? project.property('bintrayUser') : System.getenv('BINTRAY_USER')
    key = project.hasProperty('bintrayApiKey') ? project.property('bintrayApiKey') : System.getenv('BINTRAY_API_KEY')

    configurations = ['archives'] // When uploading configuration files

    publish = true // [Default: false] Whether version should be auto published after an upload

    pkg {
        repo = repositoryName // Repository name (Created in the repository Bintray site)
        name = project.name // module name
        userOrg = user // An optional organization name when the repo belongs to one of the user's orgs
        desc = 'Bintray sample library' // Optional
        licenses = ['Apache-2.0']

        vcsUrl = rootProject.ext.defaultRepoUrl
        websiteUrl = rootProject.ext.defaultUrl
        // issueTrackerUrl = '' // Optional

        labels = ['aar', 'android', 'example'] // Optional
        publicDownloadNumbers = true

        githubRepo = 'taehwandev/BintraySample' // Optional Github repository
        githubReleaseNotesFile = 'CONTRIBUTING.md' // Optional Github readme file

        version {
            name = rootProject.ext.libVersionName // The name of your library project
            desc = 'Gradle Bintray sample' // Optional - Version-specific description'
            released  = new Date() // Optional - Date of the version release. 2 possible values: date in the format of 'yyyy-MM-dd'T'HH:mm:ss.SSSZZ' OR a java.util.Date instance
            vcsTag = rootProject.ext.libVersionName
        }
    }
}

/// android setting 일부 생략
android {
    // 생략
    defaultConfig {
        // 생략

        // versionName은 Bintray에 설정한 값과 동일해야 합니다.
        // versionName은 1.0.0 등의 숫자와 문자 조합이 가능합니다.
        versionName rootProject.ext.libVersionName

        // 생략
    }
    // 생략
}
```

<br />

### Group/Version 정의

`group`과 `version`을 각각 정의합니다.

`group`은 패키지 이름을 사용하시면 됩니다. `group`을 정의하시면 다음과 같이 `dependencies`의 `compile` 부분을 확인할 수 있습니다.

`compile 'group:moduleName:version name'`의 `dependencies` 추가 시에 사용되며 전체는 아래와 같을 수 있습니다.

제가 작성한 bintray 배포 명은 아래와 같습니다.

```
compile 'tech.thdev:bintray-library:0.0.1-alpha3'
```

그래서 `build.gradle`의 root 부분에 다음과 같이 `group`과 `version`을 정의하게 됩니다.

```
group = '패키지 이름(예) 저는 tech.thdev을 사용합니다.'
version = '1.0.0'
```

bintray 정의에 대해서 자세한 내용은 [API 문서](https://github.com/bintray/gradle-bintray-plugin#buildgradle)를 참고하시면 되겠습니다.

<br />

### bintray 정의 부분 살펴보기

`bintray`은 아래와 같이 시작하고 `apply`을 추가해주어야 합니다.

```
apply plugin: 'com.jfrog.bintray'

bintray {

}
```

`user`는 회원가입 시 작성한 `Username`을 사용하면 되고, `key`는 Bintray에서 제공하는 API Key가 되겠습니다. API key를 확인하기 위해서는 다음과 같이 `Your Profile`을 눌러줍니다.

![bintray-profile]

보이는 화면에서 `Edit` 버튼을 눌러줍니다.

![bintray-profile-edit]

많은 메뉴 중 `API Key`를 눌러서 열어주면 다음과 같이 `*`으로 처리된 키가 보이게 됩니다.

만약 새로 만들어야 한다면 빨간색의 `Revoke it`을 눌러서 삭제하고 다시 생성해주시면 되겠습니다.

**키는 공개되면 안 됩니다. 누군가 같은 키로 최신 버전을 배포할 수 있는 상황이 발생할 수 있습니다.**

`show 오른쪽의 네모 버튼`을 누르면 키값이 복사가 되게 됩니다. 이 키가 `key`에서 사용하는 키입니다.

![bintray-api-key]

원래는 `local.property` 파일에 `bintrayUser`와 `bintrayApiKey`를 정의하셔서 사용하시고, 해당 파일은 github에 공개되어서는 안되도록 처리해주시는게 가장 좋습니다.

```
user = project.hasProperty('bintrayUser') ? project.property('bintrayUser') : System.getenv('BINTRAY_USER')
key = project.hasProperty('bintrayApiKey') ? project.property('bintrayApiKey') : System.getenv('BINTRAY_API_KEY')
```

대략 다음과 같을 수 있습니다.

```
user = 'Username'
key = 'Bintray API Key'
```

<br />

- configuration

Android에서는 `configuration`을 `archives`로 설정해주시면 됩니다.

```
configurations = ['archives'] // When uploading configuration files
```

- publish

`aar` 파일을 `bintray`에서 자동으로 배포하게 하려면 `publish`을 true로 설정하시면 됩니다. 기본값은 false입니다.

```
publish = true // [Default: false] Whether version should be auto published after an upload
```

만약 false로 하신 경우에는 `7일 안에 배포를 해주셔야 하며, 그렇지 않으면 자동 파기`됩니다.

![bintray-upload-success]

- githubReleaseNotesFile

`githubReleaseNotesFile`을 설정하면 `Release Note`를 관리할 수 있습니다. README.md를 걸어주시거나 별도의 md 파일을 생성하여 Release note를 관리할 수 있습니다.

```
githubReleaseNotesFile = 'CONTRIBUTING.md' // Optional Github readme file
```

<br />

### pkg

그 외 주요 설정 중에 `pkg`라는 부분이 있습니다. `pkg`에서 reposioty(bintray에서 생성한 reposioty 이름), name, 설명, 라이선스, 웹 사이트 url 등을 설정하게 됩니다.

- repo

`repo`는 `Bintray 사이트에서 생성한 Repository`을 적어주시면 됩니다.

저는 sample이라고 하였기에 `sample`이 되게 됩니다.

```
repo = sample // Repository name (Created in the repository Bintray site)
```

정의하지 않았거나, 없는 `Repository` 이름을 추가하게 되면 다음과 같이 오류가 발생합니다.

`404 Not Found`로 표시되므로 위에서 생성한 `Repository` 이름을 사용하거나, 또는 새로 생성해주시면 되겠습니다.

```
Execution failed for task ':bintray-library:bintrayUpload'.
> Could not create package 'taehwandev/sample/bintray-library': HTTP/1.1 404 Not Found [message:Repo 'sample' was not found]
```

- name

Name은 Bintray 웹 사이트에서 확인할 수 있는 이름입니다. 이 이름도 임의로 지정해주셔도 좋겠지만 저는 `module`의 이름을 그대로 따르도록 `project.name`으로 정의하였습니다.

```
name = project.name // package name
```

Bintray 홈페이지에서는 아래와 같이 확인이 가능해지게 됩니다.(배포 완료 후)

![bintray-repository-create]

- licenses

라이센스 정보를 입력해주시면 됩니다. 웹에서 설정하였던 `Apache-2.0`으로 정의하였습니다. 역시 여러 개가 포함될 수 있습니다.

```
licenses = ['Apache-2.0']
```

- vcsUrl, websiteUrl, issueTrackerUrl

vcsUrl은 별도로 지정하지는 않으셔도 됩니다. 저는 Github에 올려두고 작업하였기에 vcsUrl을 작성하였습니다.

websiteUrl도 별도로 존재할 경우 해당 URL을 적어주시면 됩니다.

issueTrackerUrl 역시 별도로 존재하신다면 적어주시면 되겠습니다.

```
// your VCS URL (mandatory if the package doesn't exist yet and must be created, and if the package is an OSS package; optional otherwise)
vcsUrl = 'https://github.com/taehwandev/BintraySample.git'
websiteUrl = 'https://github.com/taehwandev/BintraySample'
issueTrackerUrl = '' // Optional
```

<br />

### version

`version` 정보를 명시하기 위한 카테고리입니다.

- `name`은 `libraryVersion` 이름을 그대로 사용하시면 되는데 저는 아래 예와 같습니다.
- `desc`는 쓰고 싶은 내용이 없으시다면 제외하셔도 됩니다.
- `released` 옵션이지만 released 날짜를 특별히 정의하고 싶으실 때 추가하시면 됩니다.
- `vcsTag`는 버전 컨트롤을 위한 태그 이름입니다. 저는 버전 이름을 그대로 사용하였습니다.

```
version {
    name = '0.0.1-alpha3' // The name of your library project
    desc = 'Gradle Bintray sample' // Optional - Version-specific description'
    released  = new Date() // Optional - Date of the version release. 2 possible values: date in the format of 'yyyy-MM-dd'T'HH:mm:ss.SSSZZ' OR a java.util.Date instance
    vcsTag = '0.0.1-alpha3'
}
```

여기까지가 주요 `bintray`의 설정에 해당됩니다. `bintrayUpload`를 진행하셔도 문제없이 동작하게 됩니다. `pkg.name`에 대한 정의만 잘 해주시면 별도의 aar 폴더가 만들어지지 않고, 하나의 폴더로 관리가 되게 됩니다.

저는 해당 `pkg.name`을 잘못 정의하여 별도의 폴더도 생성되었습니다. Sample이라 상관없지만 관리까지 생각하셔야 하므로 정의를 잘 해주시는 게 좋겠습니다.

예를 들면 다음과 같이 생성될 수 있습니다.

- 모듈명 `samplebase`
- pkg.name `Sample_Base`로 정의한 경우

![result-01]

이런 경우가 발생할 수 있으므로 `pkg.name`을 정의 잘하시는 게 좋습니다.


<br />

### 모듈에 install 추가

`aar`은 `bintray` 설정으로도 충분히 무리 없이 업로드 됩니다. 하지만 추가 정보를 원하시는 경우가 있을 수 있으니 `install`을 추가로 정의하는 부분을 설명하겠습니다.

전체 `install의 코드는` [install의 전문](https://github.com/taehwandev/BintraySample/blob/master/bintray-library/build.gradle#L12)에서 확인 가능하며, 아래와 같습니다.

```
install {
    repositories.mavenInstaller {
        pom.project {
            name 'Bintray sample library'
            description 'Is bintray sample'
            url rootProject.ext.defaultUrl
            inceptionYear '2016' // release date

            // gradle dependency
            // ex) compile 'tech.thdev:bintray-sample:0.0.1'
            packaging 'aar'
            groupId rootProject.ext.packageName // compile 'tech.thdev:
            artifactId rootProject.ext.libraryName // :bintray-sample:
            version rootProject.ext.libVersionName // :0.0.1-alpha

            licenses {
                license {
                    name 'The Apache Software License, Version 2.0'
                    url 'http://www.apache.org/licenses/LICENSE-2.0.txt'
                    distribution 'repo'
                }
            }

            scm { // Optional setting
                connection rootProject.ext.defaultRepoUrl
                developerConnection rootProject.ext.defaultRepoUrl
                url rootProject.ext.defaultUrl
            }

            developers { // Optional setting
                developer {
                    id 'taehwandev'
                    name 'taehwan'
                    email 'taehwan@thdev.tech'
                }
            }
        }
    }
}
```

주요 설정 부분을 각각 살펴보겠습니다.

<br />

- project 기본

`pom`의 `project` 정보를 각각 설정할 수 있습니다.

기본적인 name, description과 packaging 방법, groupId, license, scm(URL), developer 정보를 셋팅할 수 있습니다.

- `name` : 임의로 만들어주시면 됩니다. `제외하셔도 괜찮습니다.`
- `description` : 설명입니다. `제외하셔도 괜찮습니다.`
- `url` : 해당 기본 사이트 url을 사용하시면 됩니다. `제외하셔도 괜찮습니다.`
- `inceptionYear` : release 년도를 표시하게 되는데 `제외하셔도 괜찮습니다.`

```
pom.project {
    name 'Bintray sample library'
    description 'Is bintray sample'
    url rootProject.ext.defaultUrl
    inceptionYear '2016' // release date
```

<br />

- project dependency 이름 정의 부분

dependency의 이름을 정의를 하는 부분으로 잘 정의해주셔야 합니다.

dependency에 추가되는 모듈의 전체 이름이 정의됩니다. 원래는 여기에서 정의하지 않으면 bintray에서 정의한 `group`과 `version`, `모듈 이름`을 이용하여 생성되게 됩니다.

**그래서 별도로 지정하지 않으셔도 문제는 없습니다.**

그리고 값을 변경하는 부분이 많지 않을 거라고 생각됩니다. 여기에서 정의를 조금 다르게 하면 실제로 배포되는 **aar의 위치와 폴더 명이 조금 변경될 수 있습니다.**

```
// gradle dependency
// ex) compile 'tech.thdev:bintray-sample:0.0.1'
packaging 'aar'
groupId rootProject.ext.packageName // compile 'tech.thdev:
artifactId rootProject.ext.libraryName // :bintray-sample:
version rootProject.ext.libVersionName // :0.0.1-alpha
```

- packaging : `aar`로 정의합니다.
- groupId : gradle의 첫 번째 값에 해당하며 `tech.thdev`로 하였습니다.
- artifactId : gradle의 두 번째 값에 해당하며 `bintray-sample`에 해당합니다.
- version : 실제 `dependency`의 버전에 해당하며 `0.0.1-alpha`로 정의하였습니다.

이 부분이 조금 복잡할 수 있지만 잘 배포해주셔야 합니다.

이유는 `artifactId`이 변경되더라도 실제 `aar`은 아래와 같습니다.

```
compile 'tech.thdev:bintraylibrary:0.0.1-alpha3@aar'
```

하지만 실제 Bintray에 표현되는 값은 아래와 같습니다.

```
compile 'tech.thdev:bintray-library:0.0.1-alpha3@aar'
```

위치가 달라지고 파일 명도 달라져 문제가 생길 수 있으리라고 생각됩니다.

실제 배포에는 모르겠고 Bintray를 테스트하는 과정에서는 위와 같은 문제가 생겨서 `-`가 없는 파일을 호출하게 되는 경우가 있었습니다.(반대의 경우도 발생 가능)

그래서 `artifactId`을 별도 정의하기보다는 기본 값을 사용하는 편이 좋다고 생각됩니다.(`project.name`)

이 경우 폴더도 아래와 같이 구분이 일어날 수 있습니다.

![result-01]

<br />

- licenses

`licenses` 역시 별도로 정의하지 않아도 되겠지만 저는 아래와 같이 가능하며, 여러 개의 `license`를 포함할 수 있습니다.

```
licenses {
    license {
        name 'The Apache Software License, Version 2.0'
        url 'http://www.apache.org/licenses/LICENSE-2.0.txt'
        distribution 'repo'
    }
}
```

<br />

- scm

스키마를 정의합니다. 옵션이라서 별도로 해주지 않아도 되지만 `connection`과 `developerConnection`은 GitHub의 repo를 정의하였고, `url`은 GitHub의 메인 페이지를 정의하였습니다.

**모두 정의할 필요는 없고, 작성하고 싶으신 것만 하시면 됩니다.**

```
scm { // Optional setting
    connection rootProject.ext.defaultRepoUrl
    developerConnection rootProject.ext.defaultRepoUrl
    url rootProject.ext.defaultUrl
}
```

<br />

- developers

개발자의 정보를 간단하게 추가할 수 있습니다. 옵션이라서 정의하지 않으셔도 되겠지만 `id`, `name`, `email` 정도를 정의할 수 있습니다.

```
developers { // Optional setting
    developer {
        id 'taehwandev'
        name 'taehwan'
        email 'taehwan@thdev.tech'
    }
}
```


<br />

- 2016.09.02 JCenter 배포에 실패하여 추가하였습니다.

## JCenter 배포를 위한 jar/docs 포함하기

부 제목을 쓴다면 JCenter를 배포하기 위해서! 추가하기!

Bintray에 배포하는 것은 문제가 없었지만 다음을 포함하지 않고 배포했더니 Bintray 관리자가 JCenter에 배포를 해주지 않더군요.

제 확인하였더니 jar를 포함해달라고...

그래서 해당 내용을 추가합니다.

```
// JCenter publish
task sourcesJar(type: Jar) {
    from android.sourceSets.main.java.srcDirs
    classifier = 'sources'
}

// Optional docs
task javadoc(type: Javadoc) {
    source = android.sourceSets.main.java.srcDirs
    classpath += project.files(android.getBootClasspath().join(File.pathSeparator))
}

// Optional javadoc
task javadocJar(type: Jar, dependsOn: javadoc) {
    classifier = 'javadoc'
    from javadoc.destinationDir
}

artifacts {
    archives javadocJar // Optional
    archives sourcesJar
}

task findConventions << {
    println project.getConvention()
}
```

최소한 `sourcesJar`를 포함해주시면 됩니다. 그러면 아래와 같이 짧아지게 됩니다.

```
// JCenter publish
task sourcesJar(type: Jar) {
    from android.sourceSets.main.java.srcDirs
    classifier = 'sources'
}

artifacts {
    archives sourcesJar
}

task findConventions << {
    println project.getConvention()
}
```

최소한 위의 코드는 포함되어야 합니다. 그렇지 않으면 JCenter 배포를 해주지 않겠다고 합니다.

JCenter 배포하는 부분은 별도의 글로 남기겠습니다.


<br />

여기까지 진행하였으면, 기본 설정이 모두 끝났습니다. bintrayUpload를 진행하도록 하겠습니다.


<br />

## Bintray에 업로드

Bintray에 이제 업로드 준비가 끝났으니 업로드를 진행하면 됩니다.

`gradle 명령`을 통해서도 진행할 수 있지만 저는 명령이 아닌 `Gradle projects` 정의되어 있는 `install`과 `bintrayUpload`를 통해서 진행하였습니다.

`install`은 `Override`라서 별도로 눌러줄 필요 없이 `bintrayUpload`하는 과정에서 함께 진행되게 됩니다.(정의하지 않았으면 기본 `install`으로 동작하게 됩니다.)

- 2016.09.02 - 일부 내용 전달이 잘못되어 수정합니다.

Android Studio의 오른쪽에 위치한 `Gradle` 버튼을 눌러주세요.

아래 그림과 같이 `(root)`의 `Tasks` > `publishing` > `bintrayUpload` 버튼을 눌러주시면 `install` 포함 bintrayUpload를 진행하게 됩니다.

![bintrayUpload]

**만약 해당 모듈에서 진행한다면 `install`을 진행하고, `bintrayUpload`를 각각 눌러주셔야 업로드가 정상적으로 진행되게 됩니다.**

진행 중 다음과 같은 오류가 발생하신다면 Bintray 웹사이트에서 `reposioty` 생성을 하지 않으신 경우입니다. 확인하시고, 수정 또는 생성해주시면 됩니다.

```
Execution failed for task ':bintray-library:bintrayUpload'.
> Could not create package 'taehwandev/sample/bintray-library': HTTP/1.1 404 Not Found [message:Repo 'sample' was not found]
```

진행이 완료되면 다음과 같이 확인이 가능합니다.

![bintrayUpload-finish]

완료되었으므로 웹에서도 확인할 수 있습니다.

https://bintray.com/UserName/repositoryName 으로 접근 가능합니다.

생성한 `reposioty` 페이지에 접근하면 다음과 같이 추가된 모듈을 확인 가능합니다.

![result-02]

해당 페이지에 접근하면 `publishing` 상태를 확인 가능합니다.

![result-03]

`publishing`을 자동으로 설정하지 않았다면 아래와 같이 별도의 알림으로 표시되고, 해주지 않으면 7일 후에 파기됩니다.

![bintray-upload-success]


<br />

## JCenter에 동기화(JCenter에 직접 올라가는 것이 아니라서 별도로 버튼을 눌러주어야 합니다.)

JCenter는 자동으로 동기화되는 것은 아닙니다. 직접 눌러서 처리해주어야 하는데 최초에는 길게는 1일 이상 소요될 수 있습니다. JCenter 동기화를 위해서 해당 Project 페이지로 이동합니다.

![jcenter-sync-01]

이동하면 오른쪽 하단에 `Linked to`라는 부분이 있고, 오른쪽 끝에 `Add to JCenter` 버튼이 나타나게 됩니다.

![jcenter-sync-02]

이 버튼을 눌러서 JCenter 추가 요청을 진행하시면 됩니다.

메일을 통해서 등록되었다고 도착하게 된다고 하는데 저는 샘플이라서 아직 받지 못하였습니다.

![jcenter-sync-03]


<br />

## JCenter를 통해 배포되면(**배포 시 1일 이상 소요**) dependencies에 추가하여 사용 가능

JCenter를 통해서 배포되기 전에는 다음의 `dependencies` 추가하는 방법으로는 테스트가 불가능합니다. 그래서 몇 가지 추가를 해주어서 작업을 진행하게 됩니다.

Project `build.gradle`에 아래와 같이 `buildscript`를 추가해주게 됩니다.

`maven`으로 시작하고 url을 추가해주면 됩니다.

url은 위에서도 적었지만 다음과 같습니다.

http://dl.bintray.com/Username/repositoryName 으로 정의합니다.

```
buildscript {
    repositories {
        mavenCentral()
        jcenter()
        // jcenter pre-deployment testing code
        maven {
            url  "http://dl.bintray.com/taehwandev/sample/"
        }
    }
}
```

Test가 필요한 App에 다음과 같이 추가하시면 되는데 우선 홈페이지에서는 다음과 같이 확인이 됩니다.

![bintray-gradle-dependency]

아직은 위와 같이 사용이 불가능합니다. JCenter 배포가 완료되어야 위와 같이 사용이 가능하게 됩니다.

전체 풀 코드로 작성을 해주시게 되는데 끝에 `@aar`을 추가해주셔야 합니다.

```
compile 'tech.thdev:bintray-library:0.0.1-alpha7@aar'
```

실제 JCenter에 배포된 다음에는 `@aar`은 불필요한 코드이므로 아래와 같이 추가하시면 됩니다.

```
compile 'tech.thdev:bintray-library:0.0.1-alpha7'
```

테스트가 완료되면 아래와 같이 간단하게 확인이 가능합니다.

![android-result]


<br />

## 마무리

Bintray를 통해서 aar 배포하는 방법을 정리해보았습니다. 정리하기 전에는 약간 삽질을 많이 했었습니다. 결국 이렇게 정리해도 보시는 분은 또 삽질을 하셔야겠지만 조금이라도 도움이 되셨으면 하여 정리해보았습니다. 결국 저도 다시 보려고 작성하였습니다.

아마 이 예를 통해서 정식으로 라이브러리 형태의 모듈들을 배포해볼 생각입니다.

Bintray의 API key는 노출이 되었다고 생각되시면 언제든 새로운 키로 변경이 가능합니다.

그리고 이 글을 정리하면서 보았던 블로그들의 글이 많은데 gpg라는 부분을 설정을 모두 하도록 하고 있습니다. 제가 작업해보니 굳이 해주지 않아도 되긴 합니다.(mavenCentral로의 배포를 하지 않아서 일지도...)

그리고 제가 작성한 부분 중에 `install` 부분도 역시 별도의 정의를 해줄 필요는 없습니다. 몇 가지 설명을 추가하고 싶으신 경우에는 쓰셔야겠지만 굳이 필요한 부분은 아니었습니다.

어렵긴 하지만 배포하는데 도움이 되셨으면 합니다. 감사합니다.

- [Sample을 작성한 예제](https://github.com/taehwandev/BintraySample)


<br />

## 관련 글

- <a href="http://thdev.tech/androiddev/2016/09/01/Android-Bintray(JCenter)-Publish.html">Android Module을 Bintray(JCenter)에 배포하는 방법</a>
- <a href="http://thdev.tech/androiddev/2016/09/03/Android-Bintray(JCenter)-Publish-02.html">Android Module을 Bintray(JCenter)에 배포하기 삽질!...</a>


[bintray-distribution]:   /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintray-distribution.png
[bintray-service]:   /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintray-service.png
[bintray-signup-01]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintray-signup-01.png
[bintray-signup-03]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintray-signup-03.png

[bintray-profile]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintray-profile.png

[bintray-main]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintray-main.png
[bintray-profile-main]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintray-profile-main.png
[bintray-new-repository]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintray-new-repository.png
[bintray-new-repository-type]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintray-new-repository-type.png
[bintray-new-repository-licenses]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintray-new-repository-licenses.png
[bintray-repository]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintray-repository.png
[bintray-repository-created-success]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintray-repository-created-success.png

[as-module-create-01]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/as-module-create-01.png
[as-module-create-02]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/as-module-create-02.png
[as-module-create-03]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/as-module-create-03.png
[as-module-create-04]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/as-module-create-04.png

[github-share]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/github-share.png
[github-share-01]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/github-share-01.png
[github-share-02]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/github-share-02.png
[github-share-03]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/github-share-03.png
[github-share-04]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/github-share-04.png

[bintray-profile-edit]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintray-profile-edit.png
[bintray-api-key]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintray-api-key.png

[bintray-repository-create]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintray-repository-create.png

[result-01]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/result-01.png
[result-02]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/result-02.png
[result-03]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/result-03.png

[bintrayUpload]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintrayUpload.png
[bintrayUpload-finish]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintrayUpload-finish.png
[bintray-upload-success]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintray-upload-success.png

[jcenter-sync-01]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/jcenter-sync-01.png
[jcenter-sync-02]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/jcenter-sync-02.png
[jcenter-sync-03]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/jcenter-sync-03.png
[bintray-gradle-dependency]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintray-gradle-dependency.png
[android-result]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/android-result.png
