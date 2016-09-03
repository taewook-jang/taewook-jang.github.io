---
layout: post
title: Android Module을 Bintray(JCenter)에 배포하기 삽질!...
categories: [AndroidDev]
tags: [Android Dev, Android, Bintray, JCenter]
fullview: false
comments: true
published: true
---

Bintray에 올리고 JCenter에 배포하기 위해서 삽질기를 추가로 작성합니다.


<br />

## 관련 글

- <a href="http://thdev.tech/androiddev/2016/09/01/Android-Bintray(JCenter)-Publish.html">Android Module을 Bintray(JCenter)에 배포하는 방법</a>
- <a href="http://thdev.tech/androiddev/2016/09/03/Android-Bintray(JCenter)-Publish-02.html">Android Module을 Bintray(JCenter)에 배포하기 삽질!...</a>

`.jar`을 포함하지 않고 Bintray에 올리고, JCenter에 동기화 요청을 하였었는데 실패하였습니다.


다음과 같은 메일을 보내주었습니다.

> Hi,

> JCenter hosts java applications that follow maven convention. In addition to the binary jar file, your version should include a .pom file, a sources jar, and optionally a javadoc jar.
Your files should be under a maven path layout. (See https://bintray.com/docs/usermanual/uploads/uploads_includingyourpackagesinjcenter.html)

> Once those files are added, we'll be glad to include your package in JCenter

> Regards,
> JFrog Support

그래서 다시 질문을 하였고, `.jar`에 대해서는 생각하지 않았습니다. 유니크한 이름을 포함해야 합니다. 이는 자신의 개인 도메인을 통해서 해결할 수 있는 문제입니다.

추가 사항에 대해서 질문을 하였고, 다음의 메일을 받았습니다.

>Hi,

>Thank you for the response. Your request is close to being approved, but we need you to upload a sources jar file before we can approve. Please let us know if you have any further questions.

>Regards,
>Bintray support

다시 메일이 왔는데 `.jar`을 포함하면 JCenter에 배포해주겠다는 내용입니다. JCenter 배포를 위해서는 최소한 다음의 파일을 필요로 한다고 합니다.

- `.jar` : sources.jar
- `.pom` : 설정 정보를 가집니다.
- `.aar` : Android Studio에서 제공하며, UI 등을 포함하는 압축

제가 처음에 올렸던 것은 `.jar`을 포함하지 않고 있었습니다. 그래서 배포를 해주지 않았던 것이죠.

이전에 작성하였던 글에 `.jar`을 포함하는 방법을 추가로 담아두었습니다.

- <a href="androiddev/2016/09/01/Android-Bintray(JCenter)-Publish.html">Android Module을 Bintray(JCenter)에 배포하는 방법</a>

아래와 같이 `.jar`을 포함할 수 있습니다.

Jake Wharton의 배포에는 항상 `.jar`을 포함하고 있는 `artifacts.gradle`을 볼 수 있다고 합니다. 다음이 좀 더 명확한 maven 배포 방법이 될 수 있겠습니다.

- [Jake Wharton - artifacts.gradle](https://github.com/ReactiveX/RxAndroid/blob/1.x/gradle/artifacts.gradle)

아래 코드는 제가 사용한 방법입니다. 해당 부분의 전체 코드는 [build.gradle](https://github.com/taehwandev/BintraySample/blob/master/bintray-library/build.gradle#L133)을 참고하시면 되겠습니다.

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

위의 설정을 추가하면 아래와 같이 `.jar`, `.aar`, `.pom`을 확인할 수 있습니다.

https://dl.bintray.com/UserName/RepositoryName/으로 접근하여 아래 사진과 같이 확인 가능합니다.

![result-01]

`.pom`은 아래와 같은 내용을 가지게 되는데 저는 `install`에 추가로 정의하여 좀 더 많은 정보를 볼 수 있습니다.

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

**Maven 배포시에는 항상 버전을 올려주셔야 합니다.**


<br />

## JCenter 배포시

JCenter에 배포는 Bintray의 업무시간에 진행됩니다. 직원이 확인하고 배포를 하는 시스템입니다.

그렇기에 가능하면 미국의 현지 업무시간과 맞추는게 가장 빠르게 배포될 수 있습니다.


<br />

## JCenter 배포 완료

배포가 완료되면 아래와 같이 확인이 가능합니다.

![bintray_jcenter-sync]

jcenter에서도 직접 확인이 가능한데 검색하면 아래와 같이 표시됩니다.(실제 전 2개를 배포하였는데 하나만 승인이 되었네요...)

![bintray_jcenter_list]

전체 경로는 아래에서 JCenter에 배포된 모든 패키지들을 확인할 수 있습니다.

http://jcenter.bintray.com/


<br />

## 마무리

JCenter와 Bintray에서 배포되는 최소한의 내용이 달라서 추가로 정리하였습니다. `.jar`가 없으면 배포가 안된다는 사실도 이제야 알았으니 다음부턴 잊지 않도록!...

- <a href="androiddev/2016/09/01/Android-Bintray(JCenter)-Publish.html">Android Module을 Bintray(JCenter)에 배포하는 방법</a>

그리고 한번 배포되면 이후에는 **자동으로 배포가** 진행된다고 합니다.

감사합니다.


<br />

## 관련 글

- <a href="http://thdev.tech/androiddev/2016/09/01/Android-Bintray(JCenter)-Publish.html">Android Module을 Bintray(JCenter)에 배포하는 방법</a>
- <a href="http://thdev.tech/androiddev/2016/09/03/Android-Bintray(JCenter)-Publish-02.html">Android Module을 Bintray(JCenter)에 배포하기 삽질!...</a>


[result-01]:  /images/2016/2016-09-03-Android-Bintray(JCenter)-Publish-02/result_01.png

[bintray_jcenter-sync]:  /images/2016/2016-09-03-Android-Bintray(JCenter)-Publish-02/bintray_jcenter-sync.png

[bintray_jcenter_list]:  /images/2016/2016-09-03-Android-Bintray(JCenter)-Publish-02/bintray_jcenter_list.png

[jcenter-sync-03]:  /images/2016/2016-09-01-Android-Bintray(JCenter)-Publish/jcenter-sync-03.png
