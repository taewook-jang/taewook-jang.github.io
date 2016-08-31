---
layout: post
title: Android Module을 Bintray(JCenter)에 배포하는 방법
categories: [AndroidDev]
tags: [Android Dev, Android, Bintray, JCenter]
fullview: false
comments: true
published: false
---

Eclipse에서 Android Studio로 넘어오면서 gradle을 사용하게 되었습니다.

별도의 lib를 다운로드 받아 추가할 필요 없이 build.gradle에 dependencies 추가만 해주면 Android Studio가 aar를 자동으로 받아 lib 사용을 할 수 있게 만들어줍니다.

<br />
## AAR 배포하기

그렇다면 AAR은 어떤식으로 배포할 수 있을까요?

aar을 배포하는 방법은 크게 3가지 정도가 있습니다.

- 개인 서버를 통해 maven 형태의 aar 배포서버 운영 및 활용(소스코드 공개가 힘든 경우)

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

위의 3가지는 모두 서버를 통해서 aar을 가지고 있도록 하고, dependencies 추가를 통해서 사용할 수 있습니다.


<br />

## 로컬에서 모듈 import 하여 사용하기

서버없이 로컬에서 모듈 형태로 import도 가능합니다.

Android Studio를 통해서 생성한 Module(library)을 다른 프로젝트에 import 하여 사용하는 방법도 가능합니다.

방법은 다음과 같습니다. Module(library)를 생성하고, 사용하려는 프로젝트의 build.gradle에 다음과 같이 추가합니다.

```
dependencies {
  compile project(":module name")
}
```

위와 같이 간단하게 처리도 가능합니다. 이 경우 역시 aar이 생성되고, 이 aar이 import되어 동작합니다.

**모듈이 빌드되고, 프로젝트가 빌드되어야 해서 빌드 시간이 더 걸릴 수 있습니다.**

해당 방법의 설명은 나중에 다시하도록 하고... bintray을 이용하여 aar 배포하는 방법을 설명하겠습니다.


<br />

## Bintray

