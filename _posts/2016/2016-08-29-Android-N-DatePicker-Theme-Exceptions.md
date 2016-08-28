---
layout: post
title: Android 7.0(Nougat)에서 DatePickerDialog 예외사항
categories: [AndroidDev]
tags: [Android, AndroidDev]
fullview: false
comments: true
published: true
---

안드로이드 7.0 누가에서 DatePickerDialog 예외 사항을 살펴보려고 합니다.

7.0부터는 Holo 테마를 활용한 DatePickerDialog 처리가 시스템적으로 동작하지 않습니다.

뭐 커스텀을 하시거나, Theme 자체를 구현하였다면 다른 이야기가 되겠지만 우선 아래와 같이 동작이 상의 함을 확인할 수 있습니다.

![Sample01]

똑같은 코드를 사용하여 Date picker에 테마를 적용하였지만 위와 같이 API 23(마시멜로우 단말기)에서는 정상으로 보이지만 오른쪽의 API 24(누가)에서는 Material 디자인으로 보입니다.

<br />

## Meterial 디자인으로는?

Meterial 디자인으로 기본 적용한다면 다음과 같이 보입니다. 정상적인 디자인의 모습을 볼 수 있습니다.

![Sample02]

위에서 보았던 검은색, 흰색 여백 부분이 추가로 발생하지 않습니다. 확인과 취소 버튼도 Meterial 디자인으로 보입니다.


<br />

## 그래서

7.0 누가의 경우 Holo Dialog theme가 제외되었음을 확인할 수 있습니다.

문제는 7.0에서도 Holo 테마를 사용해야 한다는 점입니다.

- Custom DatePicker 제공
- HoloThme를 가져와서 내장
- 시스템에 맞도록 교체

가장 간단한 방법은 시스템에 맞도록 교체하는 방법이겠습니다. 하지만 제조사가 한두 개도 아니고, 각각 OS마다 다르게 보이므로 가장 쉽게 선택하는 것은 Custom이겠죠.

일단 현상을 알았으니 다음의 방법을 선택해볼 수도 있습니다.

```java
Calendar calendar = Calendar.getInstance();
int year = calendar.get(Calendar.YEAR);
int month = calendar.get(Calendar.MONTH);
int day = calendar.get(Calendar.DAY_OF_MONTH);
Context context = new ContextThemeWrapper(this, android.R.style.Theme_Holo_Light_Dialog);
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
    // API 24 이상일 경우 시스템 기본 테마 사용
    context = this;
}
DatePickerDialog datePickerDialog = new DatePickerDialog(context, dateSetListener, year, month, day);
datePickerDialog.show();
```

Listener은 다음과 같이 구현합니다.

```java
private DatePickerDialog.OnDateSetListener dateSetListener = new DatePickerDialog.OnDateSetListener() {
    @Override
    public void onDateSet(DatePicker view, int year, int monthOfYear, int dayOfMonth) {
        Snackbar.make(floatingActionButton, year + "-" + monthOfYear + "-" + dayOfMonth, Snackbar.LENGTH_SHORT).show();
    }
};
```

그냥 단순하게 버전에 따라서 시스템의 기본 테마를 적용할지 holo를 적용할지를 결정합니다. 더 좋은 방법은 테마 자체를 다음으로 강제하여 사용하는 방법이겠습니다.

MaterialDateTimePicker를 만들어 둔 것들이 있으니 이런 것을 활용하여도 좋겠습니다.(기본 Material 디자인은 API 21 이상부터 사용 가능하므로...)

- [MateraiDateTimePicker - Github](https://github.com/wdullaer/MaterialDateTimePicker)
- 결국 또 커스텀...


<br />

## 마무리

Android N에서 Holo 테마를 사용한 DatePicker가 깨지는 것을 발견하여 정리하게 되었습니다. 당장 커스텀이 어렵다면 버전 분기를 하여 처리하는게 좋다고 생각됩니다.

감사합니다.


[Sample01]:   /images/2016/2016-08-29-Android-N-DatePicker-Theme-Exceptions/Screenshot_2016-08-28_22.27.43.png

[Sample02]:   /images/2016/2016-08-29-Android-N-DatePicker-Theme-Exceptions/Screenshot_2016-08-28_22.27.55.png
