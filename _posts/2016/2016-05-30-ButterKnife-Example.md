---
layout: post
title: Android ButterKnife Example
published: false
---

안드로이드 ButterKnife는 가장 많이 사용되는 UI Annotation입니다.

가장 많이 쓰이는 ButterKnife의 현재 가장 최신버전은 8.0.1입니다.

[Jake Wharton](https://github.com/JakeWharton)이 관리하는 OpenSource Project입니다.

ButterKnife 적용 방법 및 사용법을 소개하도록 하겠습니다.

<br />

# ButterKnife

- [ButterKnife 문서](http://jakewharton.github.io/butterknife/)
- [ButterKnife source](https://github.com/JakeWharton/butterknife)

ButterKnife는 아래와 같은 로고를 사용합니다.

버터로 그린 안드로보이입니다.

![logo](/images/2016/2016-05-30-ButterKnife-Example/logo.png)

## 일반적인 findViewById 사용하기

일반적으로 Layout을 사용할 경우 findViewById을 사용합니다.

해당 코드를 예제로 만들면 다음과 같습니다.

```java
@Override
protected void onCreate(@Nullable Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    TextView textOne = (TextView) findViewById(R.id.text_one);
    Button btnOne = (Button) findViewById(R.id.btn_one);
    EditText etMessage = (EditText) findViewById(R.id.et_message);
    Button btnTwo = (Button) findViewById(R.id.btn_two);
}
```

빌드를 돌린 다음의 코드는 아래와 같습니다.

R.java에 나열되어 있는 숫자코드가 맵핑되어 있습니다.

```java
protected void onCreate(@Nullable Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    this.setContentView(2130968601);
    TextView textOne = (TextView)this.findViewById(2131492977);
    Button btnOne = (Button)this.findViewById(2131492978);
    EditText etMessage = (EditText)this.findViewById(2131492979);
    Button btnTwo = (Button)this.findViewById(2131492980);
}
```

## ButterKnife을 사용할 경우

코드가 짧아지기 보단 findViewById을 사용하지 않아도 사실 편합니다.

ButterKnife을 사용하기 위한 dependencies는 아래에서 알아보고, 위의 findViewById을 수정하여보았습니다.

ButterKnife을 사용하기 위해서는 bind()를 해주어야 합니다.

bind()는 Activity/Fragment 등에서의 처리가 다릅니다.

현재 예제는 Activity에서 사용하는 방법으로 아래와 같이 ButterKnife.bind(this)를 하게 됩니다.

```java
@BindView(R.id.text_one)
TextView textView;

@BindViews({R.id.btn_one, R.id.btn_two})
List<Button> buttonList;

@BindView(R.id.et_message)
EditText etMessage;

@Override
protected void onCreate(@Nullable Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    ButterKnife.bind(this);
}
```

@BindView와 @BindViews는 다른값을 가지게 됩니다.

@BindView는 1개의 View를 가르키게 되고, @BindViews는 여러개의 View를 한번에 가질 수 있습니다.

Build 된 결과는 다음과 같이 확인할 수 있습니다.

우선 일반적인 Activity 코드는 다음과 같습니다. R.java의 숫자값만 맵핑된 모습을 확인할 수 있습니다.

```java
@BindView(2131492977)
TextView textView;
@BindViews({2131492978, 2131492980})
List<Button> buttonList;
@BindView(2131492979)
EditText etMessage;

public ButterKnifeExampleActivity() {
}

protected void onCreate(@Nullable Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    this.setContentView(2130968601);
    ButterKnife.bind(this);
}
```

ButterKnife을 통해서 BindView가 완료된 코드는 다음과 같습니다.

bind 함수에 다음과 같은 코드를 볼 수 있습니다. 자동적으로 bind와 unbinder를 생성하고있습니다.

```java
public Unbinder bind(Finder finder, T target, Object source) {
    ButterKnifeExampleActivity$$ViewBinder.InnerUnbinder unbinder = this.createUnbinder(target);
    View view = (View)finder.findRequiredView(source, 2131492977, "field \'textView\'");
    target.textView = (TextView)finder.castView(view, 2131492977, "field \'textView\'");
    view = (View)finder.findRequiredView(source, 2131492979, "field \'etMessage\'");
    target.etMessage = (EditText)finder.castView(view, 2131492979, "field \'etMessage\'");
    target.buttonList = Utils.listOf(new Button[]{(Button)finder.findRequiredView(source, 2131492978, "field \'buttonList\'"), (Button)finder.findRequiredView(source, 2131492980, "field \'buttonList\'")});
    return unbinder;
}

protected ButterKnifeExampleActivity$$ViewBinder.InnerUnbinder<T> createUnbinder(T target) {
    return new ButterKnifeExampleActivity$$ViewBinder.InnerUnbinder(target);
}

protected static class InnerUnbinder<T extends ButterKnifeExampleActivity> implements Unbinder {
    private T target;

    protected InnerUnbinder(T target) {
        this.target = target;
    }

    public final void unbind() {
        if(this.target == null) {
            throw new IllegalStateException("Bindings already cleared.");
        } else {
            this.unbind(this.target);
            this.target = null;
        }
    }

    protected void unbind(T target) {
        target.textView = null;
        target.etMessage = null;
        target.buttonList = null;
    }
}
```

대충 findRequiredView를 따라가보면

findView를 볼 수 있고,

```java
protected abstract View findView(Object source, int id);
```

getResourceEntryName을 이용하여 이름을 찾는것을 확인할 수 있습니다.

```java
protected String getResourceEntryName(Object source, int id) {
  return getContext(source).getResources().getResourceEntryName(id);
}
```

## ButterKnife dependencies 추가

8.0이되면서 기존 ButterKnife dependencies만 추가하면 되었지만 apt library도 하나 추가되었습니다.

전체 프로젝트 build.gradle에 다음의 코드를 추가합니다.

기존 jcenter()와 classpath 'com.android.tools.build:gradle:2.1.0'이 있던 곳에 다음을 추가해줍니다.

```
buildscript {
  repositories {
    mavenCentral()
   }
  dependencies {
    classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
  }
}
```

다음으로 app의 build.gradle에 다음을 추가합니다.

```
apply plugin: 'com.neenbedankt.android-apt'

dependencies {
  compile 'com.jakewharton:butterknife:8.0.1'
  apt 'com.jakewharton:butterknife-compiler:8.0.1'
}
```

위의 코드의 위치는 서로 상의하니 다음과 같이 구분하시면 됩니다.

전체 프로젝트 아래의 build.gradle에는 buildscript 코드가 포함되어 있으니 해당 부분의 위쪽 코드를 추가하시면 됩니다.

기본적으로 app 폴더에 포함된 build.gradle에 2번째 코드를 추가해주시면 됩니다.

Sync now를 해주시면 ButterKnife을 사용할 준비가 완료됩니다.

<br />

## ButterKnife 주요 코드

ButterKnife의 주요 코드를 살펴보겠습니다.

**bind**

기존에는 activity에서 unbind도 직접 작성해주어야 하였지만 8.0에서는 unbind는 없이 사용할 수 있습니다.

```java
// activity 기준
@Override
protected void onCreate(@Nullable Bundle savedInstanceState) {
   super.onCreate(savedInstanceState);
   // ...

   ButterKnife.bind(this);

   // ...
}
```

위와 같이 bind() 함수를 호출해주시면 됩니다.

그외 Fragment에서는 unbinder를 호출해주시면 되는데 다음과 같이 하시면 됩니다.

```java
// Fragment 기준
private Unbinder unbinder;

@Nullable
@Override
public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
    View rootView = inflater.inflate(R.layout.content_main, container, false);
    unbinder = ButterKnife.bind(this, rootView);
    return rootView;
}

@Override
public void onDestroy() {
    super.onDestroy();
    unbinder.unbind();
}
```

Adapter의 Holder에서는 다음과 같이 정의합니다.

Fragment에서는 unbind를 별도로 호출하였지만 holder에서는 별도 호출하지 않고 다음과 같이 처리합니다.(하는게 맞는것 같긴하지만 unbind 시점을 알 수 없으니)

```java
public BaseRecyclerView(AD adapter, View itemView) {
    super(itemView);
    this.adapter = adapter;
    ButterKnife.bind(this, itemView);
}
```
