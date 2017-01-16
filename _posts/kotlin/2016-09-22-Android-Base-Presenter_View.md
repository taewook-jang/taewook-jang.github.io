---
layout: post
title: Android Kotlin으로 작성한 Presenter/View 살펴보기
categories: [AndroidDev, Kotlin]
tags: [Android Kotlin, Android, Kotlin]
fullview: false
comments: true
published: true
---

Android에서 사용하기 위한 Base 코드 2번째 글입니다.

읽기 전에 미리 읽어주심을 추천합니다.

- [Android TODO MVP 어떻게 적용할까?](http://thdev.tech/androiddev/2016/06/14/Android-TODO-MVP-Example.html)
- [Kotlin Generics 사용하기](/kotlin/android/2016/09/08/Kotlin-Generics.html)


<br />

## MVP 적용은?

MVP는 다음과 같이 적용하였습니다.

`Activity`/`Fragment`에 대하여 각각 `Presenter`를 초기화합니다.

`Presenter`는 [`AbstractPresenter`](https://github.com/taehwandev/AndroidBase/blob/master/base/src/main/java/tech/thdev/base/presenter/AbstractPresenter.kt)를 상속받아서 구현하게 되는데 다음과 같은 함수가 [`BasePresenter`](https://github.com/taehwandev/AndroidBase/blob/master/base/src/main/java/tech/thdev/base/presenter/BasePresenter.kt)에 포함되어 있습니다.


```
fun attachView(view: VIEW)
fun detachView()
```

위의 함수들은 모두 [`BasePresenterActivity`](https://github.com/taehwandev/AndroidBase/blob/master/base/src/main/java/tech/thdev/base/view/BasePresenterActivity.kt)와 [`BasePresenterFragment`](https://github.com/taehwandev/AndroidBase/blob/master/base/src/main/java/tech/thdev/base/view/BasePresenterFragment.kt)에서 각각 호출하고 있습니다.

그래서 실제 사용할 때는 별다른 작업을 하지 않아도 되도록 작업해두었습니다.

그래서 포함되는 클래스는 MVP를 포함하는 경우와 하지 않는 `Activity`/`Fragment`가 구분하도록 수정하였습니다.

- `MVP` 기반의 `BasePresenterActivity`/`BasePresenterFragment` 기본 베이스
- `MVP`를 사용하지 않는 경우의 `BaseActivity`/`BaseFragment`


정리하면 MVP를 사용하기 위한 Presenter와 View의 정의를 설명하려고 합니다.

오늘 작성할 부분을 Diagram으로 표현하면 다음과 같습니다.

![er-diagram]


<br />

## 코틀린 Base 배포

최신 버전의 Kotlin으로 작성한 support base는 다음과 같습니다.

[ ![Download](https://api.bintray.com/packages/taehwandev/thdev.tech/base/images/download.svg) ](https://bintray.com/taehwandev/thdev.tech/base/_latestVersion)

```
compile 'tech.thdev.support:base:release_version_code'
```


<br />

## <a name="basepresenter"></a>BasePresenter

BasePresenter에는 `attachView`와 `detachView`를 포함하고 있습니다.

[전체 코드](https://github.com/taehwandev/AndroidBase/blob/master/base/src/main/java/tech/thdev/base/presenter/BasePresenter.kt)는 다음과 같습니다.

```
interface BasePresenter<in VIEW : BaseView> {

    /**
     * View Attach.
     */
    fun attachView(view: VIEW)

    /**
     * View detach
     */
    fun detachView()
}
```

`attachView`를 통해서 `View를 세팅` 받고, `detachView`를 통해서 `view를 null` 처리하게 됩니다.


<br />

## <a name="baseview"></a>BaseView

BaseView입니다. 사실 이 BaseView는 하는 일이 없습니다. interface를 만들기 위한 목적으로 사용하고 있는데 아직은 설정해주는 부분이 없습니다.

```
interface BaseView {

}
```


<br />

## AbstractPresenter

저는 Presenter를 좀 더 편하게 사용하기 위해서 다음과 같이 정의하였습니다.

`AbstractPresenter`라는 이름을 사용하고 `VIEW`를 Generic으로 사용합니다.

여기에서는 VIEW를 초기화하기 위한 `attachView`와 VIEW를 null 처리하기 위한 `detachView`를 정의하였습니다.

아래와 같이 정의하고, VIEW는 `in/out`을 모두 다 하기 때문에 별도의 함수 표기가 존재하지 않습니다.

`attachView`는 외부에서 접근할 수 없도록 하기 위해서 `private set`을 추가로 설정하였습니다. 그래서 외부에서는 set을 사용할 수 없고, get 역시 AbstractPresenter를 상속받을 경우에만 사용이 가능합니다.

```
abstract class AbstractPresenter<VIEW : BaseView> : BasePresenter<VIEW> {

    protected var view: VIEW? = null
        private set

    override fun attachView(view: VIEW) {
        this.view = view
    }

    override fun detachView() {
        view = null
    }
}
```


<br />

## Presenter를 위한 `interface` 정의

저는 Google Architecture를 사용하여 다음과 같은 interface를 정의합니다.

이름에는 `Contract`를 다음과 같이 만들어서 사용하고 있습니다.

다음과 같은 예를 확인할 수 있는데 `MainContract`를 정의한 모습입니다.

interface를 `View`와 `Presenter`를 모두 정의한 상태입니다.


```
public interface MainContract {

    interface View extends BaseView {

        void showMessage(String message);
    }

    interface Presenter extends BasePresenter<View> {

        void updateMessage();
    }
}
```


<br />

## Presenter를 정의한다

위와 같은 interface를 정의하였으니 이제는 `Presenter`를 정의해보려고 합니다.

[MainPresenter](https://github.com/taehwandev/AndroidBase/blob/master/sample/src/main/java/tech/thdev/sample/presenter/MainPresenter.java)는 다음과 같이 정의할 수 있습니다.

extends에는 `AbstractPresenter`을 정의하고, implements에는 Presenter를 또 한번 정의합니다... 조금은 귀찮네요. 줄이도록 해봐야겠습니다.

다음만 호출하게 되면 `AbstractPresenter`에서 구현한게 있으니 그대로 사용하면 됩니다.

```
public class MainPresenter extends AbstractPresenter<MainContract.View> implements MainContract.Presenter {

    @Override
    public void updateMessage() {
        getView().showMessage("MainPresenter example");
    }
}
```


<br />

## BasePresenterActivity/BasePresenterFragment 초기화 시

Presenter를 위와 같이 정의할 수 있는데 저는 Base를 이용하여 `BasePresenterActivity`/`BasePresenterFragment`을 한 번 더 정의하였습니다.

좀 더 사용하기 편하게 하기 위해서 [`Fragment`](https://github.com/taehwandev/AndroidBase/blob/master/base/src/main/java/tech/thdev/base/view/BasePresenterFragment.kt)와 [`Activity`](https://github.com/taehwandev/AndroidBase/blob/master/base/src/main/java/tech/thdev/base/view/BasePresenterActivity.kt)를 다음과 같의 정의해두었습니다.

해당 코드에서 `BasePresenter`를 상속받아서 정의하였습니다.

`BasePresenterFragment`와 `BasePresenterActivity`는 Generic으로 `VIEW`와 `Presenter`를 세팅하게 만들었습니다. 그래서 실제 구현하는 곳에서 `VIEW`와 `Presenter`의 `interface`를 세팅해주셔야 합니다. 해당 부분은 아래에서...

다음의 코드에서 Presenter를 초기화할 수 있도록 `onCreatePresenter` 함수를 별도로 만들었습니다. 강제로 상속받아서 초기화하게 되며, 하지 않으면 null입니다.

Kotlin의 장점인 `null`처리를 해주고 `attachView`에 `this as VIEW`를 그대로 세팅해주면 해당 `VIEW`를 `Presenter`에서 사용하게 됩니다.

`onDestroy`가 호출되면 자동으로 `detachView`를 호출하여 `null`처리합니다.

```
abstract class BasePresenterFragment<in VIEW : BaseView, P : BasePresenter<VIEW>> : BaseFragment() {

    protected var presenter: P? = null
        private set

    abstract fun onCreatePresenter(): P?

    override fun onCreateView(inflater: LayoutInflater?, container: ViewGroup?, savedInstanceState: Bundle?): View? {
        presenter = onCreatePresenter()
        presenter?.attachView(this as VIEW)
        return super.onCreateView(inflater, container, savedInstanceState)
    }

    override fun onDestroy() {
        super.onDestroy()

        presenter?.detachView()
    }
}
```


<br />

## Presenter를 사용하는 예제

오늘 작성한 Presenter와 View를 사용하게 되면 다음과 같이 사용하게 됩니다.

`BasePresenterActivity`와 `BasePresenterFragment`를 각각 extends로 상속받으면 되고, 다음과 같이 사용합니다.

참고로 Kotlin에서는 `Generic`을 항상 사용하게 되지만, Java에서는 생략이 가능합니다. 이 경우 `Object`로 초기화됩니다.

```
public class MainFragment
        extends BasePresenterFragment<MainContract.View, MainContract.Presenter>
        implements MainContract.View {

        @Nullable
        @Override
        public MainContract.Presenter onCreatePresenter() {
            return new MainPresenter();
        }

}
```

Kotlin에서는 다음과 같이 사용이 됩니다.

```
class MainFragment : BasePresenterFragment<MainContract.View, MainContract.Presenter>(), MainContract.View {

    override fun onCreatePresenter() = MainPresenter()
}

```


<br />

## 마무리

제가 사용하는 MVP에 대해서 정리를 해보았는데 유용할지는 모르겠습니다.

일단 제 기준으로 작성하였고, MVP의 형태도 기존과 다르게 변경하였습니다.

원래는 구글의 방식대로 Activity에서 초기화하고 Fragment(View)가 받아서 동작하게 하는 형태였지만 생각해보면 Activity에 무조건 Fragment가 붙는 형태가 아님을 생각하게 되어 오늘의 형태가 되었습니다.

**이전 글은 나중에 수정할 예정입니다.**

그래서 Fragment, Activity 어디에서든 초기화할 수 있는 Presenter의 형태를 만들게 되었습니다.

다음 글에서는 Kotlin으로 작성한 BaseRecyclerView Base 코드를 살펴보도록 하겠습니다.

감사합니다.



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


[er-diagram]: /images/2016/2016-09-22-Android-Base-Presenter_View/er-diagram.png
