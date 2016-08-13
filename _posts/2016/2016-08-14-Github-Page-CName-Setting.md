---
layout: post
title: GitHub page CNAME 설정으로 개인 도메인 사용하기
categories: [Web]
tags: [Web]
fullview: false
comments: true
published: true
---

GitHub에 개인의 도메인을 연결하는 방법을 정리합니다.

GitHub에는 `자신의 이름.github.io`를 이용하여 블로그를 운영할 수 있습니다. `Jekyll`를 base로 하여 간단한 웹 페이지를 생성할 수 있습니다.

GitHub 페이지 생성하는 방법은 기존 작성 글을 참고해주세요.

[GitHub 블로그 빠르게 시작하기!](http://thdev.net/653)


<br />

## CNAME 설정하기

CNAME를 통해 생성한 페이지의 Custom Domain을 사용할 수 있습니다.

하나만 등록하면 이후에 생성하는 GitHub Pages의 도메인은 자동으로 `도메인/생성 페이지 이름`으로 설정이 됩니다.

도메인을 설정하는 방법은 다음의 링크를 확인할 수 있습니다.


[https://help.github.com/articles/using-a-custom-domain-with-github-pages/](https://help.github.com/articles/using-a-custom-domain-with-github-pages/)을 이용하여 자세한 정보를 확인할 수 있습니다.


쉽게 도메인을 적용하는 방법은 다음과 같습니다.

설정으로 들어가서 `GitHub Pages` 부분에 아래와 같이 페이지 주소가 생성됩니다.

> 저는 이미 생성해둔 DNS가 있어서 `도메인/repository name`로 생성되었습니다.

여기에 개인 도메인을 사용하고 싶다면 subdomain `blog.example.com` 또는 apex domain `example.com`을 적어주시면 됩니다.

![Screenshot 2016-08-14 01.36.16](/images/2016/2016-08-14-Github-Page-CName-Setting/Screenshot 2016-08-14 01.36.16.png)


그리고 자신이 사용하는 도메인 홈페이지로 이동하여 다음을 등록합니다.

- 192.30.252.153
- 192.30.252.154

위에서 등록한 CNAME 이름을 기준으로 만약 `example.com`이라면 다음과 같이 등록합니다.

![Screenshot 2016-08-14 01.34.13](/images/2016/2016-08-14-Github-Page-CName-Setting/Screenshot 2016-08-14 01.34.13.png)

기준을 호스트 IP(A)로 설정하고 위의 IP 주소 2개를 추가해주시면 됩니다.

![Screenshot 2016-08-14 01.34.03](/images/2016/2016-08-14-Github-Page-CName-Setting/Screenshot 2016-08-14 01.34.03.png)


간단하게 CNAME을 적용하여 개인 도메인 연결이 가능합니다.

만약 `blog.example.com`으로 하고 싶다면 호스트 IP(A)를 추가할 때 `blog`라고 추가만 해주시면 됩니다.
