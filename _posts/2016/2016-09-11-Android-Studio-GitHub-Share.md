---
layout: post
title: Android Studio GitHub 공유하기 및 사용하기
categories: [AndroidDev]
tags: [Android Dev, Android Studio]
fullview: false
comments: true
published: false
---

안드로이드 스튜디오를 통해 GitHub와 연동하는 방법을 간단하게 정리하려고 합니다.

이 부분에서는 새로운 프로젝트늘 생성고, 모듈을 만드는 부분에 대한 설명은 포함되지 않습니다.

이미 생성된 프로젝트를 GitHub에 올리거나, 새로운 프로젝트를 GitHub에 공유하는 목적으로 작성하게되었습니다.


<br />

## GitHub 사용하기

GitHub에 공유하고, GitHub의 데이터를 체크아웃 받아오는 방법은 여러가지가 있습니다.

직접 명령어를 통해 GitHub를 사용하는 방법부터 GUI를 통한 방법 Android Studio에서 제공하는 GitHub 공유 및 사용하는 방법 등 다양한 방법이 존재합니다.

GitHub에 공유된 코드를 다운로드 받을때는 직접 다운로드를 할 수도 있고, 이러한 툴과 명령어를 이용하는 방법등 다양한 방법이 제공됩니다.

- **Git 명령어를 통한 방법**
  - 명령어를 통해 직접 checkout을 받거나, commit을 하는 가장 기본적인 형태입니다. 가장 기본적으로 터미널을 이용하여 사용하게 됩니다.
- **SourceTree 등의 별도 GUI 환경을 사용하기**
  - 소스트리 등을 이용하여 GUI 환경에서 GitHub를 관리할 수 있습니다. 이미 오래전부터 사용되는 방법이라 소스코드 머지등의 다양한 기능이 제공됩니다.
- **Android Studio를 이용하는 방법**
  - Android Studio에서 이러한 방법을 이미 제공하고 있습니다. 하지만 소스트리나 명령어를 직접 입력하는 것에 비해 몇가지 기능이 부족한 면이 있습니다. (`클린 업로드`, `pull` 받은 데이터를 머지하다가 실패 할 경우 이전 상태로 돌리는 등의 몇가지 기능이 부족하여 `명령어`를 사용하여 이를 해결하기도 합니다.)


<br />

## Android Studio에서 공유하기

Android Studio를 통해서 바로 해당 프로젝트를 공유할 수 있습니다.
