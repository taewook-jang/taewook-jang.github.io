---
layout: post
title: Android RecyclerView 사용하기
categories: [AndroidDev]
tags: [Android, SupportLibrary]
fullview: false
comments: true
published: true
---

안드로이드 RecyclerView에 대해서 정리합니다.

Android 5.0에 처음 소개된 `RecyclerView`는 안드로이드 `ListView`의 장/단점을 보완한 고급 위젯입니다.

Android Lollipop과 함께 나온 이 위젯은 SupportLibrary에 포함되어 Android Version 7 이상에서 사용이 가능합니다.

현재 가장 최신은 `25.0.0`이며, API 24에서 최신은 `24.2.1`입니다. Target API에 따라서 숫자가 변경되니 Google SupportLibrary 사이트에서 확인하세요.

- [v7 Support Libraries - RecyclerView library](https://developer.android.com/topic/libraries/support-library/features.html#v7)

```
dependencies {
    compile 'com.android.support:cardview-v7:21.0.+'
    compile 'com.android.support:recyclerview-v7:21.0.+'
}
```


<br />

## API 문서

- [Creating Lists and Cards](https://developer.android.com/training/material/lists-cards.html)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
- [RecyclerView.Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html)


<br />

## Android ListView 정리

리스트 뷰에 대해서 정리한 글입니다. `ArrayAdapter`을 정리하였습니다.

- [Android ListView 사용하기](http://thdev.tech/androiddev/2016/10/30/Android-CustomListView-Sample.html)


<br />

## 샘플 코드는?

GitHub에 올려두었으며, `RecyclerView 샘플`입니다.

base로 구분된 부분이 있는데 이는 나중에 `supportLibrary` 형태로 배포할 예정입니다.

Base는 Kotlin을 기준으로 작성하였고, 샘플은 Java/Kotlin 모두가 존재합니다. 각각 살펴보시면 되겠습니다.

- [AndroidRecyclerView](https://github.com/taehwandev/AndroidRecyclerView)


<br />

## Create Lists

`Creating Lists and Cards`에 정의된 List 표현입니다.

![RecyclerView]

`Widget`인 `RecyclerView`는 `LayoutManager`를 통해서 `View` 그리는 방법을 정의합니다.

[RecyclerView.Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html)에서 `Data`의 `ViewHolder` 정의에 따라서 UI가 선택되고, 이를 표현하게 됩니다.


- `ViewHolder`의 적용으로 View의 재사용을 가능하게 합니다.
- `LayoutManager`의 추가로 아이템의 배치 방법을 정의할 수 있다.
  - [LinearLayoutManager](https://developer.android.com/reference/android/support/v7/widget/LinearLayoutManager.html) : 가로/세로 형태로 아이템을 배치한다.

    ![LinearLayoutManager]
  - [GridLayoutManager](https://developer.android.com/reference/android/support/v7/widget/GridLayoutManager.html) : 한 줄에 1개 이상의 이미지를 표시할 수 있지만, 아이템의 크기는 줄의 첫 번째 아이템의 크기에 따라서 달라질 수 있다.(고정시에는 모두 고정)

    ![GridLayoutManager]
  - [StaggeredGridLayoutManager](https://developer.android.com/reference/android/support/v7/widget/StaggeredGridLayoutManager.html) : 그리드 형태에 아이템의 크기를 다양하게 적용할 수 있다.

    ![StaggeredGridLayoutManager]

  - Custom LayoutManager : 3개의 레이아웃 매니저를 상속받아 커스텀 할 수 있다.

- 많은 데이터의 리스트 형태로 제공이 가능하다.
- [RecyclerView.ItemAnimator](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemAnimator.html)을 이용하여 Item의 Animator을 정의할 수 있습니다.


<br />

## ListView의 장점

- `ListView`는 간단하게 리스트를 만드는 부분에 있어서는 장점입니다.
- 간단한 아이템 형태를 만드는 경우는 빠르게 적용이 가능한 `ArrayAdapter`을 제공합니다.

<br />

## ListView의 단점

- 아이템의 애니메이션 처리가 쉽지 않습니다.
- 리스트에는 한 개 이상의 View가 필요한 경우가 있지만 커스텀으로 작업하기 쉽지 않습니다.

  - 다음은 한 개 이상의 `ViewHolder`를 가진 샘플입니다. 위쪽에는 사진이 표시되고, 아래에는 Footer이라는 새로운 `ViewHolder`가 노출된 상태입니다.

  ![ViewHolderSample]

- `RecyclerView`에는 `Header/Footer` 추가를 직접 구현해야 하지만, `ListView`는 기본으로 제공합니다.
- `ViewHolder 패턴`을 강제적으로 사용하지는 않으므로 고비용의 `findViewById`가 매번 호출될 수 있다.

구글에서 추천하는 `ViewHolder 패턴`을 사용하지 않게되면 다음과 같은 코드를 타게 됩니다.

```java
@Override
public View getView(final int position, View convertView, ViewGroup parent) {
    Holder holder = new Holder();
    View rowView = inflater.inflate(R.layout.item_list, null);
    holder.tv = (TextView) rowView.findViewById(R.id.text);
    holder.tv.setText(result[position]);
    return rowView;
}
```

`ItemCount`에 따라서 매번 `getView`을 호출하게 됩니다. 이때 위와 같은 코드는 holder부터 `inflater.inflate`을 초기화하고, `findViewById`역시 매번 생성하게 됩니다.

고비용의 `findViewById`을 매번 하는 것은 성능상 좋지 않고, 메모리의 영향도 받을 수 있습니다.

그래서 다음과 같은 `ViewHolder 패턴`을 사용할 수 있습니다.

```java
@Override
public View getView(final int position, View convertView, ViewGroup parent) {
    if (convertView == null) {
        rootView = inflater.inflate(R.layout.item_list, null);
        Holder holder = new Holder(); // ViewHolder을 생성
        holder.tv = (TextView) rowView.findViewById(R.id.text);
        rootView.setTag(viewHolder); // setTag
    } else {
        rootView = convertView;
        holder = (Holder) rootView.getTag(); // rootView에서 holder을 꺼내온다
    }

    holder.tv.setText(result[position]);
    return rootView;
}
```

위와 같은 형태로 제공이 되는데 매번 구현하는 건 귀찮고, 서로 다른 ViewHolder을 여러 개 만들어서 사용하기 쉽지 않습니다.(ItemView의 View가 여러 개 생성될 수 있는 형태)


<br />

## RecyclerView을 사용하기 위해서

RecyclerView는 supportLibrary와 Android 5.0(API 21 이상)에서 사용이 가능합니다.

API 7부터 사용이 가능한 [supportLibrary - recyclerview-v7](https://developer.android.com/topic/libraries/support-library/features.html#v7)은 다음과 같이 정의할 수 있습니다.

```
dependencies {
    compile 'com.android.support:recyclerview-v7:24.2.1'
}
```


<br />

## RecyclerView View 정의

RecyclerView의 XML 정의는 다음과 같습니다.

v7의 RecyclerView을 정의합니다.

```xml
<android.support.v7.widget.RecyclerView
    android:id="@+id/recycler_view"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```


<br />

## layoutManager 정의하기

View의 형태를 정의하기 위해 `LayoutManager`을 정의해야 합니다.

`RecyclerView`는 기본 정의가 존재하지 않기 때문에 꼭! 최소 하나씩 설정해주어야 합니다.

설정하지 않으면 화면이 구성되지 않으므로, 헤매지 말고 꼭! 정의 부분을 살펴보시기 바랍니다.

### xml에서 정의하기

다음의 `app` 정의를 사용하기 위해서는 아래의 코드가 추가되어야 합니다.

`xmlns:app="http://schemas.android.com/apk/res-auto"`

- `app:layoutManager` : xml에서 `layoutManager`을 정의할 수 있습니다.
- `app:spanCount` : xml에서 `layoutManager`에서 사용할 `spanCount`을 정의할 수 있습니다.

**LinearLayoutManager**

```xml
<android.support.v7.widget.RecyclerView
    app:layoutManager="LinearLayoutManager" />
```

**GridLayoutManager**

```xml
<android.support.v7.widget.RecyclerView
    app:layoutManager="GridLayoutManager"
    app:spanCount="2" />
```

**LinearLayoutManager**

```xml
<android.support.v7.widget.RecyclerView
    app:layoutManager="StaggeredGridLayoutManager"
    app:spanCount="3" />
```

### LayoutManager 코드를 통한 정의

- [LinearLayoutManager](https://developer.android.com/reference/android/support/v7/widget/LinearLayoutManager.html)

```java
// use a linear layout manager
mLayoutManager = new LinearLayoutManager(this);
mRecyclerView.setLayoutManager(mLayoutManager);
```

- [GridLayoutManager](https://developer.android.com/reference/android/support/v7/widget/GridLayoutManager.html)

```java
// use a staggered grid layout manager
mGridLayoutManager = new new GridLayoutManager(this, 3);
mRecyclerView.setLayoutManager(mGridLayoutManager);
```

- [StaggeredGridLayoutManager](https://developer.android.com/reference/android/support/v7/widget/StaggeredGridLayoutManager.html)

```java
// use a staggered grid layout manager
mStgaggeredGridLayoutManager = new StaggeredGridLayoutManager(2, StaggeredGridLayoutManager.VERTICAL);
mRecyclerView.setLayoutManager(mStgaggeredGridLayoutManager);
```

- 상속받아 Custom 처리


<br />

## ViewHolder 정의

구글에서 정의해주는 간단한 ViewHolder입니다.

[RecyclerView.ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html)을 상속받아서 정의합니다.

```java
// Provide a reference to the views for each data item
// Complex data items may need more than one view per item, and
// you provide access to all the views for a data item in a view holder
public class ViewHolder extends RecyclerView.ViewHolder {
    // each data item is just a string in this case
    public TextView mTextView;
    public ViewHolder(TextView v) {
        super(v);
        mTextView = v;
    }
}
```

제가 주로 사용하는 ViewHolder는 다음과 같습니다.

`Context`와 `ViewGroup`을 생성자로 전달받아서 `LayoutInflater.form`을 통해서 View를 생성합니다.

그리고 `itemView`를 통해서 View들을 `findViewById`을 제공합니다.

```java
public class ViewHolder extends RecyclerView.ViewHolder {

  private TextView textView;

  public ViewHolder(Context context, ViewGroup parent) {
      super(LayoutInflater.from(context).inflate(R.layout.item_large_view, parent, false));

      textView = (TextView) itemView.findViewById(R.id.tv_message);
  }
}
```

코틀린을 통한 베이스를 다음과 같이 작성해두었습니다.

```kotlin
abstract class BaseViewHolder<ITEM>(
        open val adapter: RecyclerView.Adapter<*>, itemView: View) :
        RecyclerView.ViewHolder(itemView) {

    constructor(@LayoutRes layoutRes: Int,
                parent: ViewGroup?, adapter: RecyclerView.Adapter<*>)
    : this(adapter, LayoutInflater.from((adapter as? AbstractRecyclerAdapter<*, *>)?.context).inflate(layoutRes, parent, false))

    init {
        ButterKnife.bind(BaseRecyclerViewHolder@this, itemView)
    }

    // ViewHolder의 View를 정의합니다.
    abstract fun onViewHolder(item: ITEM?, position: Int)

    open protected val context: Context?
        get() = (adapter as? AbstractRecyclerAdapter<*, *>)?.context
}
```


<br />

## Adapter 정의

RecyclerView의 Adapter는 `ListView`의 `ArrayAdapter`처럼 `List<Object>`을 기본적으로 가지고 있지 않습니다.

그래서 원하는 형태의 `Data` 형태도 직접 구현하여야 합니다.

```java
public class MyAdapter extends RecyclerView.Adapter<MyAdapter.ViewHolder> {

    // 아이템 리스트
    private String[] mDataset;

    // Provide a suitable constructor (depends on the kind of dataset)
    public MyAdapter(String[] myDataset) {
        mDataset = myDataset;
    }

    // Create new views (invoked by the layout manager)
    @Override
    public MyAdapter.ViewHolder onCreateViewHolder(ViewGroup parent,
                                                   int viewType) {
        // create a new view
        View v = LayoutInflater.from(parent.getContext())
                               .inflate(R.layout.my_text_view, parent, false);
        // set the view's size, margins, paddings and layout parameters
        ...
        ViewHolder vh = new ViewHolder(v);
        return vh;
    }

    // Replace the contents of a view (invoked by the layout manager)
    @Override
    public void onBindViewHolder(ViewHolder holder, int position) {
        // - get element from your dataset at this position
        // - replace the contents of the view with that element
        holder.mTextView.setText(mDataset[position]);
    }

    // Return the size of your dataset (invoked by the layout manager)
    @Override
    public int getItemCount() {
        return mDataset.length;
    }
}
```

### item 정의

RecyclerView는 아이템에 대해서 직접 정의를 해야 합니다.

해당 Adapter에서 사용할 아이템을 직접 정의해주면 됩니다.

```java
// 아이템 리스트
private String[] mDataset;
```

그리고 `getItemCount`을 상속을 기본적으로 받아야 하며, size를 다음과 같이 return 해주면 됩니다.

```java
// Return the size of your dataset (invoked by the layout manager)
@Override
public int getItemCount() {
    return mDataset.length;
}
```

### View를 정의

`onCreateViewHolder`을 기본적으로 상속받습니다.

이를 통해 ViewHolder을 생성하게 됩니다.

viewType에 따라서 최소 1회만 생성합니다.

만약 `viewType`이 1개 이상이라면 `onCreateViewHolder` 역시 1번 이상이 호출됩니다.

```java
// Create new views (invoked by the layout manager)
@Override
public MyAdapter.ViewHolder onCreateViewHolder(ViewGroup parent,
                                               int viewType) {
    // create a new view
    View v = LayoutInflater.from(parent.getContext())
                           .inflate(R.layout.my_text_view, parent, false);
    // set the view's size, margins, paddings and layout parameters
    ...
    ViewHolder vh = new ViewHolder(v);
    return vh;
}
```

### view를 표현하기

`onCreateViewHolder`을 통해서 생성되면, `onBindViewHolder`에서 해당 `holder`의 View에 데이터를 노출을 정의하면 됩니다.

`RecyclerView`는 `ViewHolder`을 재사용할 수 있도록 설계되어 있으므로, `ViewType`이 한번 생성된 이후로는 `onBindViewHolder`만 호출되게 됩니다.

```java
// Replace the contents of a view (invoked by the layout manager)
@Override
public void onBindViewHolder(ViewHolder holder, int position) {
    // - get element from your dataset at this position
    // - replace the contents of the view with that element
    holder.mTextView.setText(mDataset[position]);
}
```

### viewType을 정의하기

기본 상속을 받는 메소드는 아니지만 `viewType`을 정의할 수 있습니다.

기본값은 0으로 초기화되지만, 사용에 따라서 `getItemViewType`을 정의할 수 있습니다.

ViewType이 1개 이상이라면 기본적으로 다음의 `getItemViewType`을 정의해주시면 되겠습니다.

```java
@Override
public int getItemViewType(int position) {
    return super.getItemViewType(position);
}
```


<br />

## RecyclerView에 setAdapter()

RecyclerView에 setAdapter을 정의하여야 합니다.

생성된 `Adapter`을 다음과 같이 추가해주시면 됩니다.

```java
mRecyclerView = (RecyclerView) findViewById(R.id.my_recycler_view);

  // use this setting to improve performance if you know that changes
  // in content do not change the layout size of the RecyclerView
  mRecyclerView.setHasFixedSize(true);

  // use a linear layout manager
  mLayoutManager = new LinearLayoutManager(this);
  mRecyclerView.setLayoutManager(mLayoutManager);

  // specify an adapter (see also next example)
  mAdapter = new MyAdapter(myDataset);
  mRecyclerView.setAdapter(mAdapter);
```


<br />

## Kotlin으로 작성한 BaseAdapter

kotlin으로 작성한 `BaseAdapter`입니다. `itemList`에 대해서 정의를 하였고,

`getItem`/`removeItem`/`setItem`/`clear`을 처리하는 부분을 `model` 형태로 처리하였습니다.

- [AbstractRecyclerAdapter](https://github.com/taehwandev/AndroidRecyclerView/blob/master/recyclerview/src/main/java/tech/thdev/support/widget/adapter/AbstractRecyclerAdapter.kt)

```kotlin
abstract class AbstractRecyclerAdapter<ITEM, VIEW_TYPE : RecyclerView.ViewHolder?>(open val context: Context) :
        RecyclerView.Adapter<VIEW_TYPE>(), BaseRecyclerModel<ITEM> {

    private val itemList: MutableList<ITEM> = ArrayList()

    abstract fun onItemViewType(position: Int): Int

    override fun getItemViewType(position: Int)
            = onItemViewType(position)

    override fun getItemCount()
            = itemList.size

    override fun addItem(item: ITEM) {
        itemList.add(item)
    }

    override fun addItem(position: Int, item: ITEM) {
        itemList.add(position, item)
    }

    override fun addItems(items: List<ITEM>) {
        itemList.addAll(items)
    }

    override fun clear() {
        itemList.clear()
    }

    override fun removeItem(item: ITEM) {
        itemList.remove(item)
    }

    override fun removeItem(position: Int) {
        itemList.removeAt(position)
    }

    /**
     * GetItem null or ITEM
     */
    override fun getItem(position: Int)
            = itemList.getOrNull(position)

    override fun getItems() = itemList
}
```


<br />

## 마무리

저는 `BaseRecyclerAdapter`을 만들어서 사용하고 있습니다.

ListView의 [ArrayAdapter](https://developer.android.com/reference/android/widget/ArrayAdapter.html)을 최대한 유사하게 사용하기 위해서 만들었습니다.

Header/Footer을 포함하는 RecyclerView도 추가로 만들어서 사용 중입니다.

`ListView`는 `Header/Footer`을 간단하게 추가할 수 있지만, `RecyclerView`는 제공하지 않습니다. 대신 직접 구현해서 사용할 수 있습니다.

처음에 만들기가 귀찮긴 하지만.. 만들어두면 사용하기 편합니다.

간단하게 사용하기에는 ListView가 편하지만, View의 커스텀이 많아지면 질수록 RecyclerView가 관리하기 편합니다.


<br />

## 샘플 코드는?

GitHub에 올려두었으며, `RecyclerView 샘플`입니다.

base로 구분된 부분이 있는데 이는 나중에 `supportLibrary` 형태로 배포할 예정입니다.

Base는 Kotlin을 기준으로 작성하였고, 샘플은 Java/Kotlin 모두가 존재합니다. 각각 살펴보시면 되겠습니다.

- [AndroidRecyclerView](https://github.com/taehwandev/AndroidRecyclerView)



[RecyclerView]: /images/2016/2016-11-01-Android-RecyclerView-intro/RecyclerView.png

[LinearLayoutManager]: /images/2016/2016-11-01-Android-RecyclerView-intro/LinearLayoutManager.png
[GridLayoutManager]: /images/2016/2016-11-01-Android-RecyclerView-intro/GridLayoutManager.png
[StaggeredGridLayoutManager]: /images/2016/2016-11-01-Android-RecyclerView-intro/StaggeredGridLayoutManager.png

[ViewHolderSample]: /images/2016/2016-11-01-Android-RecyclerView-intro/ViewHolderSample.png
