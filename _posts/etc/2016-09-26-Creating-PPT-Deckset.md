---
layout: post
title: 맥에서 Markdown으로 PPT를 만들 수 있는 Deckset
categories: [Tools]
tags: [Tools, Markdown]
fullview: false
comments: true
published: true
---

`Mac OS`에서 `Markdown`을 이용하여 `PPT`를 만들 수 있는 [Deckset](http://www.decksetapp.com/)을 소개합니다.

개발과 관련된 `PPT`를 제작할 때는 소스코드가 항상 고민이었습니다.

단순히 복사 붙여넣기를 통해서 `소스 코드가 포함된 PPT`의 경우 맘에 들지 않게 표현이 됩니다. 단순히 검은색의 텍스트가 포함되거나, 캡처를 통해서 추가하였습니다.

소스 코드에 대한 `편집이 편하지 않고`, `색상`을 추가하기도 어렵습니다.

그래서 캡처한 이미지를 추가하는 일을 하는 경우가 있습니다.

[Deckset](http://www.decksetapp.com/)은 몇 가지 `기본 테마를 제공`할 뿐 `편집은 제공하지 않습니다.` 하지만 `Markdown`을 통해서 추가 편집이 가능하므로, 사용하게 되었습니다.


<br />

## Deckset 다운로드 및 가격

[Deckset](http://www.decksetapp.com/)은 아쉽지만 `유료 앱`이고, `$29.99`에 판매 중입니다. Trial 제공되고 있긴 합니다.

- 앱스토어 다운로드 : [Deckset](https://itunes.apple.com/us/app/deckset-turn-your-notes-into/id847496013?ls=1&mt=12)
- Trial 버전 다운로드 : [Deckset Trial](http://www.decksetapp.com/try.html)


<br />

## Deckset

Deckset은 [Markdown](https://daringfireball.net/projects/markdown/)을 사용하여 제작합니다.

- [Markdown](https://ko.wikipedia.org/wiki/%EB%A7%88%ED%81%AC%EB%8B%A4%EC%9A%B4)은 일반 텍스트 및 문서의 양식을 편집하는 문법이라고 합니다.

## Deckset의 장점

Deckset을 통해 얻을 수 있는 이점은 다음과 같습니다.

편집은 아쉽지만 외부 다른 편집기를 사용해야 합니다. 저는 주로 Atom을 사용하고 있습니다.

- Markdown을 사용하여 PPT 제작 가능
- 편집 시에는 다른 Edit를 사용함
  - [Atom](https://atom.io/), 텍스트 편집기, Excel, Xcode 등이 가능
- 수정 사항 저장시 실시간 PPT 확인 가능
- `소스 코드`, `이미지`, `텍스트` 등의 간단한 작업 가능

## Deckset 단점

- 동시에 2개를 열려면 하나의 샘플을 열어두고 `Open`을 통해서 열어주어야 합니다.
- 2개가 열려있는 상황에서는 `실시간으로 변경 사항`을 체크하기 위해서는 `꼭 작업 중인 화면`을 활성화해 두어야 합니다.
- 테마를 수정하는 기능에 제공되지 않고 있습니다.
- 출력은 `pdf`, `jpg`, `png`로 진짜 `ppt`로 출력하는 건 불가능합니다.

오늘은 `Deckset`을 소개하면서 Deckset에서 사용 가능한 `Markdown 문법` 몇 가지를 소개하려고 합니다.


<br />

## Deckset 사용하기

Deckset을 실행하면 다음과 같습니다.

최근에 사용한 목록과 함께 예제가 제공됩니다.

여기에서 제공하는 4가지 예제를 모두 활용하면 간단한 PPT 제작이 가능합니다.

![main]

New를 통해서 새로운 Deckset을 이용한 PPT 생성이 가능하며, Examples를 각각 눌러서 `Deckset`의 사용법을 익힐 수 있습니다.

### 기본 샘플

첫 번째 샘플에서는 `Deckset`에 대한 전반적인 사용 방법이 모두 담겨 있습니다.

아래는 일부만 보이고 있습니다. `이미지를 배경`으로 깔거나, 그 위에 `텍스트를 올리거나`, `오른쪽에 추가하고 왼쪽에 텍스트`를 담을 수 있으며, `소스 코드 예제`까지 모두 담고 있습니다.

![sample-01]

### 이미지에 대한 샘플

두 번째 샘플에서는 이미지를 좀 더 심화적으로 다루고 있습니다. 이미지를 `fit`, `배경화면`, `사이즈 조절`, `여러 개를 한 줄에 담기`등의 샘플이 제공됩니다.

![sample-02]

### 텍스트에 대한 샘플

이번에는 텍스트에 대한 샘플을 담고 있습니다.

텍스트를 좀 더 크고, 작고, 숫자를 강조하고 등의 샘플 예제를 볼 수 있습니다.

![sample-03]

### 교육에 대한 샘플

마지막 샘플에서는 교육에 대한 자료를 담고 있습니다.

그래서 수학 수식을 추가한 예제를 포함하고 있습니다.

![sample-04]


위의 4가지 샘플을 통해서 `Deckset`을 사용하여 PPT를 만들어 볼 수 있습니다.


<br />

## 새로운 PPT 만들기

새로운 샘플을 만들어보겠습니다.

다음과 같은 화면에서 `New`를 눌러줍니다.

![main-new]

확장자는 `.md`로 끝이 납니다. 적당한 이름으로 저장해주시면 되겠습니다.

![new-ppt]

최초에 파일을 생성하시면 다음과 같이 추가 샘플이 또 한 번 나오게 됩니다.

이를 통해서 `Deckset`을 좀 더 쉽게 사용할 수 있습니다.

![new-sample]

그리고 함께 뜨는 다음의 창이 있습니다.

Markdown을 편집할 툴을 고르면 되겠습니다.

제 PC에서는 `Atom`, `Excel`, `TextEdit`, `Xcode`가 설치되어 있어서 다음이 표시됩니다.

특이하게 엑셀도 표시되긴 합니다.

![selected-edit]


<br />

## 편집하기

저는 Atom을 통해서 편집을 진행합니다.

Atom을 실행했더니 특이하게도 파란색 창이 보입니다.

그리고 첫 번째 라인에 보이시는 `Did you know?`라는 메시지가 오른쪽 파란색 화면에서도 함께 보입니다.

![edit-01]

여기서 알아두시면 좋을게 편집을 저장하시면 실시간으로 확인이 가능합니다.

그래서 가능하면 1페이지를 작업하시고, 저장하는게 좋습니다.

즉 `저장`과 동시에 `실시간으로 확인`이 가능하다는 말이 되겠습니다.


<br />

## 주요 메뉴는?

Markdown을 살펴보기 전에 메뉴를 살펴보도록 하겠습니다.

`Deckset`의 주요 메뉴는 아래가 다입니다.

`Play`, `Edit(편집)`, `Themes` 변경이 가능합니다.

![menu]

`테마`의 경우에는 10개가 제공됩니다. 각각 색상이 제공되므로, 원하시는 테마를 고르시면 되겠습니다.


<br />

## 마크다운으로 작성은?

이제 마크다운으로 작성하는 부분에 대해서 주요 코드들을 살펴보려고 합니다.

모두 샘플에서 제공하지만 몇 가지 적어보겠습니다.

### 페이지 나누기

페이지를 구분해야 합니다. 페이지는 아래와 같이 `---`으로만 구분하시면 되겠습니다.

```
---
```

### 페이지 번호 표시 및 footer 추가하기

`slide number`와 `footer`를 추가하기 위해서는 다음의 코드를 생성한 파일 최상단에 한 번만 적용해주시면 되겠습니다.

```
footer: thdev.tech, 2016
slidenumbers: true
```

### 페이지 타이틀 적용하기

페이지 타이틀은 디자인마다 조금씩 다르지만 다음의 방법으로 적용이 가능합니다.

한 줄에 적절하게 채우는 방법은 `[fit]`을 써주는 방법입니다.

```
# [fit] 이름
```

이 경우 `PPT 가로 사이즈`를 기준으로 가득 차게 됩니다.

그냥 단순 제목이면 다음과 같습니다.

`#`의 개수에 맞게 글씨 크기가 달라집니다.

```
# 가장 크게
## 조금 크게
### 조금 작게
```

다음고 같이 보이게 됩니다.

# 가장 크게

## 조금 크게

### 조금 작게

#### 더 작게

<br />

### 소스코드 적용하기

소스 코드는 시작 부분에 \`\`\`으로 시작하고, 종료 부분에 \`\`\`을 써주시면 되겠습니다.

시작 부분에서는 \`\`\` 끝에 사용할 언어를 \`\`\`java 써주신다면 java 코드 형태로 보이게 됩니다.

그러면 아래와 같이 확인 가능합니다.

```java
public class Temp {
  public void sample() {
    // ...
  }
}
```

### 인용문

인용문의 경우 `>`으로 적어주시면 되겠습니다.

실제 PPT에서는 아래와 같이 보인다고 합니다.

![edit-02]

### 이미지 추가

이미지는 백그라운드로 추가될 수도 있고, 라인에 포함될 수도 있습니다. 방법이 다양해서 몇 가지 예를 적겠습니다.

> 이미지를 추가할 경우 꼭 파일에 `.png`/`.jpg` 등의 확장지가 포함되어야 하며, 실제 파일에서도 표시되어야 합니다. 그렇지 않으면 `Deckset`에서 파일 이름을 찾지 못 합니다. 꼭 이점 명심하여주시면 되겠습니다.

#### 배경화면 추가

PPT 전체에 꽉 차는 배경화면은 다음과 같습니다.

```
![](sample.jpg)
```

PPT 위에 텍스트를 추가하려면 다음과 같습니다.

```
Sample Text...

![](sample.jpg)
```

#### 비율 유지

비율을 유지한 채로 이미지를 추가하려면 다음과 같습니다.

```
![fit](sample.jpg)
```

#### 이미지의 사이즈를 지정하기

이미지의 사이즈를 지정할 수 있습니다. 1~100까지이며, `%` 단위로 표시합니다.

```
![20%](sample.jpg)
```

#### 왼쪽 또는 오른쪽에 이미지 표시하기

왼쪽에 표시할 경우는 다음과 같습니다.

```
![left](sample.jpg)
```

오른쪽에 이미지를 표시하면 다음과 같습니다.

```
![right](sample.jpg)
```

`left`, `right`을 각각 추가할 수 있는데 이 경우에는 텍스트를 입력하면 반대편에 포함이 자동으로 일어나게 됩니다.

이미지 2장을 `left`, `right`에 동시에 하려면 다음과 같습니다.

```
![left](sample.jpg)
![right](sample.jpg)
```

#### 2장 이상 추가하려면?

2장 이상을 연속으로 놓으려면 다음과 같습니다.

```
![](sample.jpg)
![](sample.jpg)
![](sample.jpg)
```

여기에서 3장의 이미지를 추가하고, 그 위에 텍스트를 타이핑한다면 텍스트가 올라간 상태로 이미지가 백그라운드화됩니다.

#### inline

인라인이라는 키워드가 있습니다. 이를 통해 배경이 아닌, 내용의 흐름상 이미지가 될 수 있습니다.

```
## 제목

내용 내용...

![inline](sample.jpg)

내용 내용...
```

위와 같이 작성하시면 내용 사이에 이미지가 포함되게 됩니다.

#### 2가지 이상 혼합하기

2가지 이상 혼합이 가능합니다. `inline`을 함과 동시에 `right`에 이미지를 추가한다면 다음과 같습니다.

```
## 제목

내용 내용 ...

![inline, right](sample.jpg)
```

이와 같이 처리하시면 오른쪽에 이미지가 포함되어 다음과 같이 확인이 가능합니다.

![edit-03]

#### original 키워드

`original`이라는 키워드가 있습니다. 배경화면으로 적용하는 경우 테마에 따라 `이미지가 회색`으로 나올 수도 있고, `특정 필터`가 먹기도 합니다.

이 경우 `original` 키워드를 활용하여 원본 그대로를 사용할 수 있습니다.

```
![original](sample.jpg)
```

왼쪽은 `original` 키워드가 적용되지 않은 경우이고, 오른쪽은 적용한 경우입니다.

확연하게 차이가 나게 됩니다.

![edit-05]


<br />

## 마무리

Deckset을 이용한 PPT 제작 방법을 몇 가지 정리해보았습니다.

위와 같이 정리하였고, 주요 문법도 정리해보았습니다.

저는 이 정도이면 제가 사용하는 PPT에 충분하다고 생각됩니다. 추가로 알고 싶으신 부분은 샘플에서 살펴보시면 되겠습니다.

테마를 이쁘게 하고, 애니메이션을 추가하고 싶으시다면 사실 이걸로는 부족합니다.

소스 코드 기반의 정적 PPT를 만드실 분이라면 추천드리겠습니다.

마지막으로 `Deckset`은 `PPT`로의 출력은 불가능하고, `pdf`, `jpg`, `png`의 이미지 형태로 `Output`이 가능합니다. 이 점이 조금 아쉽습니다.

메뉴의 `File` > `Export...`을 눌러줍니다.

![export-01]

여기에서 3가지 Type을 선택할 수 있습니다. 선택하고, 경로 지정 후 저장하기 해주시면 됩니다.

`PDF`가 가장 무난하겠죠.

![export-02]

감사합니다.



[main]: /images/2016/2016-09-26-Creating-PPT-Deckset/main.png
[main-new]: /images/2016/2016-09-26-Creating-PPT-Deckset/main-new.png
[new-ppt]: /images/2016/2016-09-26-Creating-PPT-Deckset/new-ppt.png
[new-sample]: /images/2016/2016-09-26-Creating-PPT-Deckset/new-sample.png
[selected-edit]: /images/2016/2016-09-26-Creating-PPT-Deckset/selected-edit.png
[menu]: /images/2016/2016-09-26-Creating-PPT-Deckset/menu.png


[edit-01]: /images/2016/2016-09-26-Creating-PPT-Deckset/edit-01.png
[edit-02]: /images/2016/2016-09-26-Creating-PPT-Deckset/edit-02.png
[edit-03]: /images/2016/2016-09-26-Creating-PPT-Deckset/edit-03.png
[edit-05]: /images/2016/2016-09-26-Creating-PPT-Deckset/edit-05.png

[sample-01]: /images/2016/2016-09-26-Creating-PPT-Deckset/sample-01.png
[sample-02]: /images/2016/2016-09-26-Creating-PPT-Deckset/sample-02.png
[sample-03]: /images/2016/2016-09-26-Creating-PPT-Deckset/sample-03.png
[sample-04]: /images/2016/2016-09-26-Creating-PPT-Deckset/sample-04.png

[export-01]: /images/2016/2016-09-26-Creating-PPT-Deckset/export-01.png
[export-02]: /images/2016/2016-09-26-Creating-PPT-Deckset/export-02.png
