---
layout: post
title: Android TODO MVP 어떻게 적용할까?
categories: [AndroidDev]
tags: [Android, MVP]
fullview: false
comments: true
published: true
---

저는 최근 Android 코드를 작성할 때 MVP 패턴을 적용하고 있습니다. Github에 예제로 작성한 코드에서도 MVP를 기본으로 적용하려고 하고 있습니다.

View와 Presenter, Model을 분리할 수 있다는 장점 때문에 기존 MVC 패턴에서처럼 View 코드가 짧아져서 좋았습니다. 더구나 Android test를 해볼 수 있다는 장점도 생깁니다.

Presenter를 통해서 네트워크의 데이터가 잘 받아왔는지에 대한 검증 과정도 추가할 수 있고, View에 대한 테스트도 만들어볼 순 있으니 좋습니다.

<br />

## TODO MVP를 적용하는 이유?

MVP를 안드로이드에 적용하려는 이유가 뭘까요?

- TDD의 가능성
- View와 Model 간의 구분이 가능
- View와 Model의 사용법이 분리되면서 Clean code가 가능하다

위와 같이 나열해볼 수 있습니다. 또 다른 이유가 있다면 공유 부탁드려요!

<br />

## Google에서 제공하는 Android Architecture

저는 MVP를 처음 공부해보자고 생각한 게 토스랩 정승욱(GDE이기도 합니다.)님의 [Adapter, 누구냐 넌? — Data? View?](https://medium.com/@jsuch2362/adapter-%EB%88%84%EA%B5%AC%EB%83%90-%EB%84%8C-data-view-2db7eff11c20#.ev6d22ow1)을 보고서입니다.

그래서 승욱님의 코드를 이용해서 초기 MVP를 만들어보았습니다.
dagger, RxJava, Lambda까지 포함되어있어서 초보가 보기에는 쉽지 않더군요.

여하튼... 이걸 추려서 ButterKnife 예제라고 만들어둔 코드가 있습니다. [androidTest - JUnit4, Espresso를 이용한 테스트 코드 작성](/androiddev/2016/05/04/Android-Test-Example.html)

오늘은 Google의 Android Architecture를 집중적으로 ?? 살펴보고, 제 BasePresenter 코드도 함께 공유해보겠습니다.

**Google Android Architecture**

위에 제목 적어두고...여기서 정리를...

Google에서도 Android Architecture를 정리 중에 있습니다. 바로 TODO-MVP입니다.

TODO-MVP는 구글에서 안드로이드 개발을 위해서 도움을 주려고 정리하고 있다고 합니다. 코드 정리부터 MVP의 이름 정의까지 나열해주고 있습니다.

***앞으로 표준? 적으로 많이 사용될 것으로 생각됩니다. 저도 Base를 Google TODO-MVP처럼 변경하여 사용하고 있습니다.***

Android Architecture는 가장 Basic 단계인 todo-mvp부터 todo-mvp-dagger, todo-mvp-rxjava까지 올라와 있습니다.

[Android Architecture Blueprints](https://github.com/googlesamples/android-architecture)

사실 가장 궁금한 건 Adapter에서의 MVP 적용인데 .. 이건 승욱님이 잘 정리해주셔서 이걸 참고하시면 좋을 것 같습니다.

- [Adapter, 누구냐 넌? — Data? View?](https://medium.com/@jsuch2362/adapter-%EB%88%84%EA%B5%AC%EB%83%90-%EB%84%8C-data-view-2db7eff11c20#.ev6d22ow1)
- Dagger, RxJava, Lambda를 모르신다면... 제가 작성한 코드를 보셔도 좋을 것 같습니다.
  [ButterKnifeExample](https://github.com/taehwandev/Android-BlogExample/tree/master/ButterKnifeExample)


<br />

## MVP

MVP 패턴은 1990년대 초기에 마이크로소프트에서 공개한 패턴입니다. MVC 패턴을 기반으로 둔 GUI를 처리하기 위한 패턴이라고 합니다. 다시 말해 MVC 패턴 중 하나가 MVP가 된다고 합니다.

MVC와 다르게 MVP는 Model과 View 간의 결합도를 낮추는데 초점이 맞추어져 있습니다.

![MVP](/images/2016/2016-05-03-MediaProjection-MVP-Pattern/MVP.png)

<br />

- View에서 이벤트 발생
- Presenter에 넘겨서 처리
- Data가 필요할 경우 Model에서 처리하고, 결과를 Presenter를 이용하여 View에 전달

<br />

## MVP의 구현은

그래서 전 Google Android Architecture를 이용한 Base를 우선 소개하겠습니다.

**우선 구글에서 가장 Basic MVP는 다음의 링크를 참고하시면 되겠습니다.**

- [TODO-MVP Basic](https://github.com/googlesamples/android-architecture/tree/todo-mvp/)

<br />

저는 위에서 적었듯이 승욱님의 Adapter에 적용된 MVP 패턴을 살펴보았었고, 지금은 Google의 MVP를 살펴보았습니다. 그래서 결론은? 좋은 게 좋은거니...라는것 ...

아래쪽에서 제가 만든 Base를 정리해보겠습니다.

### TODO-MVP

TODO-MVP는 다음과 같은 구조를 가지고 있습니다.

구글에서 제공하는 예제는 Activity에 Fragment를 항상 가지고 있는 예제이고, Fragment가 View가 되고, Presenter를 구현하고 있습니다.

왼쪽 영역이 Model에 해당됩니다.

![MVP](/images/2016/2016-06-14-Android-TODO-MVP-Example/mvp.png)

<br />

### BasePresenter

BasePresenter는 다음과 같이 항상 start() 함수를 가지고 있습니다.

사실 BasePresenter에는 딱히 정의할게 많지는 않은듯합니다.

```java
public interface BasePresenter {
    void start();
}
```

### BaseView

구글에서 정의한 BaseView의 형태는 다음과 같습니다.

Presenter를 setPresenter를 통해 View에 전달하는 함수가 기본값으로 추가되어 있습니다.

```java
public interface BaseView<T> {
    void setPresenter(T presenter);
}
```

구글의 샘플은 Activity에서 Presenter를 생성하고, 이를 Fragment(View)에서 전달받아 동일한 Presenter를 양쪽에서 가지고 있도록 만들었습니다.

### Presenter와 View의 정의

presenter와 View를 정의합니다. BaseView, BasePresenter를 각각 상속받아서 구현합니다.

**Contract 정의**

Contract에는 Presenter와 View를 각각 정의하고 있습니다.

형태는 다음과 같습니다. interface인 Contract에 BaseView를 상속받는 View와 BasePresenter를 상속받는 Presenter를 각각 정의합니다.

```java
public interface ClassContract {

    interface View extends BaseView<Presenter> {
        // ...

        boolean isActive();

        // ...
    }

    interface Presenter extends BasePresenter {
        // ...

        void saveTask(String title, String description);

        // ...
    }
}
```

### Presenter를 구현한다

Contract를 이용하여 View와 Presenter를 각각 구현하였습니다. 그러면 Presenter를 구현해야겠습니다.

구글식 이름 정의는 Presenter가 되겠습니다.

```java
public class AddEditTaskPresenter implements ClassContract.Presenter {

  @Override
  public void saveTask(String title, String description) {
    // TODO ...
  }
}
```

### View를 구현한다

Fragment에 View를 정의하게 됩니다.

```java
public class SampleFragment extends Fragment implements ClassContract.View {

  private ClassContract.Presenter mPresenter;

  // ...

  @Override
  public void setPresenter(@NonNull   ClassContract.Presenter presenter) {
      mPresenter = checkNotNull(presenter);
  }

  // ...
}
```

View는 위와 같이 정의하게 됩니다.

<br />

### Presenter는 어디서 생성할까요??

일단은 구글 방법으로 한다면 Activity에서 Presenter를 생성하게 됩니다.

MVP는 처음 설계 때부터 어떻게 사용할지를 정의하고 들어가면 구글 방법의 MVP가 좋을 수 있습니다.

초기 설계부터 잘되어 있다면 위와 같이 구현할 수 있지만 전 중간에 끼워 넣고 싶어졌습니다.

그래서 생각해보았습니다.

- 굳이 Activity에서 Presenter를 생성해야 할까?
- Fragment가 각각 독립적으로 Presenter를 가지면 안 될까?
- Adapter에서 Presenter를 어떻게 사용할까?

그래서 생각한 건

- Fragment 단위로 각각 Presenter/View를 가지게 사용하고 있습니다.
- Adapter의 Presenter 부분은 정승욱 님이 잘 정리해주신 게 있어서 이걸로 사용합니다.

**결론 : 욕심 많이 내지 않는다면 작은 부분(신규 개발/리팩토링)부터 MVP를 적용해볼 수 있다.**

<br />

## 제가 사용하는 Presenter Base

전 Presenter Base를 다음과 같이 구현하였습니다.

- **BasePresenter** : Google의 Base와 같습니다.
- **BaseView** : Google의 Base와 같습니다.
- **AbstractPresenter** : View를 강제하도록 새롭게 추가하였습니다.
- **ClassContract** : BasePresenter, BaseView를 상속받아서 구현합니다.(Google의 Base와 동일합니다.)
- **ClassPresenter** : ClassContract.Presenter를 상속받아 구현하고, AbstractPresenter 역시 상속받습니다.
- **View** : ClassContract.View를 상속받아서 구현합니다.
  Fragment가 될 수도 있고, Activity가 될 수도 있습니다.

**BasePresenter**

구글의 코드와 크게 다르지 않습니다.

```java
public interface BasePresenter {
    void start();
}
```

**BaseView**

setPresenter를 그대로 두었습니다. Activity에서 생성시점이 발생할 수 있기 때문이죠.

showProgress, hideProgress를 추가하였습니다. 네트워크 처리가 발생하였을 경우 항상 호출될 수 있는 부분으로 전역으로 적용하였습니다.

```java
public interface BaseView<T extends BasePresenter> {
    void setPresenter(T presenter);
    void showProgress();
    void hideProgress();
}
```

**AbstractPresenter**

새로운 추상 Presenter를 만들어보았습니다.

View를 강제하기 위해서 AbstractPresenter를 만들었습니다.

개발상 View에 대한 실수를 줄이기 위해서 만들었는데 괜찮을지 모르겠습니다.(이것도 피드백을 받아서!^^;)

```java
public abstract class AbstractPresenter<V extends BaseView> implements BasePresenter {

    private V view;

    public AbstractPresenter(V view) {
        this.view = view;

        view.setPresenter(this);
    }

    public V getView() {
        return view;
    }
}
```

AbstractPresenter까지 추가하여 생성되는 Presenter는 요렇게 생겼습니다.

extends와 implements까지 붙으니 엄청 길어지네요...

Presenter는 하나의 View 만을 사용하게 될 겁니다.라고 생각하고 이렇게 설계를 해보았습니다.

```java
public class ViewExamplePresenter extends AbstractPresenter<ViewExampleContract.View> implements ViewExampleContract.Presenter {

    public ViewExamplePresenter(ViewExampleContract.View view) {
        super(view);
    }
}
```

<br />

## 마무리

AbstractPresenter를 추가 적용함으로써 귀찮아질 수도 있습니다. 하지만 하나의 Presenter는 1개의 View를 사용할 수 있다는 전제하에 위와 같이 구현하였던 것입니다.

그래서 어느 게 더 좋다고 말하기는 애매한듯합니다. Contract를 정의하는 것도 귀찮을 수 있습니다. 하지만 새로운 사람이 왔을 때 알아보기 좋아야 좋은 코드라고 생각됩니다.

사실 문서도 많지 않으니깐요...

읽어주셔서 감사합니다. 그리고 피드백 주시면 더 좋습니다!
전 제 맘대로 Presenter를 만들어 본 것이니깐!^^;


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
