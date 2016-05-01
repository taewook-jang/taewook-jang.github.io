---
layout: post
title: 모바일 크롬에서 단축 아이콘 생성(WebApp 만들기?)
published: true
---

Chrome 31 버전부터 웹앱을 만들 수 있는 Shortcut을 제공하고 있었다고 합니다.

그간 웹의 성능이 많이 좋아지면서 많은 변화가 있었고, 크롬 브라우저를 통한 웹앱생성이 가능하다고 합니다.

지난주 [Google Developers Summit](http://googledevkr.blogspot.kr/2016/04/google-developers-summit.html)에 참여하여 Introduction to Progressive Web App ([Robert Nyman](https://robertnyman.com/), Google)의 주제를 듣고나서 알게되었습니다.

크롬 개발문서에는 [Add to Homescreen](https://developer.chrome.com/multidevice/android/installtohomescreen) 문서가 제공중입니다.

오늘은 Add to Homescreen을 적용하는 방법을 간단하게 살펴보겠습니다.

<br />

## 홈스크린 앱 만들기

Chrome 31부터 지원하였지만 39버전 부터는 JSON-based manifest 파일을 생성하여 간단하게 웹앱을 생성할 수 있다고 합니다.

크롬 브라우저에 단축 아이콘 생성을 하게 되면 마치 네이티브앱과 동일하게 동작하게 됩니다.

Robert Nyman이 소개한 페이지를 안드로이드 크롬에서 접속하시면 단축 아이콘 생성 유도가 됩니다.(39버전 이상)

단축 아이콘을 생성하게 되면 [Flipkart](flipkart.com)이 네이티브앱 처럼 동작하게 됩니다.

<br />

## 단축 아이콘을 지원하기

네이티브 앱처럼 동작하는 단축 아이콘을 만들기 위해서는 몇가지 meta tag와 json 관련된 부분을 추가해주면 됩니다.

메인 페이지에 다음을 추가해줍니다.(저는 github 블로그에 추가할탠데 index.html이 됩니다.)

```html
<link rel="manifest" href="manifest.json">
```

이제 json을 생성해주게 됩니다.

json에는 화면의 회전 상태를 강제로 지정할 수 있고, 단축아이콘으로 사용할 이미지의 단축 아이콘을 정의할 수 있습니다.

```json
{
  "name": "Web Application Manifest Sample",
  "icons": [
    {
      "src": "launcher-icon-0-75x.png",
      "sizes": "36x36",
      "type": "image/png",
      "density": 0.75
    },
    {
      "src": "launcher-icon-1x.png",
      "sizes": "48x48",
      "type": "image/png",
      "density": 1.0
    },
    {
      "src": "launcher-icon-1-5x.png",
      "sizes": "72x72",
      "type": "image/png",
      "density": 1.5
    },
    {
      "src": "launcher-icon-2x.png",
      "sizes": "96x96",
      "type": "image/png",
      "density": 2.0
    },
    {
      "src": "launcher-icon-3x.png",
      "sizes": "144x144",
      "type": "image/png",
      "density": 3.0
    },
    {
      "src": "launcher-icon-4x.png",
      "sizes": "192x192",
      "type": "image/png",
      "density": 4.0
    }
  ],
  "start_url": "index.html",
  "display": "standalone",
  "orientation": "landscape"
}
```
