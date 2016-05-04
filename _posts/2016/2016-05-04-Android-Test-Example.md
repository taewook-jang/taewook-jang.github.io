---
layout: post
title: androidTest - JUnit4, Espresso를 이용한 테스트 코드 작성
published: false
---

Android를 테스트하기 위해서 AndroidTest 작성하는 방법을 다루어보겠습니다.

Android Test는 Android Testing Support Library 페이지가 제공되고 있습니다.

- [Android Testing Support Library - Github 문서](https://google.github.io/android-testing-support-library/)
- [Testing Support Library - Android 문서](http://developer.android.com/tools/testing-support-library/index.html)
- [Android Testing Concepts](http://developer.android.com/tools/testing/testing_android.html#TestAPI)

Android Test를 하기 위해서는 다음을 사용하고 있습니다.

- [Espresso](https://google.github.io/android-testing-support-library/docs/espresso/index.html)
- [AndroidJUnitRunner](https://google.github.io/android-testing-support-library/docs/androidjunitrunner-guide/index.html)
- [JUnit4 Rules](https://google.github.io/android-testing-support-library/docs/rules/index.html)
- [UI Automator](https://google.github.io/android-testing-support-library/docs/uiautomator/index.html)

위와 같은 4가지를 Testing Support Library 페이지에서 다루고 있습니다.

JUnit4는 widely-used(널리 사용되는) 유닛 테스트 프레임워크입니다.

JUnit4와 Espresso을 함께 사용하여 Activity/Service에 대해서 테스트를 진행할 수 있습니다.

이번 글에서는 Android JUnit4와 Espresso에 대해서 간략하게 다루어 보겠습니다.

<br />

## Android Test 위한 설정

단위 테스트를 진행하기 위해서는 최소 설정을 해주어야 합니다.

build.gradle에 최소한의 코드가 추가되고 동기화 처리를 해주면 단위 테스트를 진행할 수 있습니다.

Android Test를 하기 위해서는 각각의 dependency를 추가해주어야 합니다.

우선 android.defaultConfig에 다음을 추가해주어야 하며, 이를 추가하면 안드로이드 테스트를 하겠다는 의미가 됩니다.

```
testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
```

build.gradle의 전체 코드를 보면 다음과 같으며, support-annotations의 경우 androidTestCompile에 아래와 같이 추가가 필요합니다.

test runner의 Annotation 버전과 중복되어 오류가 발생하기에 다음과 같이 추가해줄 수 있습니다.

```
apply plugin: 'com.android.application'

android {
    compileSdkVersion 23
    buildToolsVersion "23.0.3"

    defaultConfig {
        applicationId "tech.thdev.butterKnife_example"
        ...

        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
}

dependencies {
    // Error:Conflict with dependency 'com.android.support:support-annotations'.
    // Resolved versions for app (23.3.0) and test app (23.1.1) differ.
    // See http://g.co/androidstudio/app-test-app-conflict for details.
    androidTestCompile 'com.android.support:support-annotations:23.3.0'

    // Test dependency
    androidTestCompile 'com.android.support.test:runner:0.5'
    ...
}
```

또는 강제로 사용하지 않도록 다음과 같이 지정해줄 수도 있습니다.

```
configurations.all {
    resolutionStrategy {
        force 'com.android.support:support-annotations:23.3.0'
    }
}
```

<br />

## JUnit4 Rules

JUnit4는 Android ActivityTestRule와 ServiceTestRule를 테스트할 수 있는 AndroidJUnitRunner입니다.

JUnit4에서 제공하는 ActivityTestRule은 다음과 같이 정의하여 사용할 수 있습니다.

### JUnit4 ActivityTestRule

```java
@RunWith(AndroidJUnit4.class)
@LargeTest
public class MyClassTest {

    @Rule
    public ActivityTestRule<MyClass> mActivityRule = new ActivityTestRule(MyClass.class);

    @Test
    public void myClassMethod_ReturnsTrue() { ... }
}
```

위에서 정의한 @Rule, @Test의 각 Annotation 타입에 대해서 알아보도록 하겠습니다.

- [Android 개발 사이트의 Testing APIs](http://developer.android.com/tools/testing/testing_android.html#TestAPI)

- @Before : @Test를 시작하기 전 사전에 진행해야 할 사전 정의에 해당됩니다. @Test가 시작되기 전 항상 호출되게 됩니다.(단위 테스트 포함)
- @After : @After은 모든 테스트가 종료되면 호출되게 됩니다. 메모리에서 resource를 release 할 수 있습니다.
- @Test : @Before가 완료되면 실제 코드 테스트를 진행하게 됩니다.
- @Rule : 해당 Test class에서 사용하게 될 ActivityTestRule과 ServiceTestRule에 대하여 정의하게 됩니다.
- @BeforeClass, @AfterClass : public static method로 정의하여야 하며, @Before, @After와 동일하게 한 번씩만 실행되게 됩니다.
- @Test(timeout=<milliseconds>) : @Test 룰에 대한 timeout을 지정하게 됩니다. timeout 안에 테스트가 완료되지 않으면 fail이 되며, time은 milliseconds으로만 사용할 수 있습니다.
  예) @Test(timeout=500)
- @RequiresDevice : 에뮬레이터를 사용하지 않고 기기만 사용할 수 있습니다.
- @SdkSupress : minSdkVersion을 지정할 수 있습니다.
- @SmallTest, @MediumTest, @LargeTest : 테스트 성격을 구분하여 테스트할 수 있습니다.

문서 - [JUnit annotations](http://junit.sourceforge.net/javadoc/org/junit/package-summary.html)

### JUnit4 ActivityInstrumentationTestCase2

JUnit4 테스트 방법은 또 다른 방법도 있습니다.

Android Studio에서 새로운 프로젝트를 생성하면 항상 생성되는 androidTest 패키지에 포함된 ApplicationTest가 존재합니다.

초기화시에 @Rule를 사용하지 않고 InstrumentationRegistry을 이용할 수 있습니다.

```java
import android.support.test.runner.AndroidJUnit4;
import android.support.test.runner.AndroidJUnitRunner;
import android.test.ActivityInstrumentationTestCase2;

@RunWith(AndroidJUnit4.class)
public class CalculatorInstrumentationTest
        extends ActivityInstrumentationTestCase2<CalculatorActivity> {

    @Before
    public void setUp() throws Exception {
        super.setUp();

        // Injecting the Instrumentation instance is required
        // for your test to run with AndroidJUnitRunner.
        injectInstrumentation(InstrumentationRegistry.getInstrumentation());
        mActivity = getActivity();
    }

    @Test
    public void typeOperandsAndPerformAddOperation() {
        // Call the CalculatorActivity add() method and pass in some operand values, then
        // check that the expected value is returned.
    }

    @After
    public void tearDown() throws Exception {
        super.tearDown();
    }
}
```

위와 같은데 [InstrumentationRegistry](http://developer.android.com/reference/android/support/test/InstrumentationRegistry.html)에는 getArguments, getContext, getInstrumentation, getTargetContext, registerInstance을 포함하고 있습니다.

### JUnit4 ServiceTestRule

ServiceTestRule은 아래와 같이 정의하여 사용하게 됩니다.

ServiceTestRule는 binder를 이용한 테스트를 진행할 수 있습니다.

```java
@RunWith(AndroidJUnit4.class)
@MediumTest
public class MyServiceTest {

  @Rule
  public final ServiceTestRule mServiceRule = new ServiceTestRule();

  @Test
  public void testWithStartedService() {
      mServiceRule.startService(
          new Intent(InstrumentationRegistry.getTargetContext(), MyService.class));
      // test code
  }

  @Test
  public void testWithBoundService() {
      IBinder binder = mServiceRule.bindService(
          new Intent(InstrumentationRegistry.getTargetContext(), MyService.class));
      MyService service = ((MyService.LocalBinder) binder).getService();
      assertTrue("True wasn't returned", service.doSomethingToReturnTrue());
  }
}
```

## Espresso

JUnit4와 UI를 테스트하기 위해서는 Espresso를 추가로 적용해주어야 합니다.

Espresso를 사용하기 위해서는 다음의 dependency를 추가해주어야 합니다.

```xml
androidTestCompile 'com.android.support.test.espresso:espresso-core:2.2.2'
androidTestCompile 'com.android.support.test:runner:0.5'
```

필요에 따라 espresso는 Intent는 아래와 같이 추가해주면 됩니다.

```xml
androidTestCompile 'com.android.support.test:runner:0.5'
androidTestCompile 'com.android.support.test:rules:0.5'
androidTestCompile 'com.android.support.test.espresso:espresso-core:2.2.2'
androidTestCompile 'com.android.support.test.espresso:espresso-intents:2.2.2'
```

Espresso의 가장 기본적인 코드는 아래와 같이 작성할 수 있습니다.

- onView : View(TextView, EditText 등)
- withText : 해당 텍스트 "Hello world!"를 가지는 View를 찾습니다.
- withId : R.id.view에 해당하는 View를 찾습니다.
- perform : ViewAction을 처리합니다.
- click : 아이템을 클릭하는 이벤트입니다.
- check(matches()) : ViewAssertion 유효성을 체크하고, 맞는지 확인합니다.
- isDisplayed : 화면에 노출 상태를 가져옵니다.

```java
@RunWith(AndroidJUnit4.class)
@LargeTest
public class HelloWorldEspressoTest {

    @Rule
    public ActivityTestRule<MainActivity> mActivityRule = new ActivityTestRule(MainActivity.class);

    @Test
    public void listGoesOverTheFold() {
        onView(withText("Hello world!")).check(matches(isDisplayed()));
    }
}
```

Espresso을 이용한 가장 기본적인 예제는 위와 같습니다.

onView를 withText를 이용하여 찾고, isDisplayed 상태인지 체크합니다.

아래 예는 Activity one에서 Activity Two로 이동하게 되는데 이동하는 과정에서 EditText에 typeText를 입력하고, 이를 B Activity로 전달하고 출력하는 과정을 담습니다.

onView(withId(R.id.btn_second_activity)).perform(click())을 실행하여 B Activity로 전환하고, 써진 텍스트가 "Change Activity ..."인지를 확인하게 됩니다.

keyboard 역시 함께 close(hide) 상태로 변경하게 됩니다.

```java
@Test
public void testChangeTextToSecondActivity() throws Exception {
    // Type text and then press the button.
    onView(withId(R.id.et_message)).perform(clearText());
    onView(withId(R.id.et_message)).perform(typeText("Change Activity ..."), closeSoftKeyboard());

    onView(withId(R.id.btn_second_activity)).perform(click());

    onView(withId(R.id.tv_message)).check(matches(withText("Change Activity ...")));
}
```

<br />

## ButterKnife를 이용한 AndroidTest 예제

이번 AndroidTest를 테스트하기 위한 예제입니다.

- [AndroidTest ButterKnife Example](https://github.com/taehwandev/BlogExample/tree/master/Android/2016-04-29-ButterKnifeExample)

ButterKnife 8.0.1과 Retrofit 2를 사용하였고, MVP를 작성해보았습니다.

아직 코드가 완성된 것은 아니니 Android Test 코드에 대해서만 다루겠습니다.

최종적으로 성공한 경우는 아래와 같이 Tests ran to completion. 와 OK 아이콘이 표시됩니다.

![Screenshot 2016-05-03 23.39.33](/images/2016/2016-05-04-Android-Test-Example/Screenshot 2016-05-03 23.39.33.png)

여기에서 적용한 테스트 코드를 살펴보도록 하겠습니다.

제가 사용한 예제에는 [dexmaker-mockito](https://github.com/crittercism/dexmaker) 사용하였습니다.

```xml
ext {
    supportLibVersion = '23.3.0'

    testRunnerVersion = '0.5'
    espressoVersion= '2.2.2'
    dexmakerVersion = '1.4'
}

androidTestCompile "com.android.support:support-annotations:${supportLibVersion}"
androidTestCompile "com.android.support.test:runner:${testRunnerVersion}"
androidTestCompile "com.android.support.test:rules:${testRunnerVersion}"
androidTestCompile "com.android.support.test.espresso:espresso-core:${espressoVersion}"
androidTestCompile "com.crittercism.dexmaker:dexmaker-mockito:${dexmakerVersion}"
androidTestCompile 'com.jayway.awaitility:awaitility:1.7.0'
```

다음을 테스트하였습니다.

Presenter를 테스트하기 위해서 photoPresenter와 photoPresenterView, photoDataModel을 초기화합니다.

Presenter 만 정상적으로 생성하고, 나머지는 mock으로 생성하였습니다.

실제 RecyclerView에 그리거나, Activity View 이벤트를 전달하지는 않고, mock으로 동작하게 됩니다.

```java
private PhotoPresenter photoPresenter;
private PhotoPresenterView photoPresenterView;
private PhotoDataModel photoDataModel;

@org.junit.Before
public void setUp() throws Exception {
    photoPresenterView = mock(PhotoPresenterView.class);
    photoDataModel = mock(PhotoDataModel.class);
    photoPresenter = new PhotoPresenter(photoPresenterView, RetrofitBinder.getRetrofitBinder(), photoDataModel);
}
```

doAnswer에는 onRefresh가 정상적으로 떨어질 경우 finish[0]을 true로 변경하게 되는데 await() 함수를 통해서 확인하게 됩니다.

```java
@org.junit.Test
public void testLoadPhotos() throws Exception {
  final boolean[] finish = {false};
  doAnswer(new Answer() {
      @Override
      public Object answer(InvocationOnMock invocationOnMock) throws Throwable {
          finish[0] = true;
          return invocationOnMock;
      }
  }).when(photoPresenterView).onRefresh();

  photoPresenter.loadPhotos(1);

  await().until(new Runnable() {
      @Override
      public void run() {
          while (!finish[0]) {
              verify(photoDataModel, atLeastOnce()).add((Photo) any());
          }
      }
  });
  verify(photoPresenterView).onRefresh();
}
```

onItemClick에 대한 예제입니다.

Web으로 전달받은 Photo 데이터를 thenReturn으로 return 하는 mock을 생성합니다.

그리고 photoItemClick 이벤트가 일어나면 onBottomSheetShow에 photo을 전달하도록 하는 테스트입니다.

```java
@org.junit.Test
public void testOnItemClick() throws Exception {
    loadPhotos();

    final int position = 0;
    Photo photo = photoDataModel.getPhotoItem(position);
    when(photoDataModel.getPhotoItem(position)).thenReturn(photo);

    // when
    photoPresenter.photoItemClick(position);

    // then
    verify(photoPresenterView).onBottomSheetShow((Photo) any());
}
```

위의 예제는 Presenter만을 테스트하는 예제입니다.

실제 화면에서 돌아가는 것을 보면서 테스트하려면 ActivityTestRule을 정의하여 테스트할 수 있습니다.

@Before에서 adapter의 size가 0보다 커질 때를 기다리기 위해서 await 을 다음과 같이 구현하였습니다.

```java
@Before
public void setUp() throws Exception {
    activity = rule.getActivity();
    await().until(new Runnable() {
        @Override
        public void run() {
            // adapter size > 0
            while (!(activity.adapter.getSize() > 0)) {

            }
        }
    });
}
```

실제 adapter의 사이즈가 0을 넘으면 다음을 테스트하게 됩니다.

BottomSheet에 이미지를 출력하고, BottomSheet의 Behavior을 변경하는 테스트를 진행합니다.

signal을 이용하여 1초 후 STATE_EXPANDED 상태로 변경하고 HIDDEN 상태로 변경하면 테스트가 종료되게 됩니다.

```java
@Test
public void testShowBottomSheet() throws Throwable {
  rule.runOnUiThread(new Runnable() {

      @Override
      public void run() {
          activity.photoPresenter.photoItemClick(0);
      }
  });

  signal.await(1, TimeUnit.SECONDS);

  activity.bottomSheetBehavior.setState(BottomSheetBehavior.STATE_EXPANDED);

  signal.await(1, TimeUnit.SECONDS);

  activity.bottomSheetBehavior.setState(BottomSheetBehavior.STATE_HIDDEN);
}
```

<br />

## 마무리

ImageView에 실제로 이미지가 그려졌는지도 테스트해보려고 했는데 Espresso만으로는 불가능합니다.

커스텀 또는 Robolectric을 이용하여 테스트가 가능한 것 같습니다.

- [CustomMatchers(frankiesardo)](https://gist.github.com/frankiesardo/7490059)
- [Robolectric](http://robolectric.org/)

ButterKnife을 다루는 예제이며, MVP 패턴을 함께 적용하였습니다.

AndroidTest는 정승욱 님이 작성한 [Android-MVP-AdapterRoleSample](https://github.com/ZeroBrain/Android-MVP-AdapterRoleSample)을 참고하여 작성하였습니다.
