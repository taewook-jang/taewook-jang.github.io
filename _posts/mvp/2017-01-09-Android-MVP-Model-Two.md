---
layout: post
title: Android MVP 무작정 따라하기 - Google Architecture의 Model
categories: [AndroidDev]
tags: [Android, AndroidDev, MVP]
fullview: false
comments: true
published: true
---

MVP 무작정 따라하기 9 번째 글입니다.

오늘은 구글 Architecture에서 설명하는 모델 정의를 살펴보려고 합니다.

- [가장 기본적인 Model 정의](https://github.com/googlesamples/android-architecture/tree/todo-mvp/)
- [Loader을 추가한 Model 정의](https://github.com/googlesamples/android-architecture/tree/todo-mvp-loaders/)
- [Clean 코드를 위한 정의](https://github.com/googlesamples/android-architecture/tree/todo-mvp-clean/) : Presenter Layout/Domain Layout/Data Layout 통해서 Clean 코드를 구현한다

위와 같은 3가지 정도로 구분할 수 있는데 기본적인 Model을 정의해야 Loader/Clean 코드를 적용해 볼 수 있습니다.

그래서 오늘은 Google Architecture의 가장 기본적인 Model 정의를 살펴보겠습니다.


<br />

## Model

이전 글에서 설명하였듯 모델 정의 자체는 간단합니다.

구글에서 설명하는 Model은 ios viper라는 개념과 동일하다고 생각하시면 될 것 같습니다.

원래는 Google Architecture clean 코드까지 viper 적용이라고 보이지만 viper와 유사한 형태입니다.

Interactor에서 `Google Repository` 역할을 처리하게 됩니다.

- 출처 : [ios viper](https://www.ckl.io/blog/ios-project-architecture-using-viper/)

![ios_viper]

비슷하지만 용어도 다르고, 실제 구성이 다르므로 여기까지만...


<br />

## Google Architecture Model

구글에서 설명하고 있는 Model 정의는 아래와 같습니다.

- Repository : 로컬/서버 중 어떤 데이터를 불러올지 정의하고, 메모리 캐시를 포함한다.
- Remote data source : 서버에서 데이터를 받아온다
- Local data source : 로컬에서 데이터를 받아온다

가장 기본적으로는 위와 같습니다. 로컬/서버에서 받아오는 데이터는 `Repository`에서 캐시 처리할 수 있습니다.

필요에 따라서 캐시를 하고, 이를 Presenter에 다시 콜백을 해주게 됩니다.

RxJava로 구성을 하면 `async/await` 형태로 만들어지므로, `return Observable/Flowable`의 형태가 만들어질 수 있습니다.

그래서 가장 basic MVP는 아래의 그림을 가집니다.

![todo-mvp]

여기에서 Repository이 왼쪽과 같이 표현될 수 있습니다.

Presenter와 Repository 사이의 데이터를 가공할 수 있는 Loader가 추가될 수도 있고, Clean 코드를 위한 `Domain Layout`이 포함될 수 있습니다.

이런 Loader와 Domain Layout 정의는 clean 코드를 위한 정의입니다. 필요할 경우에 추가하여 사용이 가능해집니다.


<br />

## TODO MVP

모델의 정의는 다음과 같은 형태로 생성됩니다.

- Presenter 생성하는 위치에서 Repository 생성
- Presenter에서 Model(Repository) 포합(setter)
- Presenter에서 Model(Repository) 호출
- Model(Repository)는 Remote/Local의 데이터를 선택하고, 이를 캐쉬
- 데이터 호출이 완료되면 Loader 또는 Presenter에서 세팅한 Listener에 값을 전달

추가로 Loader의 정의는 아래와 같습니다.

![todo-mvp-loader]


<br />

## 주요 코드

[ImageSource - GitHub](https://github.com/taehwandev/Kotlin-Udemy-Sample/blob/master/app-java/src/main/java/tech/thdev/java_udemy_sample/data/source/image/ImageSource.java)

Repository의 interface 정의

LoadImageCallback을 통해서 성공했을 때와 오류 났을 때를 구분하여 Listener을 추가합니다.

**RxJava를 사용할 때는 다음이 필요치 않습니다.**

```java
public interface ImageSource {

    interface LoadImageCallback {
        void onImageLoaded(List<PhotoItem> photoItems);
        void onDataNotAvailable();
        void onLoadFail(int code, String message);
    }

    void getImageItems(int page, LoadImageCallback loadImageCallback);
}
```

Source 상속받아서 Repository와 Remote/Local DataSource 구분을 하면 됩니다.

<br />

### Repository

- [ImageRepository - GitHub](https://github.com/taehwandev/Kotlin-Udemy-Sample/blob/master/app-java/src/main/java/tech/thdev/java_udemy_sample/data/source/image/ImageRepository.java)

Repository은 싱글톤을 통해서 생성하면 됩니다.

생성자를 통해 Remote/Local DataSource 생성을 하여 사용할 수 있습니다.

kotlin에서는 object class 생성을 처리할 수 있습니다.

```java
public class ImageRepository implements ImageSource {

    private ImageRemoteDataSource imageRemoteDataSource;

    private static ImageRepository imageSampleRepository;

    public static ImageRepository getInstance() {
        if (imageSampleRepository == null) {
            imageSampleRepository = new ImageRepository();
        }

        return imageSampleRepository;
    }

    private ImageRepository() {
        imageRemoteDataSource = ImageRemoteDataSource.getInstance();
    }

    @Override
    public void getImageItems(int page, final LoadImageCallback loadImageCallback) {
        imageRemoteDataSource.getImageItems(page, loadImageCallback);
    }
}
```

<br />

### Remote/Local DataSource

서버/로컬 데이터 소스를 정의할 수 있습니다.

여기에서는 Local SQLite/Realm DB를 사용하여 구성할 수 있고, Retrofit을 사용하여 서버 데이터를 불러올 수 있습니다.

역시 DataSource interface 상속을 통해서 정리하면 됩니다.

[ImageRemoteDataSource - GitHub](https://github.com/taehwandev/Kotlin-Udemy-Sample/blob/master/app-java/src/main/java/tech/thdev/java_udemy_sample/data/source/image/ImageRemoteDataSource.java)

```java
public class ImageRemoteDataSource implements ImageSource {

    private static ImageRemoteDataSource INSTANCE;

    private final FlickrService flickrService;

    private ImageRemoteDataSource() {
        flickrService = RetrofitCreator.createRetrofit().create(FlickrService.class);
    }

    public static ImageRemoteDataSource getInstance() {
        if (INSTANCE == null) {
            INSTANCE = new ImageRemoteDataSource();
        }

        return INSTANCE;
    }

    @Override
    public void getImageItems(int page, final LoadImageCallback loadImageCallback) {
      // 생략
    }
}
```


<br />

## 마무리

구글 Architecture TODO MVP를 간단하게 살펴보았습니다.

다음 글에서 직접 구현해보도록 하겠습니다.


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


[ios_viper]: /images/mvp/2017-01-09-Android-MVP-Model-Two/ios_viper.png
[todo-mvp]: /images/mvp/2017-01-09-Android-MVP-Model-Two/todo-mvp.png
[todo-mvp-loader]: /images/mvp/2017-01-09-Android-MVP-Model-Two/todo-mvp-loader.png
