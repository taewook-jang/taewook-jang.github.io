---
layout: post
title: Android Studio GitHub 공유하기 및 사용하기
categories: [AndroidDev]
tags: [Android Dev, Android Studio]
fullview: false
comments: true
published: true
---

안드로이드 스튜디오를 통해 GitHub와 연동하는 방법을 간단하게 정리하려고 합니다.

이 부분에서는 새로운 프로젝트를 생성하고, 모듈을 만드는 부분에 대한 설명은 포함되지 않습니다.

이미 생성된 프로젝트를 GitHub에 올리거나, 새로운 프로젝트를 GitHub에 공유하는 목적으로 작성하게 되었습니다.


<br />

## GitHub 사용하기

GitHub에 공유하고, GitHub의 데이터를 체크아웃 받아오는 방법은 여러 가지가 있습니다.

직접 명령어를 통해 GitHub를 사용하는 방법부터 GUI를 통한 방법 Android Studio에서 제공하는 GitHub 공유 및 사용하는 방법 등 다양한 방법이 존재합니다.

GitHub에 공유된 코드를 다운로드할 때는 직접 다운로드를 할 수도 있고, 이러한 툴과 명령어를 이용하는 방법 등이 제공됩니다.

- **Git 명령어를 통한 방법**
  - 명령어를 통해 직접 checkout을 받거나, commit을 하는 가장 기본적인 형태입니다. 가장 기본적으로 터미널을 이용하여 사용하게 됩니다.
- **SourceTree 등의 별도 GUI 환경을 사용하기**
  - 소스 트리 등을 이용하여 GUI 환경에서 GitHub를 관리할 수 있습니다. 이미 오래전부터 사용되는 방법이라 소스 코드 머지 등의 다양한 기능이 제공됩니다.
- **Android Studio를 이용하는 방법**
  - Android Studio에서 이러한 방법을 이미 제공하고 있습니다. 하지만 소스 트리나 명령어를 직접 입력하는 것에 비해 몇 가지 기능이 부족한 면이 있습니다. (`클린 업로드`, `pull` 받은 데이터를 머지 하다가 실패할 경우 이전 상태로 돌리는 등의 몇 가지 기능이 부족하여 `명령어`를 사용하여 이를 해결하기도 합니다.)


<br />

## Android Studio에서 공유하기

Android Studio를 통해서 생성한 프로젝트를 바로 공유할 수 있습니다. 프로젝트를 생성하는 방법에 대해서는 포함하지 않고, 공유에 대해서 정리하였습니다.

새로운 프로젝트를 생성할 수 있습니다.

![share-01]

생성할 프로젝트에 대한 `Application name`을 지정해줍니다.

![share-02]

생성된 프로젝트는 다음과 같이 확인이 가능합니다.

![share-03]

이제 메뉴에 가서 `VCS` > `Import into Version Control` > `Share Project On GitHub` 메뉴를 눌러서 `GitHub`에 공유를 시작합니다.

![share-04]

`GitHub`에 공유하기 위해서는 로그인을 해야 합니다. 로그인 인증 타입은 2가지가 있습니다.

- `Password` : GitHub 로그인시 패스워드
- `Token` : GitHub 페이지에서 별도로 토큰을 받아 로그인할 수 있으며, 일회용 비밀번호입니다.

![share-05]

2차 패스워드를 사용할 경우 아래와 같이 추가 인증을 하게 됩니다.

2차 패스워드를 통해 인증을 하지 않으시려면 `Token`으로의 로그인을 추천드립니다.

![share-05-01]

새로운 `repository`을 생성하는 부분으로 자신의 `GitHub`의 프로젝트 이름으로 노출됩니다.

유료 사용자이시면 `Private`에 공유가 가능하게 됩니다.

![share-06]

업로드할 파일을 선택합니다.

저는 `.idea` 폴더 자체를 업로드하지 않습니다. 일부 설정이 사용하시는 PC마다 자동으로 변경되기 때문에 굳이 업로드하지 않아도 문제는 없어서 저는 `.idea` 폴더 전체를 제외하고 업로드합니다.

![share-07]

추가로 다음과 같은 창이 표시될 수 있습니다. `.idea/vcs.xml` 파일이 추가로 생성되는데 이 파일도 굳이 업로드할 필요는 없어서 `No` 버튼을 눌러서 닫았습니다.

