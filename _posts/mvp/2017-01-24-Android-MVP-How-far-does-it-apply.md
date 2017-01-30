---
layout: post
title: Android MVP 무작정 따라하기 - 어느정도 범위로 적용할 수 있을까?
categories: [AndroidDev]
tags: [Android, AndroidDev, MVP]
fullview: false
comments: true
published: false
---

MVP 무작정 따라하기 11 번째 글입니다.

그간 작성한 글을 간략하게 정리하며, 이 포괄적인 MVP를 어느정도까지 적용하는게 좋을지에 대해서 정리해보려고 합니다.

MVP 적용하는 부분은 주관적인 부분이 많습니다.


<br />

## 지금까지 ...

그간 정리하였던 글과 샘플 코드는 다음과 같습니다.

[AndroidMVPSample](https://github.com/taehwandev/AndroidMVPSample)

그리고 이를 도식화 하면 아래 그림과 같습니다.

![model]

이 샘플은 Java/Kotlin을 함께 작성하였습니다.

View/Model/Presenter를 각각 정리하면 다음과 같습니다.

- View : View의 정의만 가진다(비즈니스 로직(Business logic)을 제외한 부분을 처리한다(Control/View))
- Presenter : 비즈니스 로직(Business logic)을 처리한다
- Model : Local/Remote에 대한 데이터를 처리한다

위와 같이 정리하고, 그간 글들을 정리하였습니다.


<br />

## MVP 적용을 얼마나 진행하면 좋을까?

MVP는 구조 자체를 변경하는 것입니다.

전체 코드를 일괄성 있게 유지하며,


공부양에 따라서 달라지는 부분이며, 이 부분은 개인적인 생각을 주입하여 정리합니다.

혼자서 공부하고, 이를 이해하고 쓰더라도 적용 범위를 약 3단계 정도로 분리하면 좋을것 같습니다.

- 1 단계 : View/Presenter를 분리하며, Model의 정의는 RxJava로 대체 가능하며, 이미 알고 있는 방법으로 접근
- 2 단계 : Model의 정의를 구글에서 정의하는 Repository 방법으로 정리
- 3 단계 : [dagger](https://google.github.io/dagger/) 적용을 하거나, [DataBinding](https://developer.android.com/topic/libraries/data-binding/index.html)을 적용, layer 형태로 변형

대략 3단계 정도로 위와 같이 정의가 가능합니다.

당연히 처음부터 MVP의 구조로 작업을 진행하고, 이를 이해하는 방법이 가장 좋겠지만 그렇지 못합니다.

새로 추가하는 `Activity/Fragment`에 대해서 MVP 정의를 시작합니다.


<br />

## 다음을 이해하시면 좀 더 쉽게 적용이 가능합니다.

다음의 경우 View에서 처리해야 할까요? Presenter에서 처리해야 할까요?

- View에서 onClick이 발생합니다.
	- 텍스트 크기를 키웁니다.
	- 텍스트의 색상을 변경합니다.

저는 위와 같은 경우 View에서 만 처리합니다.

```kotlin
class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

				button.setOnClickListener {
					// View에서 색상을 변경하고, 텍스트 크기를 변경
					button.color = ...
					button.size = ...
				}
    }
}
```

위와 같이 View에서 모든것을 처리한다면 `Presenter`를 거치지 않고 바로 처리하게 됩니다.


### Model이 필요한 경우라면?

- View에서 onClick이 발생합니다.
	- Preferences 정의값을 가져와 View 갱신을 해야 합니다.
		- Text size
		- Text color

Preferences에는 Text size/color가 정의되어 있습니다.

이 경우는 `Presenter`에서 비즈니스 로직을 처리하고, View 갱신만을 합니다.

```kotlin
class MainActivity : AppCompatActivity(), MainContract.View {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

				button.setOnClickListener {
					// View에서 색상을 변경하고, 텍스트 크기를 변경
					presenter.updateUI()
				}
    }

		override fun updateColor(color: Int) {
			button.color = color
		}

		override fun updateSize(size: Int) {
			button.size = size
		}
}
```

비즈니스 로직은 `Presenter`의 updateUI 메소드에서 처리합니다.

updateUI에서는 데이터를 불러오고 이를 갱신하는 역활을 처리합니다.

```java
public class MainPresenter implements MainContract.Presenter {

	@Override
	public void updateUI() {
		// Preferences 처리...

		view.updateColor(color);
		view.updateSize(size);
	}
}
```



<br />

## 동영상

동영상에는 Java/Kotlin이 포함됩니다.


<br />

## 마무리

Android MVP 무작정 따라하기는 이번글을 마지막으로 기본적인 내용을 모두 다루었습니다.

앞으로 작성하는 글은 다음과 같은 형태입니다.

- MVP 어느정도 범위로 적용할 수 있을까?
- View와 Model을 좀 더 명확하게 구분할 수 있는 방법은?
- Model 정의를 구글 Architecture/ios Viper 처럼 만들어야 할까?
- Google Architecture 정의하는 ViewModel이란?

등을 살펴보고 MVP 작성한 부분에 대한 테스트 코드 작성을 순차적으로 작성해보려고 합니다.

지금과 마찬가지로 일부 동영상이 포함되도록 진행해보겠습니다.


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


[model]: /images/androiddev/2017/2017-01-24-Android-MVP-Google-Architecture-Model/model.png
