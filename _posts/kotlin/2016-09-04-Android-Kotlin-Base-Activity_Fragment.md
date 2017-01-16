---
layout: post
title: Android Kotlin으로 작성한 Activity/Fragment 살펴보기
categories: [AndroidDev, Kotlin]
tags: [Android Kotlin, Android, Kotlin]
fullview: false
comments: true
published: true
---

Kotlin을 공부하면서 Android base 작업을 해보았습니다.

이 작업한 Base는 MVP 모델을 사용하기 위한 Base로 만들었고, Adapter와 Activity를 사용하기 위한 기본적인 Util 형태를 포함하고 있습니다.

**그간 제가 정리하였던 글 대부분이 이 글과 연관성을 가지고 작성하였던 글 들입니다.**

Base 정리가 끝나면 Kotlin으로 작성한 Photo 예제를 순차적으로 작성해보도록 하겠습니다.

<br />

Base 관련 글은 아래와 같이 정리하려고 합니다.

- Android Kotlin으로 작성한 Activity/Fragment 살펴보기
- Android Kotlin으로 작성한 Presenter/View 살펴보기
- Android Kotlin으로 작성한 Adapter 살펴보기

**Util은 필요에 따라 추가하므로 위의 글을 소개함과 함께 간단하게 추가하겠습니다.**