JCenter는 [Bintray](https://bintray.com/)에서 운영 중인 Meven Repository입니다.

가장 유명하고, Google은 Android Studio에서 제공하는 support library를 JCenter를 통해서 배포하고 있습니다.

주요 고객사로 구글과 아마존, 넷플리스, MS, vmware 등이 있다고 합니다.

![bintray-distribution]

Bintray가 Maven Repository만을 운영하는것은 아닙니다.

![bintray-service]

<br />

## Bintray에 upload 하려면

Bintray를 사용하기 위해서는 다음의 과정이 필요합니다.

- [Bintray 회원가입](https://bintray.com/)(Github/Google, Twitter 또는 직접 회원가입)
- Bintray에서 Maven Repository 생성(미리 해두어도 괜찮고 나중에 하여도 됩니다.)
- Android Studio에서 Module(library) 생성하기
- Bintray에 업로드를 위한 build.gradle 셋팅
- Bintray 사용을 위한 API 키 확인([Profile](https://bintray.com/profile/edit)을 통해 확인 가능)
- Bintray에 업로드
- JCenter에 동기화(JCenter에 직접 올라가는것이 아니라서 별도로 버튼을 눌러주어야 합니다.)
- JCenter를 통해 배포되면(**첫 배포시 1일 이상 소요**) dependencies에 추가하여 사용 가능

**ETC**

그외 mavenCentral에도 배포가 가능하다고 합니다.

추가로 작업을 하고 한번에 2곳에 배포가 가능한 형태인데 **이 글에서는 다루지 않습니다.**


<br />

## Bintray 회원가입

[Bintray](https://bintray.com/) 홈페이지에서 간단하게 회원가입할 수 있습니다.

Bintray는 유료 버전과 무료 버전으로 구분되어 있습니다. 유료의 경우 private 형태도 제공하고 있어서 private 형태를 원하신다면 한달에 최소 $45, 최대 $150을 납부하시면 됩니다. 자세한 내용은 [Bintray pricing](https://bintray.com/account/pricing?tab=account&type=pricing)에서 보시면 됩니다.

[Bintray](https://bintray.com/)에서 Sign In 버튼을 눌러주면 아래와 같이 확인이 가능합니다.

간단한 회원가입 절차를 진행하시면 됩니다.

![bintray-signup-01]

저는 Github를 통해서 진행하였고 간단하게 승인을 하였습니다.

그리고 몇가지 추가사항만 확인해주시면 됩니다.

![bintray-signup-03]


<br />

## Bintray Repository 생성하기

아래와 같은 화면이 보인다면 `Owned Repository`에 `Add` 버튼을 눌러서 추가할 수 있습니다.

![bintray-main]

만약 위와 같이 보이지 않으면 `Your Profile`을 눌러 이동합니다.

![bintray-profile]

다음과 같은 화면을 찾아서 `Add new Repository` 버튼을 눌러주시면 됩니다.(첫 사진의 Add와 같습니다.)

![bintray-profile-main]

다음과 같은 화면에서 사용할 Repository 이름과 Repository의 Type, 라이선스 정보(옵션)를 선택합니다.

![bintray-new-repository]

Repository 이름은 `maven` 정도로 하셔도 괜찮고, 자신이 원하는 이름으로 해주시면 됩니다.

maven을 여러개 관리하고 싶으시다면 저처럼 다른이름으로 하셔도 괜찮고, 그렇지 않으시면 maven으로 하시면 됩니다.

**Type 지정**

type을 지정해주시면 됩니다. Android에서 사용할 것이므로 `maven`으로 선택하시면 되겠습니다.

![bintray-new-repository-type]

**License 지정**

License와 Description은 별도로 선택/작성해주지 않으셔도 괜찮습니다.

해당 부분은 모두 Optional이므로 따로 하지 않으셔도 괜찮지만 저는 아래와 같이 `Apache-2.0`으로 선택하였습니다.

![bintray-new-repository-licenses]


모두 완료되면 하단의 `Create` 버튼을 눌러서 완료하면 자동으로 Repository 페이지로 이동합니다.

이동한 페이지에는 다음과 같은 화면이 보여집니다.

![bintray-repository]

**참고**

Repository를 접근하는 방식은 UI가 포함된 Bintray 홈페이지에서의 접근과 dl을 통한 접근이 가능합니다.

Bintray에서는 다음과 같이 접근이 가능합니다.

https://bintray.com/Username/Repository-name/

dl을 통해 접근시에는 다음과 같습니다.

http://dl.bintray.com/Username/Repository-name/

저는 아래와 같이 생성하였습니다.

- 사용자명(회원가입시) : taehwandev
- Repository 이름 : sample

그래서 합쳐서 아래와 같은 주소를 가지게 됩니다.

다음의 주소는 추후 JCenter에 배포되기 전에 테스트를 진행할때 사용하게 되는 URL 입니다.(JCenter는 최대 1일 이상의 배포 시간이 소요되며, 최초에는 이메일을 통해 확인이 가능합니다.)

http://dl.bintray.com/taehwandev/sample/


<br />

## Android Studio에서 Module(library) 생성하기

**새로운 프로젝트 생성 부분은 제외하고 모듈을 추가하는 방법을 설명합니다.**

- 모듈 생성
- Github에 Share 하기(대부분의 예제가 Github 관련 예제들이라서 우선 저도 그렇게 하였습니다.)

maven 배포는 library에서만 가능합니다. 그래서 library용 Module을 생성합니다.

Android Studio에서 메뉴의 File > New -> New Module 을 눌러주거나

오른쪽 마우스를 눌러서 다음과 같이 New > Module 버튼을 눌러서 새로운 모듈을 추가할 수 있습니다.

![as-module-create-01]

기본적인 모듈을 선택하는 방법입니다. 여기에서는 `Android Library`을 눌러 줍니다.

![as-module-create-02]

모듈 이름을 생성할때 주의해 주셔야 하는 사항이 있습니다.

- 배포되는 라이브러리에 `-`를 포함하고 싶다
- 단순히 이름으로 정의하고 싶다

여기에서는 `-`를 포함할때 주의해주셔야 합니다.

대부분의 라이브러리는 `appcompat-v7`식으로 `-`를 포함하고 있습니다.

그래서 모듈 이름을 생성하실때 다음과 같이 해주시면 `-`를 포함할 수 있습니다.

**참고: aar이 압축될때 기본적으로 모듈 이름을 그대로 사용합니다. 별도로 정의해서 할 수 있지만 .. 저도 아직 방법을 잘...**

aar이름이 `appcompat-v7`와 같은 형태로 만들고 싶으시다면 다음과 같이 해주세요

아래 그림과 같이 `Sample-Libary`의 형태로 작성해주시면 됩니다.

(아래에서 한번 더 이야기 하겠지만 `-` 때문에 폴더가 2개가(aar을 포함하는 폴더, xml(설정파일)을 포함하는 폴더)) 생성되기도 합니다.)

![as-module-create-03]

마지막으로 샘플 화면을 선택하시면 됩니다. 저는 단순하게 Bintray를 통한 배포를 해보기 위함이라서 empty로 처리하였습니다.

![as-module-create-04]

참고로 Github share하는 방법은 다음의 메뉴를 이용하여 진행합니다.

![github-share]


<br />

## Bintray에 업로드를 위한 build.gradle 셋팅

Bintray을 사용하기 위해서는 다음의 모듈을 사용하게 됩니다.

- [Gradle Android Maven plugin](https://github.com/dcendents/android-maven-gradle-plugin)
- [Gradle Bintray plugin](https://github.com/bintray/gradle-bintray-plugin)
- [Gradle Bintray sample](https://github.com/bintray/bintray-examples)
  - 샘플에서는 gradle-bintray-plugin-examples/android-maven-example을 참고하시면 됩니다.
  - [android-maven-example](https://github.com/bintray/bintray-examples/tree/master/gradle-bintray-plugin-examples/android-maven-example)
- [직접 작성한 Bintray upload sample](https://github.com/taehwandev/BintraySample/blob/master/bintray-library/build.gradle)

<br />
### dependencies 추가

[root Project의 build.gradle](https://github.com/taehwandev/BintraySample/blob/master/build.gradle)에 dependencies을 추가해주어야 합니다.

buildscript의 dependencies에 Maven plugin과 Bintray plugin을 추가해주세요.

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

위에 두개를 합치면 최종적으로 다음과 같습니다.

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

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}
```

<br />

### 모듈에 bintray 추가

bintray을 추가하여 bintrayUpload를 진행할 수 있도록 준비합니다.

[Bintray 작성한 예제는 다음과 같습니다.](https://github.com/taehwandev/BintraySample/blob/master/bintray-library/build.gradle#L63)

library의 build.gradle에 Bintray는 아래와 같이 작성을 하게 됩니다.

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

//    publications = ['mavenJava']  // When uploading Maven-based publication files
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

group과 version을 추가해주게 됩니다.

group은 패키지 이름을 사용하시면 되고, group은 다음과 같이 사용됩니다.

`compile 'group:apk name:version name'`의 dependencies 추가시에 사용되며 전체는 아래와 같을 수 있습니다.

제가 작성한 bintray 배포명은 아래와 같습니다.

```
compile 'tech.thdev:bintray-library:0.0.1-alpha3'
```

그래서 group은 패키지 이름으로 설정해주시면 됩니다.

```
group = '패키지 이름(예) 저는 tech.thdev을 사용합니다.'
version = '1.0.0'
```

bintray 정의는 [API 문서](https://github.com/bintray/gradle-bintray-plugin#buildgradle)에 잘 나와있습니다.

### bintray 정의 부분 살펴보기

bintray은 아래와 같이 시작하고 apply을 추가해주어야 합니다.

```
apply plugin: 'com.jfrog.bintray'

bintray {

}
```

user는 Username을 사용하면되고, key는 Profile에서 확인할 수 있습니다.

![bintray-profile]

화면에서 `Edit` 버튼을 눌러줍니다.

![bintray-profile-edit]

많은 메뉴 중 `API Key`를 눌러서 열어줍니다.

아래와 같이 가려진 상태로 표시됩니다. 빨간색의 `Revoke it`을 눌러주면 현재 키를 삭제하고 다시 생성할 수 있습니다.

**key는 공개되면 안되겠죠?.. 키만 있으면 덮어쓰기도 가능하니.. 주의하셔야 합니다.**

**show 오른쪽의 버튼을** 눌러주시면 복사가 됩니다.

![bintray-api-key]


복사한 키를 `key`에 붙여 넣어주시면 됩니다.

또는 별도로 `local.properties`에 bintrayUser와 bintrayApiKey를 각각 담아주셔도 됩니다.(**이 경우 local.properties는 github 등에 커밋하지 않아야 합니다.**)

다음은 publications을 설정할 수 있습니다. 해당 부분은 저도 정확하게 아는 부분이 아니라서 자료를 찾아보시기 바랍니다.
```
publications = ['mavenJava']  // When uploading Maven-based publication files
```

Android에서는 configuration을 `archives`로 설정해주시면 됩니다.

```
configurations = ['archives'] // When uploading configuration files
```

aar 파일을 bintray에 바로 업로드 하려는 경우에는 publish을 true로 설정하시면 됩니다.

설정하지 않으면 false이며, **이 경우 website에서 publish을 하거나, 6일 후 자동으로 publish을 처리하게 됩니다.**

```
publish = true // [Default: false] Whether version should be auto published after an upload
```

### pkg

그외 주요 설정중에 pkg라는 부분이 있습니다. pkg에서 reposioty(bintray에서 생성한 reposioty 이름), name, 설명, 라이선스, 웹 사이트 url 등을 설정하게 됩니다.

repo는 Bintray 사이트에서 생성한 Repository을 적어주시면 됩니다.

저는 sample이라고 하였기에 `sample`이 되게 됩니다.

```
repo = sample // Repository name (Created in the repository Bintray site)
```

정의하지 않으면 업로드 중 오류가 자동으로 발생합니다.

`404`로 떨어지니 이 때는 위에서 했던 repository을 다시 생성해주시면 됩니다.

```
Execution failed for task ':bintray-library:bintrayUpload'.
> Could not create package 'taehwandev/sample/bintray-library': HTTP/1.1 404 Not Found [message:Repo 'sample' was not found]
```

Bintray에서 확인할 수 있는 이름입니다.

Bintray에 업로드가 완료되면 표시되는 이름으로 저는 `project.name`을 사용하였습니다.

```
name = project.name // package name
```

다음과 같이 확인이 가능합니다.

![bintray-repository-create]

라이센스 정보를 입력해주시면 됩니다. 웹에서 했던것과 같아서 전 Apache-2.0으로 설정하였지만 설정하지 않으셔도 됩니다.

```
licenses = ['Apache-2.0']
```

vcsUrl은 기본값으로 추가해야 하는데 대부분 Github 관련 예제들이라서 Github의 repository url을 적용합니다.

저는 올린 샘플이 다음과 같습니다. 저는 Github에 올려두고 진행하였기에 다음과 같이 `vcsUrl, websiteUrl`을 작성하였습니다. `vcsUrl, websiteUrl, issueTrackerUrl`은 별도로 지정해주지 않으셔도 됩니다.

```
// your VCS URL (mandatory if the package doesn't exist yet and must be created, and if the package is an OSS package; optional otherwise)
vcsUrl = 'https://github.com/taehwandev/BintraySample.git'
websiteUrl = 'https://github.com/taehwandev/BintraySample'
issueTrackerUrl = '' // Optional
```

### version

version 정보를 명시하기 위한 카테고리입니다.

- name은 libraryVersion 이름을 그대로 사용하시면 되겠습니다.
- desc는 별도로 지정하지 않으셔도 됩니다.
- released 옵션이지만 날짜를 자동으로 지정하게 하고 싶으시다면 아래와 같이 해주시면 됩니다.
- vcsTag는 버전 컨트롤을 위한 태그 이름입니다. 저는 버전 이름을 그대로 사용하였습니다.

```
version {
    name = '0.0.1-alpha3' // The name of your library project
    desc = 'Gradle Bintray sample' // Optional - Version-specific description'
    released  = new Date() // Optional - Date of the version release. 2 possible values: date in the format of 'yyyy-MM-dd'T'HH:mm:ss.SSSZZ' OR a java.util.Date instance
    vcsTag = '0.0.1-alpha3'
}
```

여기까지가 주요 bintray의 설정에 해당됩니다. 여기까지만 진행하시고 bintrayUpload를 하셔도 동작하지만 저는 모듈 install을 추가하였습니다. aar에 추가 정보를 포함할 수 있습니다.


<br />

### 모듈에 install 추가

aar은 bintray의 설정으로 만으로도 업로드가 되어야 하고, 추가 정보를 원하신다면 install을 진행해주시면 됩니다.

저는 aar에 추가 설정을 위해서 install을 추가하였습니다.

주의하실 점은 artifactId을 여기서 별도로 지정할 수 있는데 해당 이름은 module의 이름을 사용하지 않고, 다른 이름을 사용하여 배포하면 폴더가 2개가 생성되게 됩니다. 해당 부분을 아래 그림과 함께 추가 설명하도록 하겠습니다.

[install의 전문](https://github.com/taehwandev/BintraySample/blob/master/bintray-library/build.gradle#L12)은 아래와 같습니다.

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

pom의 project 정보를 각각 설정할 수 있습니다.

기본적인 name, description과 packaging 방법, groupId, license, scm(URL), developer 정보를 셋팅할 수 있습니다.

- name : 임의로 만들어주시면 됩니다. 제외하셔도 괜찮습니다.
- description : 설명입니다. 제외하셔도 괜찮습니다.
- url : 사이트 url을 사용하시면 됩니다. 제외하셔도 괜찮습니다.
- inceptionYear : release 년도를 표시하게 되는데 제외하셔도 괜찮습니다.

```
pom.project {
    name 'Bintray sample library'
    description 'Is bintray sample'
    url rootProject.ext.defaultUrl
    inceptionYear '2016' // release date
```

gradle의 파일명을 정할 수 있습니다. 여기서 조금 다르게 설정하시면 폴더가 구분되는 것을 볼 수 있습니다.

우선 packaging은 aar로 설정합니다. groupId는 `packageName`을 설정하게 됩니다.

대략 다음의 설정 부분이 아래의 dependency에 추가되는 내용과 관계가 있습니다.

설정해주지 않으시면 기본 값으로 처리가됩니다.(group, version, 모듈 명을 이용하여 조합합니다. 그래서 설정하지 않으셔도 같습니다.)

```
compile 'tech.thdev:bintray-library:0.0.1-alpha3'
```

위와 같습니다. 순서대로 packaging은 aar인데 JCenter를 통해서 배포되면 @aar이 자동으로 누락됩니다.

- groupId는 첫번째 `tech.thdev`으로 `:`전까지의 첫번째 이름에 해당됩니다. 변경해주셔도 상관 없습니다.
- artifactId는 실제 이름이 됩니다. 오른쪽 `:` 다음부터 `:`의 끝까지의 이름입니다.
  - 여기에서 다르게 설정을 하게되면 예를 들여 아래와 같이 `bbb`라고 하면 모듈 이름과 달라지게됩니다. 이 경우 다음과 같은 결과물이 만들어집니다.

aar 폴더가 구분되어 집니다. 아래 이미지처럼 `_` 포함과 그렇지 않은 `모듈명`이 생성되게 됩니다.

그럼 관리자체도 달라지고, 실제로 JCenter에 배포되었을때 정상적으로 동작할지는 아직 확인이 안되었습니다.(JCenter 배포가 아직 안되었네요...) 그리고 aar의 위치와 maven의 배포시 포함되는 xml 정의 파일의 폴더 위치가 서로 상의합니다.

![result-01]

그래서 해당 `artifactId`는 모듈 이름을 그대로 사용하는게 좋습니다.

좀 더 예를 들면 다음과 같습니다.

```
// 오류 발생
compile 'tech.thdev:bintray-library:0.0.1-alpha3@aar'

// 오류 발생하지 않음
compile 'tech.thdev:bintraylibrary:0.0.1-alpha3@aar'
```

이라고 배포가되었지만 모듈명에는 `-`가 포함되어 있지 않으므로 `-`를 제외하고 추가해주어야 정상 동작합니다.

- version은 libVersionName을 그대로 가져오시면 됩니다.

```
// gradle dependency
// ex) compile 'tech.thdev:bintray-sample:0.0.1'
packaging 'aar'
groupId rootProject.ext.packageName // compile 'tech.thdev:
artifactId 'bbb' // :bintray-sample:
version rootProject.ext.libVersionName // :0.0.1-alpha
```

위의 부분은 사실 별도로 추가해주실 필요는 없습니다. 잘못 설정하면 파일명이 변경되어 제가 했던것 처럼 원치 않는 결과가 나올 수 있으므로 추가하지 않으시는 편이 좋습니다.(아니면 그대로 ... 설정)

licenses 역시 별도로 정의하지 않아도 되겠지만 저는 아래와 같이 해주었습니다.

licenses라서 여러개의 license가 포함 가능합니다.

```
licenses {
    license {
        name 'The Apache Software License, Version 2.0'
        url 'http://www.apache.org/licenses/LICENSE-2.0.txt'
        distribution 'repo'
    }
}
```

스키마를 정의합니다. 음 역시 해주지 않아도 됩니다. url 정보와 repo 정보를 포함해주시면 됩니다.

**각각도 역시 옵션이라서 추가하고 싶으신 경우만 해주시면 됩니다.**

```
scm { // Optional setting
    connection rootProject.ext.defaultRepoUrl
    developerConnection rootProject.ext.defaultRepoUrl
    url rootProject.ext.defaultUrl
}
```

개발자 정보를 추가할 수 있습니다. 역시 여러개를 정의할 수 있으며, id, name, email이 기본이 됩니다.

```
developers { // Optional setting
    developer {
        id 'taehwandev'
        name 'taehwan'
        email 'taehwan@thdev.tech'
    }
}
```

이제 기본 설정이 끝났으니 bintrayUpload을 진행하면 되겠습니다.


<br />

## Bintray에 업로드

Bintray에 이제 업로드 준비가 끝났으니 업로드를 진행하면 됩니다.

gradle 명령을 통해서도 진행할 수 있지만 저는 명령이 아닌 Gradle projects 정의되어 있는 install과 bintrayUpload를 통해서 진행하겠습니다.

install은 Override의 성격이라서 별도로 install 해줄 필요는 없습니다.

오른쪽 메뉴의 `Gradle`을 눌러주고, 자신이 생성한 `module`을 선택해줍니다.

아래와 같이 `bintray-library`에서 `Tasks` > `publishing` > `bintrayUpload` 버튼을 눌러만 주시면 됩니다.

![bintrayUpload]

진행이 완료되면 다음과 같이 확인이 가능합니다.

![bintrayUpload-finish]

완료되었으므로 웹에서도 확인할 수 있습니다.

https://bintray.com/UserName/repositoryName 으로 접근 가능합니다.

생성한 reposioty 페이지에 접근하면 다음과 같이 추가된 모듈을 확인 가능합니다.

![result-02]

해당 페이지에 접근하면 publishing 상태를 확인할 수 있습니다.

publishing을 하지 않은 상태라면 별도의 알림으로 표시되고, 해주지 않으면 6일 후에 자동으로 publishing을 하게됩니다.

![result-03]


<br />

## JCenter에 동기화(JCenter에 직접 올라가는것이 아니라서 별도로 버튼을 눌러주어야 합니다.)


<br />

## JCenter를 통해 배포되면(**첫 배포시 1일 이상 소요**) dependencies에 추가하여 사용 가능



<br />

## 마무리


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

[as-module-create-01]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/as-module-create-01.png
[as-module-create-02]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/as-module-create-02.png
[as-module-create-03]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/as-module-create-03.png
[as-module-create-04]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/as-module-create-04.png
[github-share]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/github-share.png

[bintray-profile-edit]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintray-profile-edit.png
[bintray-api-key]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintray-api-key.png

[bintray-repository-create]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintray-repository-create.png

[result-01]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/result-01.png
[result-02]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/result-02.png
[result-03]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/result-03.png

[bintrayUpload]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintrayUpload.png
[bintrayUpload-finish]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/bintrayUpload-finish.png
