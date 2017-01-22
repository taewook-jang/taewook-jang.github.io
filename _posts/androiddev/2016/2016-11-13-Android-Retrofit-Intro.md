---
layout: post
title: Android HTTP 통신을 위한 Retrofit 사용하기
categories: [AndroidDev]
tags: [Android, API]
fullview: false
comments: true
published: true
---

Android Retrofit을 소개합니다.

Retrofit은 HTTP REST API 구현을 위한 라이브러리입니다.

[jakewharton](https://github.com/jakewharton)이 소속된 [Square, Inc](http://square.github.io/)의 오픈소스 라이브러리입니다.

- [Square, Inc](http://square.github.io/)에는 많이 들어보셨을 `otto`, `dagger`, `picasso`, `OkHTTP` 등을 오픈소스로 배포 중입니다.
- [REST](https://ko.wikipedia.org/wiki/REST) : Representational State Transfer의 약자로, 웹에서 사용하는 Architecture의 한 형식입니다. 네트워크 상에서 클라이언트와 서버 간의 통신 방식을 말합니다.
	- HTTP에서는 GET, POST, PUT, DELETED 등의 Method를 제공합니다.
	- 클라이언트에 대한 응답은 xml, json, text, rss 등으로 전달하게 됩니다.
	- [What exactly is RESTful programming?](http://stackoverflow.com/questions/671118/what-exactly-is-restful-programming)


<br />

## Retrofit

Retrofit은 REST API을 구현한 상태입니다.

그래서 간단하게 GET, POST, PUT, DELETED 등을 전달하면 서버에서 처리 후 xml, json, text, rss 등으로 응답을 제공받을 수 있는 형태입니다. (실제 서버에서 응답 오는 방법은 다를 수 있습니다.)


### Retrofit 적용하기

- [Retrofit 설명 페이지](https://square.github.io/retrofit/)

Gradle와 JAR를 모두 제공하고 있으며, Android Studio의 build.gradle에 다음과 같이 추가합니다.

```
compile 'com.squareup.retrofit2:retrofit:(insert latest version)'
```


### Introduction

Retrofit은 `interface`을 정의하고, 이 `interface class`을 Retrofit에 초기화를 하는 과정을 거치면, `HTTP` 통신을 할 준비가 완료됩니다.

아래는 GitHub에서 제공하는 API 중 `user`와 관련된 부분을 불러오는 부분입니다.

interfaceService로 정의하고, `@GET` annotation을 추가하고 다음을 정의하면 됩니다.

```java
public interface GitHubService {
  @GET("users/{user}/repos")
  Call<List<Repo>> listRepos(@Path("user") String user);
}
```

다음과 같이 Retrofit을 초기화합니다. 간단하게 `baseUrl`을 적어서 초기화할 수 있습니다.

Retrofit을 생성하면, 위에서 생성한 `GitHubService`을 retrofit을 통해 생성합니다.

이후부터는 `service`을 통해서 `Retrofit`을 사용하게 됩니다.

```java
Retrofit retrofit = new Retrofit.Builder()
    .baseUrl("https://api.github.com/")
    .build();

GitHubService service = retrofit.create(GitHubService.class);
```

그 외 `@GET`, `@POST`, `@PUT`, `@DELETED`, `@HEAD` 등의 자료는 Retrofit API 문서를 참고하시면 되겠습니다.(실제 서버와 통신하는 방법에 따라서 달라지는 부분이 되겠습니다.)

- [Retrofit API](https://square.github.io/retrofit/)


<br />

## ConvertFactory 이용하기

대부분의 서버에서는 응답으로 `json`을 주로 이용합니다.

retrofit은 이러한 응답을 간단하게 변환할 수 있도록, 다양한 Converter을 제공하고 있습니다. 바로 `addConverterFactory()`로 추가하는 방법입니다.

Retrofit에서 소개하고 있는 간단한 예제를 동작하면 아래와 같이 오류가 발생할 수 있습니다.

```
Caused by: java.lang.IllegalArgumentException: Unable to create converter for class tech.thdev.customlistviewsample.data.GitHubUserResponse
```

`Converter` 초기화 안되어서 나타나는 오류입니다.

이번 글에서는 GsonConverterFactory을 추가하는 방법을 정리합니다.


### GsonConverterFactory 추가하기

GsonConverterFactory을 사용하기 위해서는 먼저 `build.gradle`에서 다음을 추가합니다.

```
compile 'com.squareup.retrofit2:converter-gson:(insert latest version)'
```

그리고 retrofit 초기화 코드에 `addConverterFactory`을 다음과 같이 추가해주시면 됩니다.

```java
Retrofit retrofit = new Retrofit.Builder()
    .baseUrl("https://api.github.com")
    .addConverterFactory(GsonConverterFactory.create())
    .build();
```

이렇게 하면 다음과 같은 `interface` 정의를 사용할 수 있습니다.

```java
public interface GitHubService {
  @GET("users/{user}/repos")
  Call<List<Repo>> listRepos(@Path("user") String user);
}
```

Repo 객체도 당연히 구현되어 있어야 합니다.

아래는 data 객체 예입니다.

```kotlin
data class Repo(val name: String)
```

### Retrofit에서 응답받은 데이터 처리하기

Retrofit과 생성한 interface을 연결하였습니다.

응답받는 곳에서는 다음과 같이 처리할 수 있습니다.

다음과 같이 `searchGitHubUser`을 호출하였습니다.

이때 Retrofit은 알아서 `Thread`을 처리하고, 동기화로 데이터를 넘겨줍니다.

받아온 데이터를 `enqueue`을 통해서 `onResponse`와 `onFailure`을 사용합니다.

`onResponse`의 경우 서버에서 정의하는 success가 아닌 경우에 대한 예외 처리를 포함한 코드 작성을 해주시면 되겠습니다.

`GsonConverterFactory`을 설정하였기 때문에 `response.body()`는 다음과 같이 처리가 가능합니다.

```java
final Call<GitHubUserResponse> gitHubUserCall = retrofitGitHub.searchGitHubUser(userKeyword, ++page, DEFAULT_ITEM_COUNT);
        gitHubUserCall.enqueue(new Callback<GitHubUserResponse>() {
            @Override
            public void onResponse(Call<GitHubUserResponse> call, Response<GitHubUserResponse> response) {
                if (!response.isSuccessful()) {
                    return;
                }

								// 정상 응답
                // Retrofit에서 GSON을 GitHubUserReponse로 변환한 결과를 받아온다
                GitHubUserResponse gitHubUserResponse = response.body();
            }

            @Override
            public void onFailure(Call<GitHubUserResponse> call, Throwable t) {
                // 응답 실패(네트워크 오류 등)
            }
        });
```


<br />

## RxJava와 함께 사용하기

Retrofit은 [ReactiveX](http://reactivex.io/)을 통한 응답도 가능합니다.

### RxJava 1.x 사용하기

Retrofit에서 RxJava을 사용하기 위해서는 dependencies에 `retrofit2 rxjava`을 추가해주어야 합니다.

```
compile 'com.squareup.retrofit2:adapter-rxjava:(insert latest version)'
```

위와 같이 추가하고, `interface`에는 다음과 같이 `Observable<Repo>`을 정의해주시면 되겠습니다.

- 다음의 예제는 kotlin입니다.

```kotlin
@GET("/search/users?")
fun searchUser(
        @Query(value = "q", encoded = true) userKeyword: String,
        @Query("page") page: Int,
        @Query("per_page") perPage: Int): Observable<GitHubUserSearchResponse>
```

RxJava을 사용하기 위해서는 다음과 같이 `addCallAdapterFactory`에 `RxJavaCallAdapterFactory.create()`을 해주면 됩니다.

```java
Retrofit retrofit = new Retrofit.Builder()
    .baseUrl("https://api.github.com")
		.addCallAdapterFactory(RxJavaCallAdapterFactory.create())
    .addConverterFactory(GsonConverterFactory.create())
    .build();
```

이후에는 RxJava을 사용하기 위함이므로 다음을 추가로 추가해주시면 되겠습니다.

- [RxJava](https://github.com/ReactiveX/RxJava)
- [RxAndroid](https://github.com/ReactiveX/RxAndroid)

```
compile "io.reactivex:rxandroid:1.2.2"
compile "io.reactivex:rxjava:1.2.1"
```


### ReactiveX 2.0 사용하기

최근에 2.0도 정식으로 Release되었습니다.

- [RxJava 2.0](https://github.com/ReactiveX/RxJava/tree/2.x)
- [RxAndroid 2.0](https://github.com/ReactiveX/RxAndroid/tree/2.x)

```
compile "io.reactivex:rxandroid:2.0.1"
compile "io.reactivex:rxjava:2.0.1"
```

Retrofit에서 RxJava 2.0을 함께 사용하려면 다음을 추가해주시면 되겠습니다.

- [JakeWharton Retrofit RxJava2 Adapter](https://github.com/JakeWharton/retrofit2-rxjava2-adapter)

```
compile 'com.jakewharton.retrofit:retrofit2-rxjava2-adapter:1.0.0'
```

그리고 기존 `addCallAdapterFactory`을 RxJava2CallAdapterFactory으로 변경해주시면 되겠습니다.

```java
Retrofit retrofit = new Retrofit.Builder()
    .baseUrl("https://api.example.com")
    .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
    .build();
```

그리고 다음과 같이 초기화가 가능합니다.

RxJava2부터는 `Observable` 말고도 `Flowable`을 사용하라고 하더군요.

`Observable`에서는 `MissingBackpressureException`이 발생한다고 합니다. 이러한 부분을 해결하기 위해서 `Flowable`을 추가하였다고 합니다.

그래서 자세한 내용은 RxJava2에서 달라진 부분을 참고하시면 되겠습니다.

- [Observable and Flowable](https://github.com/ReactiveX/RxJava/wiki/What's-different-in-2.0#observable-and-flowable)

```kotlin
interface GitHubInterface {

// https://api.github.com/search/users?q=tom+repos:%3E42&page=3&per_page=50
@GET("/search/users?")
fun searchUser(
        @Query(value = "q", encoded = true) userKeyword: String,
        @Query("page") page: Int,
        @Query("per_page") perPage: Int): Flowable<GitHubUserSearchResponse>
}
```


<br />

## OkHTTP을 함께 사용하여 Retrofit의 로그 보기

OkHttp 역시 HTTP 통신을 할 수 있는 라이브러리입니다.

Retrofit과 함께 사용하면 Retrofit에서 통신과정의 로그를 확인이 가능합니다.

- [OkHttp](http://square.github.io/okhttp/)


### 로그를 보기 위한 OkHttp dependencies 추가

다음과 같이 2개의 `dependencies`을 추가합니다.

```
// OkHttp
compile 'com.squareup.okhttp3:okhttp:3.4.2'
compile 'com.squareup.okhttp3:logging-interceptor:3.4.2'
```

### 코드 추가하기

다음과 같이 `HttpLoggingInterceptor`을 생성합니다.

간단하게 로그의 Level을 지정하고, 이를 `OkHttpClient.Builder`에 추가합니다.

```java
private static OkHttpClient createOkHttpClient() {
    OkHttpClient.Builder builder = new OkHttpClient.Builder();
    HttpLoggingInterceptor interceptor = new HttpLoggingInterceptor();
    interceptor.setLevel(HttpLoggingInterceptor.Level.BODY);
    builder.addInterceptor(interceptor);
    return builder.build();
}
```

그리고 아래와 같이 `client`에 OkHttp을 추가해주시면 되겠습니다.

```java
public static Retrofit createRetrofit() {
    return new Retrofit.Builder()
            .baseUrl("https://api.flickr.com/services/rest/")
            .addConverterFactory(GsonConverterFactory.create())
            .client(createOkHttpClient())
            .build();
}
```

그러면 아래와 같은 로그 정보를 확인할 수 있습니다.

```
D/OkHttp: <-- 200
D/OkHttp: date: Sun, 13 Nov 2016 02:02:06 GMT
D/OkHttp: content-type: application/json
// 생략
D/OkHttp: {"photos":{"page":1,"pages":5,"perpage":100,"total":500,
// 생략
D/OkHttp: <-- END HTTP (16347-byte body)
```


<br />

## 마무리

Retrofit을 간단하게 정리해보았습니다.

제가 그간 작성하였고, 스터디 참여한 예제들을 아래와 같이 정리하였습니다.

각각 Retrofit 사용 방법이 다르므로 필요한 부분을 살펴보시면 되겠습니다.

- [Retrofit 샘플 - Java](https://github.com/taehwandev/Android-BlogExample/blob/master/2016-10-30-CustomListViewSample/src/main/java/tech/thdev/customlistviewsample/network/RetrofitCreator.java)
	- 기본 코드를 통해서 작성한 Retrofit 예제입니다.
- [Retrofit 샘플 - RxJava 1.x](https://github.com/delicious-mvp/Delicious/blob/master/app/src/main/java/com/delicious/delicious/network/RetrofitCreator.java)
	- 제가 참여한 스터디에서 함께 공부한 MVP에 대한 예제입니다. RxJava 1.x 버전을 사용합니다.
- [Retrofit 샘플 - RxJava 2.0 - kotlin](https://github.com/taehwandev/AndroidRecyclerView/blob/01-TDD-Sample/sample/src/main/kotlin/tech/thdev/androidrecyclerview/network/RetrofitCreator.kt)
	- RxJava 2.0을 이용한 Retrofit 예제입니다.