![share-08]

업로드가 완료되면 다음과 같이 확인이 가능합니다.

![share-result-01]

GitHub에서도 다음과 같이 확인이 가능합니다.

`All`에서 확인이 가능하며 `Private`은 유료로 결제하여 사용하는 경우에 확인이 가능합니다.

![share-result-02]


<br />

## 수정하여 Push 하기

수정하여 Push를 하게 됩니다.

우선 `Git`의 경우는 로컬에 `commit`을 하고, 이를 `push`를 통해 머지 하게 됩니다.

로컬에 가지고 있다가 한 번에 `push`를 할 수 있어서 상황에 따라 `push`를 진행할 수 있습니다.

예를 들면 로컬에서 작업을 많이 하고, 한 번에 올리는 경우입니다.

<br />

아래와 같이 빨간색으로 표시되는 경우는 아직 `git`에 `commit`할 수 없는 상태입니다. 이를 `Add`를 해서 `commit`을 할 수 있는데 저 파일에서 오른쪽 마우스를 눌러서 `Add` 해줄 수 있습니다.

![push-01]

또는 아래와 같이 수정을 한 다음이라면 `파란색`으로 표시가 됩니다. 최초 생성이면 `초록색`으로 표시됩니다.

![push-02]

저는 `.gitignore`을 수정하였고, 수정 사항을 확인할 수 있는데 맥에서는 `command + d`, 윈도우에서는 `ctrl + d`를 눌러서 달라진 사항을 한눈에 확인할 수 있습니다.

마우스 오른쪽을 눌러서 `Show Diff`를 눌러서 확인도 가능합니다.

![push-03]

수정사항을 `commit` 할 수 있는데 아래와 같이 오른쪽 마우스를 눌러서 `commit changes...`을 할 수 있습니다. 수정이 하나라면 해당 파일을 눌러주시면 되고 그렇지 않으면 `Default` 버튼을 눌러서 하시면 됩니다.

![push-04]

`Commit Changes`을 눌러주면 다음과 같이 추가 창이 표시됩니다.

단순히 `commit`을 할 수도 있고, `commit`과 동시에 `push`도 진행할 수 있습니다.

`push`까지 한 번에 하게 되면 GitHub에서 수초 내로 업데이트 상황을 확인할 수 있습니다.

![push-05]

별도로 `Push...`을 할 수 있는데 맥에서는 `Shift + command + k`를 눌러주면 됩니다.

윈도우에서는 `shift + ctrl + k` 일 겁니다.

![push-06]

만약 여러 건의 로컬 `commit`이 있다면 다음과 같이 여러 건의 Message를 확인할 수 있습니다. 오른쪽 하단의 `Push`을 통해 업데이트가 진행됩니다.

![push-07]

푸시가 완료되면 다음과 같이 간단하게 메시지 확인이 가능합니다.

![push-result]


<br />

## CheckOut(소스 코드 다운로드) 하기

GitHub의 소스 코드를 받을 수 있는 방법은 몇가지 있습니다.

`Clone or download` 버튼을 눌러서 URL을 통해서 직접 Git 명령어를 통해서 다운로드하거나, `GUI`를 통해서 다운로드할 수 있습니다.

또는 아래의 `download` 버튼을 눌러서 소스 코드를 압축으로 받을 수 있습니다.

AS에서 다운로드하는 방법을 설명하겠습니다.

- `clone 하기 위한 주소를 복사합니다.`

![check-out-01]

그리고 아래와 같은 화면에서 `Check out project from Version Control`을 눌러서 `GitHub`를 눌러줍니다.

![check-out-02]

상황에 따라서 로그인을 할 수 있습니다. 로그인을 해주시면 되고, 다음과 같이 `Clone Repository`에서 확인이 가능합니다. 로그인을 하신 다음이라서 이미 자신의 소스 코드는 해당 목록에 표시가 됩니다. 목록에 표시되지 않는 경우는 위에서 복사한 주소를 붙여 넣어주시고, `Directory Name`을 확인해주시면 되겠습니다.

![check-out-03]

버전 컨트롤을 통해 `Checkout`을 여부에서 `Yes`을 눌러서 다음을 진행합니다.

![check-out-04]

