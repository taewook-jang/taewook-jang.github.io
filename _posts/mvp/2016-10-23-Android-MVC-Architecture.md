---
layout: post
title: Android MVP 무작정 따라하기 - MVC 구조 이해하기
categories: [AndroidDev]
tags: [Android, AndroidDev, MVP]
fullview: false
comments: true
published: true
---

오늘은 Android MVP 무작정 따라 하기 두 번째 시간으로, MVC 구조를 소개합니다.

MVC 구조로 작성된 코드를 하나씩 MVP 구조로 변경하기 위해서 추가해보는 글입니다.

MVC 구조는 아래와 같은 그림으로 나타낼 수 있습니다.

![mvc]

`MVC`는 `MVP` 이전의 구조 중 하나입니다.

`Model`, `View`, `Control`의 약자로 웹에서 주로 사용되는 구조입니다.

그래서 `Android`에 적용된 구조는 조금 다른 형태로 표현됩니다.

오늘은 간단하게 `MVC`의 동작 방법과 `Android에서` 사용 중인 `MVC` 구조를 바탕으로 예제 코드 설명을 추가하겠습니다.


<br />

## MVC로 작성한 샘플 코드

오늘 관련된 코드는 Branch 첫 번째의 `01-MVC`를 참고하시면 되겠습니다.