이 글에 사용된 Base 코드는 GitHub에 [Android Base](https://github.com/taehwandev/AndroidBase)로 올려두었습니다.


<br />

## 코틀린 Base 배포

현재 코틀린으로 작성한 Base는 Bintray가 운영하는 JCenter에 배포 중에 있습니다.

이 글을 작성하는 현재 최신 버전은 다음과 같습니다.

[ ![Download](https://api.bintray.com/packages/taehwandev/thdev.tech/base/images/download.svg) ](https://bintray.com/taehwandev/thdev.tech/base/_latestVersion)

```
compile 'tech.thdev.support:base:1.0.1'
```

이후에도 계속적으로 버전이 올라갈 수 있도록 하려고 합니다. 아래의 링크를 통해 최신 버전 확인이 가능합니다.

- <a href="http://thdev.tech/androiddev/2016/09/01/Android-Bintray(JCenter)-Publish.html">Android Module을 Bintray(JCenter)에 배포하는 방법</a>
- [배포 중인 전체 소스코드](https://github.com/taehwandev/AndroidBase)

<a href='https://bintray.com/taehwandev/thdev.tech/base?source=watch' alt='Get automatic notifications about new "base" versions'><img src='https://www.bintray.com/docs/images/bintray_badge_color.png'></a>

AndroidBase로 최신 코드가 공개되며, Bintray를 통해 배포합니다. 이에 따라 예제도 필요에 따라 추가할 예정입니다.


<br />

## MVP의 Base

Activity와 Fragment는 MVP Base로 작성됩니다. MVP는 구글의 Android Architecture를 기준으로 작성할 수 있도록 만들었습니다. 이 글의 다음 글을 작성할 때 자세하게 작성하도록 하겠습니다.

제가 작성하는 MVP는 아래 글을 참고해주시면 되겠습니다.

- [Android TODO MVP 어떻게 적용할까?](androiddev/2016/06/14/Android-TODO-MVP-Example.html)


<br />

## Kotlin으로 작성한 Activity

- [BaseActivity.kt](https://github.com/taehwandev/AndroidBase/blob/master/base/src/main/java/tech/thdev/base/view/BaseActivity.kt)

`AppCompatActivity`을 상속받아서 `abstract`으로 `BaseActivity`을 구현하였습니다.

별다른 부분은 없고 `BaseActivity`에는 `ButterKnife`을 초기화하게 됩니다.

실제로 `Kotlin`으로 작성되는 경우에는 [`ButterKnife`](http://jakewharton.github.io/butterknife/)을 사용하지 않겠지만 `Java`로 작성되는 Activity에서는 ButterKnife을 사용하게 됩니다.

`ButterKnife`을 사용을 위해서 아래와 같이 `setContentView`에 `ButterKnife.bind()`를 호출하게 됩니다.

```
abstract class BaseActivity : AppCompatActivity() {

    override fun setContentView(layoutResID: Int) {
        super.setContentView(layoutResID)
        ButterKnife.bind(this@BaseActivity)
    }
}
```

java에서는 `ButterKnife.bind()`에 `this`로 표현을 하게 되지만 Kotlin에서는 `this@ClassName`을 사용하게 됩니다.

Kotlin의 `this` 표현식에 대한 상세한 내용은 다음의 링크를 확인하시면 되겠습니다.

- [Kotlin This Expression](http://kotlinlang.org/docs/reference/this-expressions.html)


<br />

### Java에서 BaseActivity을 사용하려면?

저는 다음을 dependencies에 추가하고 사용하게 되는데

```
compile 'tech.thdev.support:base:1.0.1'
```


[MainActivity.java](https://github.com/taehwandev/AndroidBase/blob/master/sample/src/main/java/tech/thdev/sample/MainActivity.java) 전체 코드에서 다음과 같이 상속받아 사용하게 되는데 코틀린과 Java에서 조금 다르게 표현됩니다.

```java
public class MainActivity extends BaseActivity {
  @Override
  protected void onCreate(@Nullable Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity_main);
  }
}
```

`MainActivity`에서 `setContentView`를 호출하게 되면 자동으로 `ButterKnife.bind()`을 하게 됩니다. 그래서 별도의 처리를 할게 없어지죠.

해당 Activity에서는 `BindView`를 그냥 사용할 수 있게 됩니다.


<br />

### Kotlin에서 BaseActivity 사용법

코틀린에서는 아래와 같이 사용이 가능합니다.

하지만 `setContentView`을 호출하더라도 `ButterKnife`을 사용하지는 않습니다.

`ButterKnife`이 없어도 `kotlin extension`을 사용하면 가장 쉽게 접근할 수 있어 `ButterKnife`을 사용하지는 않아도 됩니다.

```java
class MainActivity : BaseActivity() {
  override fun onCreate(savedInstanceState: Bundle?) {
      super.onCreate(savedInstanceState)
      setContentView(R.layout.activity_main)
  }
}
```


<br />

## Fragment을 호출하기 위한 Util

Fragment을 사용하기 위한 Util이 있습니다. Kotlin의 [`Infix notation`](/kotlin/2016/08/07/Kotlin-Idioms.html#infix-notation)을 활용하게 되는데 다음과 같이 Util을 생성하였습니다.

`AppCompatActivity`을 사용하는 경우와 `Activity`을 사용하는 경우 2가지로 구분해서 만들었습니다. `supportFragmentManager`을 사용하여 replace를 통해 Fragment를 바로 등록하게 됩니다.

기존 `fragmentManager`을 사용하여서도 같은 방법을 사용하게 됩니다.


```java
fun AppCompatActivity.replaceContentFragment(@IdRes frameId: Int, fragment: Fragment) {
    supportFragmentManager.beginTransaction().replace(frameId, fragment)?.commit()
}

fun Activity.replaceContentFragment(@IdRes frameId: Int, fragment: android.app.Fragment) {
    fragmentManager.beginTransaction().replace(frameId, fragment).commit()
}

```

Activity에서는 다음과 같이 사용이 가능합니다.

**Java에서 사용법**

- [replaceContentFragment](https://github.com/taehwandev/AndroidBase/blob/master/sample/src/main/java/tech/thdev/sample/MainActivity.java#L20)

```java
MainFragment mainFragment = new MainFragment();
ActivityUtilKt.replaceContentFragment(this, R.id.frame_layout, mainFragment);
```

**Kotlin에서 사용법**

Java에 비해서 Kotlin에서 간결하게 처리가 됩니다. 이미 `AppCompatActivity`을 상속받고 있기 때문에 별도의 this를 해줄 필요가 없습니다.

```java
val fragment: MainFragment = MainFragment()
setContentFragment(R.id.frame_layout, fragment)
```


<br />

## <a name="basefragment"></a> BaseFragment

- [BaseFragment.kt](https://github.com/taehwandev/AndroidBase/blob/master/base/src/main/java/tech/thdev/base/view/BaseFragment.kt)

BaseFragment에는 Android Architecture를 이용할 수 있도록 `BasePresenter`를 Generic으로 사용하고 있습니다. 기본으로 `v4.Fragment`를 상속받고 있고, `BaseView`를 함께 상속받고 있습니다.

```java
abstract class BaseFragment<P : BasePresenter<*>>() : android.support.v4.app.Fragment(), BaseView<P> {
}
```

`onCreateView`와 `onViewCreate`를 구분하여 사용하게 됩니다.

onCreateView에서는 Fragment에서 사용할 View를 초기화하도록 하였고, 별도 Layout을 강제할 수 있도록 `getLayout`을 만들어두었습니다.

```java
override fun onCreateView(inflater: LayoutInflater?, container: ViewGroup?, savedInstanceState: Bundle?): View? {
    return inflater?.inflate(getLayout(), container, false)
}

abstract fun getLayout(): Int
@LayoutRes
```

`onViewCreated`에서 ButterKnife을 bind 하게 됩니다. Activity와 같이 `this@BaseFragment`을 포함합니다.

그리고 Kotlin의 안전한 널 처리를 함께 하기 위해 [`let`](/kotlin/2016/08/04/Kotlin-Null-Safety.html#safe-null-call)을 사용하게 됩니다.

```java
override fun onViewCreated(view: View?, savedInstanceState: Bundle?) {
    super.onViewCreated(view, savedInstanceState)
    view?.let {
        ButterKnife.bind(this@BaseFragment, it)
    }
}
```

저는 이 `BaseFragment`에서 `Presenter`를 가지게 만들었습니다. 제가 만든 `Base`는 모두 `MVP`구조를 가지게 됩니다. 그래서 아래와 같은 코드가 추가되어 있습니다.

다음의 구조를 기본으로 가지고 있습니다.

```java
protected var presenter: P? = null
    private set

override fun onPresenter(presenter: P) {
    this.presenter = presenter
}

override fun onDestroy() {
    super.onDestroy()

    presenter?.detachView()
}
```

`Android Architecture`의 기준대로 `Activity`에서 `Presenter`을 만들고, 이를 `Fragment`에서 받아서 처리하게 됩니다.

![android-mvp]

대략 위의 그림과 같습니다.


<br />

### Fragment을 상속받는 Java 클래스

java에서는 다음과 같이 사용이 가능한데 ButterKnife를 포함하고 있고, `getLayout`을 구현해주면 됩니다.

```java
public class MainFragment extends BaseFragment<MainContract.Presenter> implements MainContract.View {

  @BindView(R.id.textView)
  TextView textView;

  @Override
  public int getLayout() {
      return R.layout.fragment_main;
  }
}
```

<br />

### Kotlin에서 Fragment 상속 구현하기

`Kotlin`에서는 `lazy`를 사용하여 `findViewById`을 할 수 있고, `kotlin extension`을 이용하여 사용도 가능합니다. 저는 아직 `kotlin extension`을 사용하지 않고 있어서 다음과 같이 작성할 수 있습니다.

```
class MainFragment : BaseFragment<MainContract.Presenter>(), MainContract.View {

  private val textView by lazy {
      view?.findViewById(R.id.textView) as TextView
  }

  override fun getLayout() = R.layout.fragment_main
}
```


<br />

## 마무리

대략 Base로 작성한 `Activity`와 `Fragment`을 작성 부분을 정리해보았습니다.

다음 글에서 Presenter와 Adapter 들도 정리해보겠습니다.

일단은 제가 편하게 쓸 수 있는 용도로 만들어서 공용성은 떨어질 수 있습니다.

Base는 자기가 편한 대로 쓰게 되어 있다 보니... 표준적인 것은 없습니다.

일단 Java가 아니라 `Kotlin`을 이용하여 Base를 만들어본 이유는 다음과 같습니다.

- Kotlin에서도 함께 쓰기 쉽도록 하기 위함(사실 코드가 짧아서 편합니다.)
- Java에서의 호환성

2가지로 정리하긴 했지만 일단은 Java에서보다 편하게 쓰는게 가능하고, 호환도 문제없어서 Base를 작성해보았습니다.

읽어주셔서 감사합니다.


<br />

## 코틀린 관련 포스트 목록

- [Android Kotlin 시작하기](http://thdev.tech/androiddev/kotlin/2016/07/31/Kotlin-Android-Start.html)
- [Kotlin 기본 타입](http://thdev.tech/kotlin/2016/10/02/Kotlin-Basic-Types.html)
- [Kotlin 기본 문법 - 1](http://thdev.tech/kotlin/2016/08/02/Basic-Kotlin-01.html)
- [Kotlin 안전한 null 처리](http://thdev.tech/kotlin/2016/08/04/Kotlin-Null-Safety.html)
- [Kotlin 기본 문법 - 2](http://thdev.tech/kotlin/2016/08/07/Kotlin-Idioms.html)
- [Kotlin Generics 사용](http://thdev.tech/kotlin/android/2016/09/08/Kotlin-Generics.html)
- [kotlin 주요 class](http://thdev.tech/kotlin/2016/10/09/Kotlin-Class.html)
- [Kotlin Function - default, 변수 순서 변경](http://thdev.tech/kotlin/2017/01/15/Kotlin-Function.html)
- [Kotlin constructor init 이란?](http://thdev.tech/kotlin/2017/01/17/Kotlin-Constructor-Init.html)

### 추가 내용

- [Android Kotlin으로 작성한 Activity/Fragment 살펴보기](/androiddev/kotlin/2016/09/04/Android-Kotlin-Base-Activity_Fragment.html)
- [Android Kotlin으로 작성한 Presenter/View 살펴보기](/androiddev/kotlin/2016/09/22/Android-Base-Presenter_View.html)
- Android Kotlin으로 작성한 Adapter 살펴보기



[android-mvp]:  /images/2016/2016-09-04-Android-Kotlin-Base-Activity_Fragment/android-mvp.png