다음과 같이 Gradle을 설정할 하도록 합니다. 이 부분은 모두 기본값으로 `다음`, `다음`을 눌러서 완료 처리해주시면 됩니다.

- 여기에서 진행을 하더라도 최종적으로 오류가 발생할 수 있습니다.

![check-out-05]

마지막에서 `Finish`을 눌러서 완료 처리합니다.

![check-out-07]

다음과 같이 폴더 구조가 이상하게 생성이 될 겁니다.

`Android` 탭에서는 `app` 또는 `모듈 이름`만 표시되어야 합니다.

![check-out-09]

또 다른 오류는 다음과 같이 시작함과 동시에 오류가 나는 경우도 있습니다.

![check-out-error]

위와 같은 2가지 오류가 발생한다면 해당 Android Studio 프로젝트를 `X`버튼을 눌러서 닫아주세요. 완전히 종료를 하지 않고, 열어주시고, 첫 화면에서 `Open an existing Android Studio project`을 눌러서 해당 경로의 `Project`을 다시 열어 주시면 됩니다.

![share-01]

아래와 같이 정상적으로 실행이 되게 됩니다.

![check-out-10]

그리고 새로운 프로젝트를 연다면 아래와 같이 `Unregistered VCS root detected`가 표시됩니다. 다음에서 `Add root`를 눌러주시면 됩니다.

만약 이 창이 보이지 않으신다면 아래와 같이 하시면 됩니다.

- `Android Studio 설정` > `Version Control` > VCS를 설정해주시면 됩니다. 이 경우 `Git`을 설정해주시면 되고, 스튜디오상에서 `9: Version Control`을 확인할 수 있습니다.

![check-out-08]


<br />

## 추가로 .gitignore 설정하는 Plugin

`.gitignore`을 위한 별도의 `Plugin`이 있습니다.

이 플러그인은 다음과 같이 설치가 가능합니다.

`Browse Repositories`을 눌러주시면 `.ignore`에 대한 플러그인이 있습니다. 이를 설치하시면 Git 아니더라도 다른 것들에 대한 `.ignore`을 설정할 수 있습니다.

![git-ignore-install]

사용은 해당 프로젝트에서 `New` > `.ignore File` > `.gitignore file (Git)`을 눌러주시면 추가가 간단하게 됩니다.

![git-ignore-01]

추가시에 `Android`를 눌러주시면 필요한 설정을 추가로 제공해줍니다.

기본이랑 그다지 다른 부분은 없지만 추가로 설치해서 사용 중에 있습니다.

![git-ignore-02]


<br />

## 마무리

간단하게 GitHub를 통해 소스 코드 공유하고, 소스 코드를 다운로드하는 방법에 대해서 정리해보았습니다.

저는 최근에는 모두 GitHub에 코드를 업로드하고 있습니다. 이외에는 직접 명령어를 이용하는 방법도 있고, `SourceTree`등을 이용하는 방법 등이 있습니다.

다양한 `GUI Git`을 소개하는 페이가 있습니다.

- [GUI Clients](https://git-scm.com/download/gui/linux)



[share-01]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/share-01.png
[share-02]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/share-02.png
[share-03]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/share-03.png
[share-04]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/share-04.png
[share-05]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/share-05.png
[share-05-01]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/share-05-01.png
[share-06]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/share-06.png
[share-07]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/share-07.png
[share-08]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/share-08.png
[share-result-01]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/share-result-01.png
[share-result-02]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/share-result-02.png

[push-01]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/push-01.png
[push-02]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/push-02.png
[push-03]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/push-03.png
[push-04]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/push-04.png
[push-05]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/push-05.png
[push-06]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/push-06.png
[push-07]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/push-07.png
[push-result]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/push-result.png

[check-out-01]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/check-out-01.png
[check-out-02]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/check-out-02.png
[check-out-03]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/check-out-03.png
[check-out-04]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/check-out-04.png
[check-out-05]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/check-out-05.png
[check-out-07]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/check-out-07.png
[check-out-08]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/check-out-08.png
[check-out-09]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/check-out-09.png
[check-out-10]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/check-out-10.png

[check-out-error]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/check-out-error.png

[git-ignore-install]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/git-ignore-install.png
[git-ignore-01]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/git-ignore-01.png
[git-ignore-02]: /images/2016/2016-09-13-Android-Studio-GitHub-Share/git-ignore-02.png
