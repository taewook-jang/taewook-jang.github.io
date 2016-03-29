---
layout: post
title: example
---

AndroidManifest.xml에서 다음을 정의하면 Multi-Window를 지원/해제 할 수 있습니다.

```
android:resizeableActivity=["true" | "false"]
```
이 코드를 &lt;actiity&gt; 또는 &lt;application&gt;에 각각 넣을 수 있습니다.
application이 false 이면 해당 앱은 false가 기본으로 처리되어 멀티 윈도우가 동작하지 않습니다.
activity 기반으로도 각각 페이지별 true/false를 적용하게 되면 동작이 다르게 되는 것이죠.
