---
layout: post
title: Android Module을 Bintray(JCenter)에 배포하기 삽질!...
categories: [AndroidDev]
tags: [Android Dev, Android, Bintray, JCenter]
fullview: false
comments: true
published: false
---

Bintray에 올리고 JCenter에 배포하기 위해서 삽질기를 추가로 작성합니다.

.jar을 추가해주지 않아도 배포가 잘 되는 경우가 있다는데 우선 전 그렇지 않아서... 다음의 글을 작성합니다.

우선 JCenter에 배포 신청을 하였더니 다음의 메일이 도착하였습니다.

> Hi,

> JCenter hosts java applications that follow maven convention. In addition to the binary jar file, your version should include a .pom file, a sources jar, and optionally a javadoc jar.
Your files should be under a maven path layout. (See https://bintray.com/docs/usermanual/uploads/uploads_includingyourpackagesinjcenter.html)

> Once those files are added, we'll be glad to include your package in JCenter

> Regards,
> JFrog Support

그래서 다시 질문을 하였죠. 일단 `.jar`가 있어야 하는진 몰랐기에 다른 부분을 확인해달라고 하였습니다.

파일의 위치나, 생성된 것들... 그래서 다음의 메일이 왔습니다.

>Hi,

>Thank you for the response. Your request is close to being approved, but we need you to upload a sources jar file before we can approve. Please let us know if you have any further questions.

>Regards,
>Bintray support

결국 `.jar`을 추가해주면 배포해주겠다는 내용이었습니다.

흑.. 수많은 예제가 있었지만 `.jar`가 포함되어야 할지는 생각하지도 않았는데 결국 추가해달라고 합니다.

그래서 추가를 하기 위해서 다음을 추가하였습니다.(이미 이전 글에 작성되어 있습니다.)

- <a href="androiddev/2016/09/01/Android-Bintray(JCenter)-Publish.html">Android Module을 Bintray(JCenter)에 배포하는 방법</a>

아래와 같이 최소한의 코드로 jar을 추가할 수 있고, docs를 추가로 사용할 수 있습니다.

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

- [sourcesJar 전체 코드](https://github.com/taehwandev/BintraySample/blob/master/bintray-library/build.gradle#L133)

그래서 아래와 같이 업로드됨을 확인할 수 있습니다.

https://dl.bintray.com/UserName/RepositoryName/으로 접근하여 아래 사진과 같이 확인 가능합니다.

![result-01]

결론적으로 해당 버전의 폴더에 다음을 담게 됩니다.

- .aar
- .pom
- .jar

.pom은 xml의 형태로 아래와 같은 내용을 가지게 됩니다.

- `install` 부분에 추가 내용을 작성하였으면 아래와 같이 좀 길게 나오게 됩니다.

```xml
<project>
<modelVersion>4.0.0</modelVersion>
<groupId>tech.thdev</groupId>
<artifactId>bintray-library</artifactId>
<version>0.0.1-beta</version>
<packaging>aar</packaging>
<name>bintray-library</name>
<description>Is bintray sample</description>
<url>https://github.com/taehwandev/BintraySample</url>
<inceptionYear>2016</inceptionYear>
<licenses>
<license>
<name>The Apache Software License, Version 2.0</name>
<url>http://www.apache.org/licenses/LICENSE-2.0.txt</url>
<distribution>repo</distribution>
</license>
</licenses>
<developers>
<developer>
<id>taehwandev</id>
<name>taehwan</name>
<email>taehwan@thdev.tech</email>
</developer>
</developers>
<scm>
<connection>https://github.com/taehwandev/BintraySample.git</connection>
<developerConnection>https://github.com/taehwandev/BintraySample.git</developerConnection>
<url>https://github.com/taehwandev/BintraySample</url>
</scm>
<dependencies>
<dependency>
<groupId>com.android.support</groupId>
<artifactId>appcompat-v7</artifactId>
<version>24.2.0</version>
<scope>compile</scope>
</dependency>
</dependencies>
</project>
```

해당 내용에는 `dependency` 내용도 포함됩니다.

**올릴 때는 항상 버전을 올려주셔야 합니다.**


<br />

## JCenter 배포시

JCenter에 배포는 Bintray의 업무시간에 진행됩니다. 직원이 확인하고 배포를 하는 시스템입니다.

그렇기에 가능하면 미국의 현지 업무시간과 맞추는게 가장 빠르게 배포될 수 있습니다.


<br />

## JCenter 배포 완료

배포가 완료되면 아래와 같이 확인이 가능합니다.




<br />

## 마무리

JCenter와 Bintray에 배포되는 내용이 달라서 처음엔 `.jar`는 당연히 사용하지 않으니 빼고 배포하였습니다.

하지만 `.jar`가 제공되지 않으면 배포를 해주지 않는다는 메시지를 받고, 추가하게 되었습니다.(근데 그냥 `aar`만 올라가는 경우도 있습니다... 아마 `library` 버전에 따라 다른 듯)

이전 글을 잘 활용하여 배포에 무리 없으셨으면 합니다.

- <a href="androiddev/2016/09/01/Android-Bintray(JCenter)-Publish.html">Android Module을 Bintray(JCenter)에 배포하는 방법</a>

그리고 Jake Wharton이 배포시에는 항상 포함한다는 `artifacts.gradle`의 링크입니다. 다음에 배포시에는 참고하여 작성해봐야겠습니다.

- [Jake Wharton - artifacts.gradle](https://github.com/ReactiveX/RxAndroid/blob/1.x/gradle/artifacts.gradle)

감사합니다.


[result-01]:  /images/2016/2016-09-03-Android-Bintray(JCenter)-Publish-02/result_01.png

[jcenter-sync-03]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/jcenter-sync-03.png