- [Android MVP Sample - Branch : 01-MVC](https://github.com/taehwandev/AndroidMVPSample/tree/01-mvc)


<br />

## MVC는?

`Model`, `View`, `Control`의 약자입니다.

MVC는 주로 웹에서 사용하고, 가장 널리 사용되는 구조 중 하나입니다.

MVC 구조에서의 입력은 모두 `Control`에서 발생하게 되는 구조입니다.

이벤트가 발생한 `Control`에 의해 모듈의 정의와 `View`의 용도가 결정됩니다.

- `Model` : 데이터를 가집니다.
- `View` : 사용자에게 보일 화면을 표현합니다.
- `Control` : 사용자로부터 입력을 받고, 이를 모델에 의해 View 정의를 하게 됩니다.


<br />

## 역시 어렵죠...

다시 정리해보면 아래와 같을 수 있습니다.

![mvc-default]

위의 그림을 순서대로 나열해보겠습니다.

1. `Control` : 사용자 이벤트 발생
2. `Control` : 사용자 이벤트가 발생하였는데 갱신이 필요한지 `Model`에 확인
3. `Model` : 데이터 갱신이 필요하다는 이벤트 발생
4. `View` : `Model` 또는 `Control`로부터 갱신 필요 여부 이벤트를 받는다.
5. `View` : `Model`에서 실제 필요한 데이터를 받아와 `View`를 갱신

일부 설명이 잘못되었을 수 있으나... 위와 같은 5단계 정도로 나열할 수 있을 것 같습니다.


<br />

## 하지만 Android에서는?

Android에서는 `View`와 `Control`이 `Activity`/`Fragment`같은 View들이 모두 가지고 있습니다.

예를 들면 다음과 같은 코드가 될 수 있습니다.

```java
public class MainActivity extends AppCompactActivity {

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    // ...

    FloatingActionButton fab = (FloatingActionButton) findViewById(R.id.fab);
    fab.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View view) {
            // 데이터 갱신 요청
            // Model에 접근해서 최신 데이터를 요청
              // ex) ArrayList<String> getItems()
            // 전달받은 ArrayList를 통해 View를 갱신
        }
    });
  }
}
```

하나의 화면 안에서 `Control`인 `setOnClickListener`이 발생하고, 이를 `View`에서 모두 처리하는 형태입니다.

Web에서 적용된 `MVC` 패턴은 `View`와 `Control`이 모두 분리된 상태를 말합니다.

하지만 안드로이드는 위와 같습니다.

그래서 그림으로 정리해보면 아래와 같을 수 있습니다.

![android-mvc]

간단하게 정리해보면 아래와 같습니다.

1. `Activity`에서 사용자 이벤트 발생
2. `Model`로부터 데이터 갱신이 필요한지 확인
3. `Model`로부터 전달받은 데이터를 통해 `View` 갱신 여부 판단
4. `View`에서 UI 갱신 처리


<br />

## 한 화면에서 모든 데이터를 처리함으로써 얻는 장점

`Android에서 흔하게 사용되는 MVC`는 class 하나로 처리가 가능한 구조가 만들어지게 됩니다.

그렇기에 정리만 잘하면 한눈에 코드 파악이 가능할 수 있지만, 어느 정도 범주를 벗어나면 코드 파악이 어렵습니다. 함수 분리가 안되어 있다면 더욱 그렇고요.

그렇기에 함수 분리 또는 class 분리를 적절하게 해야 복잡도가 낮아질 수 있습니다.

저는 아래와 같이 함수 분리하는 것을 좋아합니다. 하나의 예이지만...

```java
boolean isLast() {
  return itemList.size() >= 100;
}
```

대략 위와 같은 식으로 분리하게 됩니다.

그 외에도 공통으로 분리될 수 있어 보이는 `setVisibility()`도 여러 번 `Visible/Gone`이 발생한다면 당연히 함수로 분리합니다.

그렇기에 코드 분리만 잘해도 사실 `MVC` 패턴으로 코드 작성은 문제는 없습니다.


<br />

## 장점은?

- 개발 기간이 짧을 수 있음

생각보다 개발 기간이 짧을 수 있습니다. 생각해야 할 부분도 많지 않고, 그냥 Android Activity에서 모든 걸 다 동작하게 처리만 해주면 되므로, 개발 기간이 짧을 수 있습니다.

- 코드만 읽을 수 있다면 누구나 쉽게 파악 가능

그리고 처음 보는 사람도 별도의 패턴을 구분하지 않고, 쉽게 파악이 가능합니다.


<br />

## 단점은?

- 코드 양의 증가

`하나의 class`에서 모든 걸 할 수 있습니다.

 그로 인해 `하나의 class`에서 `수백 ~ 수천 줄`이 넘는 코드를 발견하실 수 있습니다.

- 스파게티 코드 가능성

복사 붙여넣기가 많아지고, 코드 분리가 안되어 있다면 스파게티 코드처럼 빙빙 꼬여있는 모습을 볼 수 있을 수 있습니다.

그렇기에 복잡도는 증가합니다.

`결국 처음 설계부터 중요하고... 분리도 잘해야겠죠`

- 유지 보수의 어려움

개발 기간이 짧다는 말은 그만큼 코드를 막 작성할 수 있다는 말이고, 코드의 정체성이 혼란이 생길 수 있습니다.

꾸준하게 이런 일이 발생하게 되면 쓰레기 코드의 양이 증가도 동시에 가져오게 됩니다.

이러한 이유들로 유지 보수 역시 어려워지게 됩니다.

- `View`와 `Model`의 결합도가 높다

MVC는 `View`와 `Model`간의 결합도가 높습니다. 대부분의 코드를 `View`에서 `Model`을 직접 호출하여 사용하게 됩니다.

그렇기에 `View`와 `Model` 간의 결합도가 높아지게 되고, 테스트 코드 작성에도 어려움이 발생합니다.

- 테스트 코드 작성이 어렵다

MVC는 대부분 `UI`에서 모든 걸 할 수 있기 때문에 `테스트 코드` 작성이 어려워지게 됩니다.

작성을 한다고 하더라도 `UI`위주의 테스트 코드 작성이 가능합니다.

하지만 `UI`는 변화가 자주 있는 곳 중에 하나입니다. `UI`가 아닌 모델까지 변경이 된다면...


<br />

## MVC로 작성한 샘플 코드

오늘 관련된 코드는 Branch 첫 번째의 `01-MVC`를 참고하시면 되겠습니다.

- [Android MVP Sample - Branch : 01-MVC](https://github.com/taehwandev/AndroidMVPSample/tree/01-mvc)


<br />

해당 샘플은 `MVC`로 작성하였고, `java/kotlin`의 코드를 모두 담고 있습니다.

- [Java sample](https://github.com/taehwandev/AndroidMVPSample/tree/01-mvc/app_java)
- [Kotiln sample](https://github.com/taehwandev/AndroidMVPSample/tree/01-mvc/app_kotlin)

아래와 같은 샘플을 확인할 수 있습니다.

![sample]


<br />

## 주요 코드 살펴보기

- Model에 대한 data 코드는 이미 분리되어 있습니다.

- Activity

```java
// Adapter를 생성
imageAdapter = new ImageAdapter(this);
// Adapter에 itemList를 data를 통해서 불러와서 저장
imageAdapter.setImageItems(SampleImageData.getInstance().getImages(this, 10));
// RecyclerView에 adapter를 세팅
recyclerView.setAdapter(imageAdapter);
```

만약 메뉴에서 reload를 호출하게 되면 다음의 코드가 동작

```java
// reload 액션이 발생
if (id == R.id.action_reload) {
    // 기존 itemList clear
    imageAdapter.clear();
    // 새로운 ImageList 불러와서 교체
    imageAdapter.setImageItems(SampleImageData.getInstance().getImages(this, 10));
    // UI Change
    imageAdapter.notifyDataSetChanged();
    return true;
}
```

- Adapter

Java에서 작성한 Adapter의 경우 ViewHolder까지 모두 한 class에 포함하고 있습니다. 아직 분리하지 않은 상태로 보여드리기 위해서 다음과 같이 작성하였습니다.

- [ImageAdapter.java](https://github.com/taehwandev/AndroidMVPSample/blob/01-mvc/app_java/src/main/java/tech/thdev/android_mvp_sample/adapter/ImageAdapter.java)


<br />

## 코틀린의 주요 코드 살펴보기

- Activity

대부분 자바와 동일하지만 일부 문법적으로 다른 부분이 있으므로 몇 가지만 추가로 설명하겠습니다.

```kotlin
// 사용자가 reload 버튼 클릭
if (id == R.id.action_reload) {
    // Image adapter가 null이 아니라면 다음 apply 블락 안의 내용을 수행
    imageAdapter?.apply {
        // clear
        imageList?.clear()
        // 새로운 데이터를 불러와서 imageList를 교체
        imageList = ImageData.getSampleList(baseContext, 10)
        // Adapter를 notifyDataSetChanged 호출
        notifyDataSetChanged()
    }
    return true
}
```

여기에서 apply는 ImageAdapter 내부 로직을 간단하게 불러올 수 있는 block이라고 생각하시면 되겠습니다.

연속적으로 위와 같이 처리해야 하는 경우에 유용하게 사용이 가능합니다.

- Adapter

Adapter에서 ViewHolder 부분은 아래와 같습니다.

ImageAsync를 직접 구현하여 사용하였습니다. 아직 외부 다른 라이브러리를 추가하지 않고, 로컬로 처리하였습니다.

`setOnClickListener`은 람다식이 적용된 모습입니다.

```kotlin
override fun onBindViewHolder(holder: ImageViewHolder?, position: Int) {
    val item = imageList?.get(position)

    ImageAsync(holder?.imageView).execute(item?.resource)
    holder?.textView?.text = item?.title

    holder?.itemView?.setOnClickListener {
        Toast.makeText(context, "Show ${item?.title}", Toast.LENGTH_SHORT).show()
    }
}
```

- Model

Java와 Kotlin에서 모두 동일한 Model을 가집니다. 다만 Kotlin에서는 `object class`를 생성하여 사용하였습니다.

- [ImageData.kt](https://github.com/taehwandev/AndroidMVPSample/blob/master/app_kotlin/src/main/java/tech/thdev/app_kotlin/data/ImageData.kt)

```kotlin
object ImageData {

    fun getSampleList(context: Context, size: Int) : ArrayList<ImageItem> {
        val list = ArrayList<ImageItem>()
        for (index in 0..size) {
            // Random을 통해 이미지를 불러온다.
            val name = String.format("sample_%02d", (Math.random() * 15).toInt())
            // getIdentifier을 통해서 resource을 찾습니다.
            val resource = context.resources.getIdentifier(name, "drawable", context.packageName)
            list.add(ImageItem(resource, name))
        }
        return list
    }
}
```

- <a href="https://developer.android.com/reference/android/content/res/Resources.html#getIdentifier(java.lang.String, java.lang.String, java.lang.String)">Android API getIdentifier</a>


<br />

## 마무리

다음 글에서 오늘 작성한 MVC 코드를 MVP로 변환하는 과정을 설명하도록 하겠습니다.

우선 첫 번째 작성한 MVC에서 어떤게 View이고 어떤 부분이 Presenter로 분리되어야 하는지를 먼저 살펴보겠습니다.


<br />

## MVP 무작정 따라하기

무작정 따라 하기는 MVP 패턴을 이해할 수 있도록 하나씩 차근차근 작성해보는 예제입니다.

그래서 각각의 브런치와 글들을 통해서 조금씩 확장해가려고 합니다.

- [Android MVP 무작정 따라하기 - Intro](http://thdev.tech/androiddev/2016/10/12/Android-MVP-Intro.html)
- [Android MVP 무작정 따라하기 - MVC 구조 이해하기](http://thdev.tech/androiddev/2016/10/23/Android-MVC-Architecture.html)
- [Android MVP 무작정 따라하기 - Presenter/View 생성하기](http://thdev.tech/androiddev/2016/11/28/Android-MVP-One.html)
- [Android MVP 무작정 따라하기 - Presenter/View 생성하기 Other](http://thdev.tech/androiddev/2016/11/30/Android-MVP-Two.html)
- [Android MVP 무작정 따라하기 - Presenter 분리하기](http://thdev.tech/androiddev/2016/12/23/Android-MVP-Three.html)
- [Android MVP 무작정 따라하기 - Adapter Contract 정의하기](http://thdev.tech/androiddev/2016/12/26/Android-MVP-Four.html)
- [Android MVP 무작정 따라하기 - Adapter Contract 정의하기 2번째(동영상)](http://thdev.tech/androiddev/2016/12/27/Android-MVP-Four-Two.html)
- [Android MVP 무작정 따라하기 - Adapter OnClick 정의하기(동영상)](http://thdev.tech/androiddev/2016/12/29/Android-MVP-Four-Three.html)
- [Android MVP 무작정 따라하기 - Model 정의하기](http://thdev.tech/androiddev/2016/12/29/Android-MVP-Model-One.html)
- [Android MVP 무작정 따라하기 - Google Architecture의 Model](http://thdev.tech/androiddev/2017/01/09/Android-MVP-Model-Two.html)
- [Android MVP 무작정 따라하기 - Google Architecture Model(영상 포함)](http://thdev.tech/androiddev/2017/01/29/Android-MVP-Google-Architecture-Model.html)


[mvc]: /images/2016/2016-05-03-MediaProjection-MVP-Pattern/MVC.png
[mvc-default]: /images/2016/2016-10-23-Android-MVC-Architecture/default-mvc.png
[android-mvc]: /images/2016/2016-10-23-Android-MVC-Architecture/android-mvc.png

[sample]: /images/2016/2016-10-23-Android-MVC-Architecture/sample.png
