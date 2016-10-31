---
layout: post
title: Android ListView 사용하기
categories: [AndroidDev]
tags: [Android, AndroidDev]
fullview: false
comments: true
published: true
---

5년 전에 작성하였던 [Android 구글 날씨 파싱(XmlPullParser 사용)](http://thdev.net/28)을 다시 정리하였습니다.

그간 `구글 날씨 API`가 없어졌고, 안드로이드 버전도 많이 달라졌습니다.

그에 따라 새롭게 샘플을 작성하고, 정리하게 되었습니다.


<br />

## RecyclerView

- [Android ListView 사용하기](http://thdev.tech/androiddev/2016/10/30/Android-CustomListView-Sample.html)


<br />

## 그간의 변화?

- 구글 날씨가 종료되었습니다.
  - GitHub API로 대체하였습니다.
- XML보다는 json을 많이 사용하고 있습니다.
  - 구글 날씨 파싱 할 때는 `XmlPullParser`을 사용하였었는데 지금은 `json`을 많이 사용하고, 안드로이드에서는 [Google-gson](https://github.com/google/gson)을 이용하여 파싱을 하고 있습니다.
- [ListView](https://developer.android.com/reference/android/widget/ListView.html)는 API 1부터 존재하였는데 현재는 ListView의 단점을 보완한 [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)를 많이 사용하지만, Support library을 통해서 제공합니다.
- `HTTP`를 직접 구현하였었지만, 이제는 `Retrofit`을 사용하여 간단하게 구현이 가능합니다.
- `Eclipse` 기반의 코드에서 `Android Studio` 기반의 코드로 새롭게 작성합니다.


<br />

## 사용한 API

- [ListView API 문서](https://developer.android.com/reference/android/widget/ListView.html)
- [ListView Example](https://developer.android.com/guide/topics/ui/layout/listview.html)
- [ArrayAdapter API 문서](https://developer.android.com/reference/android/widget/ArrayAdapter.html)
  - 아이템을 관리하는 부분이 포함되어 있어 다루기 쉽습니다.
- [GitHub API](https://developer.github.com/v3/)
  - GitHub API 중 사람 검색 부분을 사용하였습니다.
  - 사람 검색의 경우 Auth 인증을 하지 않고 처리가 가능하며, API 호출 제한이 걸립니다.
- [Square-Retrofit](https://square.github.io/retrofit/)
  - HTTP 통신을 위한 라이브러리입니다.
- [Square-OkHttp](http://square.github.io/okhttp/)
  - Retrofit에서 함께 사용하며, 로그를 보기 위해서 추가하였습니다.
- [Chrome Custom Tabs](https://developer.chrome.com/multidevice/android/customtabs)
  - 사람 검색의 사용자 프로필을 상세하게 보기 위해서 추가하였습니다.
- [Android TODO MVP 어떻게 적용할까?](http://thdev.tech/androiddev/2016/06/14/Android-TODO-MVP-Example.html)
  - MVP 패턴으로 다음의 예제를 작성하였습니다. MVP 부분을 참고해주시면 되겠습니다.

<br />

## ListView 샘플 코드는

리스트 뷰로 작성한 샘플은 아래의 링크를 통해 확인이 가능합니다.

Java/Kotlin으로 각각 분리되어 있고, 편하신 코드로 보시면 되겠습니다.

- [Custom ListView Sample](https://github.com/taehwandev/Android-BlogExample/tree/master/2016-10-30-CustomListViewSample)

과거에 작성하였던 구글 날씨 ListView는 아래의 링크를 참고하시면 다운 받을 수 있습니다.

- [Android 구글 날씨 파싱(XmlPullParser 사용)](http://thdev.net/28)


<br />

## 작성한 샘플의 화면

Java와 Kotlin으로 각각 작성된 샘플입니다. 오른쪽의 `2`를 누르면 똑같은 화면의 `kotlin` 샘플을 볼 수 있습니다.

상세 페이지는 Chrome Custom Tabs을 이용하여 추가해두었습니다.

| GitHubList  | Chrome Tabs  |
|:----------: | :----------: |
|![sample_01] | ![sample_02] |


<br />

## ListView

ListView는 안드로이드에 임베디드 되어 있는 코드로 동작하며, `API level 1`부터 존재하였습니다. `ListView`는 오래된 만큼 예제도 많고, 접근 방법도 다양합니다.

가장 일반적인 ListView의 `getView` 접근 방법이 되겠습니다.

```java
@Override
public View getView(final int position, View convertView, ViewGroup parent) {
    Holder holder = new Holder();
    View rowView = inflater.inflate(R.layout.item_list, null);
    holder.tv = (TextView) rowView.findViewById(R.id.text);
    holder.img = (ImageView) rowView.findViewById(R.id.image);
    holder.tv.setText(result[position]);
    holder.img.setImageResource(imageId[position]);
    rowView.setOnClickListener(new OnClickListener() {
        @Override
        public void onClick(View v) {
            // TODO Auto-generated method stub
            Toast.makeText(context, "You Clicked " + result[position], Toast.LENGTH_LONG).show();
        }
    });
    return rowView;
}
```

하지만 위와 같이 동작하게 되면 <a href="https://developer.android.com/reference/android/widget/Adapter.html#getView(int, android.view.View, android.view.ViewGroup)">getView</a> ListView의 재사용성이 떨어집니다.

재사용이라는게 getView는 현재 화면상에 아이템이 보일 때마다 호출되게 됩니다.

예를 들면 아이템이 20개가 있고, 이를 스크롤 한다고 해보겠습니다.

스크롤 시에도 `getView`는 계속 적으로 호출됩니다.

```
현재 보이는 View : 0~10
보이지 않는 View : 11~20

스크롤 후 이동된 View : 5~15
보이지 않은 View : 0~4, 16~20
```

보이지 않는 View는 아직 생성되지 않았고, 현재 List 상 보이는 아이템의 ViewHolder만 생성된 상태입니다.

코드상

```java
Holder holder = new Holder();
View rowView = inflater.inflate(R.layout.item_list, null);
```

의 위치입니다. 별도의 `null` 처리가 없으므로, 스크롤을 통해 이동할 때마다 View의 `create`가 발생합니다.

View의 create가 발생함과 동시에 `findViewById` 역시 함께 일어나게 됩니다.

리스트 뷰의 특성상 하나의 `View`만 있으면, 연속적으로 사용이 가능한 형태가 만들어지면 되는데 ListView는 강제적이지 않습니다.


## 그래서 ViewHolder 개념이...

네 그래서 ViewHolder 개념이 추가되었습니다. 구글의 권장 사항이라서 강제적이지는 않습니다.

다만 위와 같이 `inflate`와 `findViewById`을 리스트 뷰에서 연속적으로 발생하게 되면 메모리와 성능에 영향을 미칠 수 있습니다.

간단한 리스트야 문제없지만 복잡한 ListView라면 당연히 성능에 영향을 미치죠.

- [findViewById의 호출 시 문제 될 만한 부분 정리 글 ](http://hashcode.co.kr/questions/1951/findviewbyid%EC%97%90-%EA%B4%80%ED%95%98%EC%97%AC)


## ViewHolder을 적용하면?

ViewHolder 패턴입니다. `convertView == null` 일 경우에만 `inflate`와 `findViewById`가 생성됩니다. 그리고 `rootView`에 `setTag`을 호출하여, 생성된 `ViewHolder`을 임시 저장해둡니다.

메모리에 문제가 없다면 최초 1회만 생성하고 이후 `else`문을 통해서 `getTag`을 호출하고, 이를 통해 `ViewHolder`에 접근이 가능한 형태가 만들어집니다.

```java
@Override
public View getView(final int position, View convertView, ViewGroup parent) {
    // 최초에 convertView가 null이므로, inflate를 처리한다
    if (convertView == null) {
        // 전역으로 생성한 View에 inflate
        rootView = inflater.inflate(R.layout.item_list, null);

        // ViewHolder을 생성
        Holder holder = new Holder();
        holder.tv = (TextView) rowView.findViewById(R.id.text);
        holder.img = (ImageView) rowView.findViewById(R.id.image);

        // setTag
        rootView.setTag(viewHolder);
    } else {
        // convertView에 convertView를 셋팅
        rootView = convertView;
        // rootView에서 holder을 꺼내온다
        holder = (Holder) rootView.getTag();
    }

    holder.tv.setText(result[position]);
    holder.img.setImageResource(imageId[position]);
    rowView.setOnClickListener(new OnClickListener() {
        @Override
        public void onClick(View v) {
            // TODO Auto-generated method stub
            Toast.makeText(context, "You Clicked " + result[position], Toast.LENGTH_LONG).show();
        }
    });
    return rootView;
}
```

이렇게 하는 게 바로 `ViewHolder` 패턴입니다.

하지만 강제적이지 않아서 구현하기 귀찮습니다.

또한 커스텀이 많고, 하나의 리스트에 다양한 `ViewHolder` 만들기가 쉽지 않습니다.

ex) 아래와 같다면...

```
사진이 포함된 ViewHolder
텍스트만 있는 ViewHolder
오른쪽으로 스크롤 되는 ListView가 포함된 ViewHolder
```

ViewHolder 패턴을 이해하고 작성하면 만들 수 있지만, 그래도 귀찮습니다.

안드로이드 5.0부터 나온 `RecyclerView`가 이를 대체할 수 있고, `ViewType` 구분만 하여도 어렵지 않게 접근이 가능합니다.

**다만 아이템에 대한 관리를 모두 개발자가 해야 합니다.**

그래서 저는 `별도의 BaseRecyclerView`를 만들어서 쓰고 있습니다.

- [Android Recyclerview Base](https://github.com/taehwandev/AndroidRecyclerView)


RecyclerView는 다음에 다루겠습니다.


<br />

## Custom ListView 주요 코드

Adapter custom을 통해서 getView를 다루었습니다.

GitHub API는 Retrofit을 통해서 받아오고, 이를 Presenter에서 처리하였습니다.

- [Java는 Presenter에서 처리합니다.](https://github.com/taehwandev/Android-BlogExample/blob/master/2016-10-30-CustomListViewSample/src/main/java/tech/thdev/customlistviewsample/view/presenter/MainPresenter.java#L30)
- [kotlin은 model에서 처리합니다.](https://github.com/taehwandev/Android-BlogExample/blob/master/2016-10-30-CustomListViewSample/src/main/kotlin/tech/thdev/customlistviewsample/data/source/GitHubUserRemoteDataSource.kt#L22)

### java - 데이터 불러오는 부분

```java
@Override
public void loadGitHubUser(String userKeyword) {
    // 마지막 페이지인지 체크
    if (page > 0 && isLastItem) {
        return;
    }

    // Progress 보여주기
    view.showProgress();

    // github를 통해서 User 정보를 받아옵니다.
    final Call<GitHubUserResponse> gitHubUserCall = retrofitGitHub.searchGitHubUser(userKeyword, ++page, DEFAULT_ITEM_COUNT);

    // Retrofit의 enqueue을 통해서 다음을 처리합니다.
    gitHubUserCall.enqueue(new Callback<GitHubUserResponse>() {

        // 성공적인 response을 받았을 경우
        @Override
        public void onResponse(Call<GitHubUserResponse> call, Response<GitHubUserResponse> response) {
            // API limit으로 인해 실패하는 경우가 생깁니다.
            if (!response.isSuccessful()) {
                view.hideProgress();

                /*
                 * API rate limit exceeded for IP Address.
                 * (But here's the good news: Authenticated requests get a higher rate limit. Check out the documentation for more details.)
                 */
                isLastItem = true;
                return;
            }

            // Retrofit에서 GSON을 GitHubUserReponse로 변환한 결과를 받아온다
            GitHubUserResponse gitHubUserResponse = response.body();
            if (gitHubUserResponse != null) {
                if (gitHubUserResponse.items != null && gitHubUserResponse.items.size() > 0) {
                    // items를 추가한다
                    for (GitHubItem item : gitHubUserResponse.items) {
                        view.addItem(item);
                    }
                }
            }

            view.hideProgress();
            view.notifyListView();
        }

        // 받아오기 실패할 경우
        @Override
        public void onFailure(Call<GitHubUserResponse> call, Throwable t) {
            view.hideProgress();
            view.showFailLoad();
        }
    });
}
```

<br />

### ArrayAdapter 주요코드

- [GitHubUserAdapter.java](https://github.com/taehwandev/Android-BlogExample/blob/master/2016-10-30-CustomListViewSample/src/main/java/tech/thdev/customlistviewsample/adapter/GitHubUserAdapter.java)

사용할 ViewHolder을 다음과 같이 추가하였습니다.

ImageView와 TextView 2개입니다.

```java
private class ViewHolder {
    ImageView imgUserAvater;
    TextView tvUserName;
    TextView tvUserScore;
}
```

ViewHolder 패턴을 통해서 처리하였습니다.

`convertView == null`일 경우에만 `inflater`와 `findViewById`을 호출하게 됩니다.

그렇지 않으면 `else`를 통해 `getTag` 함수가 호출되게 됩니다.

```java
@NonNull
@Override
public View getView(int position, View convertView, ViewGroup parent) {
    if (convertView == null) {
        LayoutInflater inflater = LayoutInflater.from(getContext());
        rootView = inflater.inflate(R.layout.item_github_user, parent, false);

        viewHolder = new ViewHolder();
        viewHolder.imgUserAvater = (ImageView) rootView.findViewById(R.id.img_user_avater);
        viewHolder.tvUserName = (TextView) rootView.findViewById(R.id.tv_user_name);
        viewHolder.tvUserScore = (TextView) rootView.findViewById(R.id.tv_user_score);

        // setTag
        rootView.setTag(viewHolder);

    } else {
        rootView = convertView;
        viewHolder = (ViewHolder) rootView.getTag();
    }

    // Holder에 아이템을 출력합니다.
    final GitHubItem gitHubItem = getItem(position);
    if (gitHubItem != null) {
        viewHolder.tvUserName.setText(gitHubItem.login);
        viewHolder.tvUserScore.setText(String.format("%f", gitHubItem.score));

        // ImageDownloadThread을 직접 구현하였습니다.
        ImageDownloadThread.getInstance().loadImage(R.mipmap.ic_launcher, viewHolder.imgUserAvater, gitHubItem.avatar_url);
    }

    return rootView;
}
```

<br />

### ImageDownloadThread

- [ImageDownloadThread](https://github.com/taehwandev/Android-BlogExample/blob/master/2016-10-30-CustomListViewSample/src/main/java/tech/thdev/customlistviewsample/async/ImageDownloadThread.java)

Thread을 통해서 ImageDownloadThread을 처리합니다.

간단하게 이미지 캐시로 [LruCache](https://developer.android.com/reference/android/util/LruCache.html)을 사용하였습니다.

Url connection을 통해서 이미지를 다운받고, 이를 UI Thread에서 draw 하는 간단한 코드입니다. RxJava을 활용하거나, 이미지 로도를 별도로 이용한다면 다음과 같이 긴 코드가 불필요하겠지만.. 과거에 작성했던 코드를 새로 추가 작성한 부분입니다.

```java
private class DownloadThread implements Runnable {

  // 생략...

  @Override
  public void run() {
      try {
          URL url = new URL(resourceUrl);
          connection = url.openConnection();
          connection.connect();

          inputStream = connection.getInputStream();
          bufferedInputStream = new BufferedInputStream(inputStream);

          cache.put(resourceUrl, new WeakReference<>(BitmapFactory.decodeStream(bufferedInputStream)));
          draw(resourceUrl);

      } catch (IOException e) {
          closeStream();

      } finally {
          closeStream();
      }
  }

  // 생략 ...

  // Bitmap을 통해 이미지를 그린다. 현재 보여지는 아이템의 위치에 맞게 그리도록 TAG를 활용
  private void draw(final String resourceUrl) {
      new Handler(Looper.getMainLooper()).post(new Runnable() {
          @Override
          public void run() {
              ImageView imageView = weakReferenceImageView.get();
              if (!TextUtils.isEmpty(resourceUrl) &&
                      imageView.getTag() != null &&
                      imageView.getTag().equals(resourceUrl) &&
                      cache.get(resourceUrl) != null &&
                      cache.get(resourceUrl).get() != null) {
                  imageView.setImageBitmap(cache.get(resourceUrl).get());
              }
          }
      });
  }
}
```

`ImageView` 역시 [WeakReference](https://developer.android.com/reference/java/lang/ref/WeakReference.html)을 추가하였습니다. GC가 호출되면 메모리 해제가 되도록 처리하였습니다.

그리고 `TAG`을 통해 현재 보이는 화면의 `TAG`가 맞는지를 확인하여 그리도록 하였습니다.

- [[안드로이드] 안드로이드 메모리 관리 (Weak Reference와 Soft Reference)](http://itmining.tistory.com/10)


<br />

### Retrofit 주요 코드

- [RetrofitCreator.java](https://github.com/taehwandev/Android-BlogExample/blob/master/2016-10-30-CustomListViewSample/src/main/java/tech/thdev/customlistviewsample/network/RetrofitCreator.java)

Retrofit의 코드를 간략하게 다음과 같이 추가합니다.

`HttpLoggingInterceptor`을 통해서 Log 확인이 가능하고, `Retrofit`을 통해서 http 연결을 동작합니다. 이때 `addConverterFactory`을 `GsonConverterFactory`을 추가함으로써 `gson`을 통해서 json 파싱 된 결과 데이터를 전달받을 수 있습니다.

```java

public class RetrofitCreator {

    public static Retrofit createRetrofit() {

        HttpLoggingInterceptor interceptor = new HttpLoggingInterceptor();
        interceptor.setLevel(HttpLoggingInterceptor.Level.BODY);
        OkHttpClient client = new OkHttpClient.Builder().addInterceptor(interceptor).build();

        return new Retrofit.Builder()
                .baseUrl("https://api.github.com/")
                .client(client)
                // Json 변환
                .addConverterFactory(GsonConverterFactory.create())
                .build();
    }
}
```

### Chrome Custom Tabs

Chrome Custom Tabs을 추가해보았습니다.

Custom tabs을 사용하기 위해서는 다음의 dependency 추가가 필요합니다.

```
compile 'com.android.support:customtabs:24.2.1'
```

저는 다음과 같이 사용하였습니다.

- [MainActivity.java - Custom Tabs](https://github.com/taehwandev/Android-BlogExample/blob/master/2016-10-30-CustomListViewSample/src/main/java/tech/thdev/customlistviewsample/view/MainActivity.java#L119)

```java
CustomTabsIntent.Builder builder = new CustomTabsIntent.Builder();
builder.setToolbarColor(getResources().getColor(R.color.colorPrimary));

builder.setStartAnimations(this, 0, 0);
builder.setExitAnimations(this, 0, 0);

CustomTabsIntent customTabsIntent = builder.build();
customTabsIntent.launchUrl(this, Uri.parse(item.html_url));
```

시작과 종료에 대한 에니메이션을 제거하고, `Toolbar` 색상을 기본 색상으로 지정하였습니다.

WebView를 별도로 구현하는것보다 성능상 좋다고 합니다.


<br />

## 마무리

구글 날씨 파싱에 작성했던 ListView을 새로 작성해보았습니다.

ListView를 많이 쓰지는 않지만... 다시 한번 작성해보았습니다.

- [Android 구글 날씨 파싱(XmlPullParser 사용)](http://thdev.net/28)

다음에는 `RecyclerView`을 작성해보려고 합니다.


<br />

## RecyclerView

- [Android ListView 사용하기](http://thdev.tech/androiddev/2016/10/30/Android-CustomListView-Sample.html)


<br />

## ListView 샘플 코드는

리스트 뷰로 작성한 샘플은 아래의 링크를 통해 확인이 가능합니다.

Java/Kotlin으로 각각 분리되어 있고, 편하신 코드로 보시면 되겠습니다.

- [Custom ListView Sample](https://github.com/taehwandev/Android-BlogExample/tree/master/2016-10-30-CustomListViewSample)

과거에 작성하였던 구글 날씨 ListView는 아래의 링크를 참고하시면 다운 받을 수 있습니다.

- [Android 구글 날씨 파싱(XmlPullParser 사용)](http://thdev.net/28)


[sample_01]: /images/2016/2016-10-30-Android-CustomListView-Sample/sample_01.png
[sample_02]: /images/2016/2016-10-30-Android-CustomListView-Sample/sample_02.png
