---
title: adb로 딥 링크 테스트하기
date: 2020-12-13 03:00 +0900
tags:
  - android
  - adb 
  - deep links
  - test
---

Android 모바일 어플리케이션 테스트를 하다보면 `URI Scheme 방식의 딥링크`를 테스트해야하는 경우가 종종 발생합니다.
이런경우, adb(Android Debug Bridge)를 am(Activity Manager)와 함께 사용하여 딥 링크용으로 지정한 URI가 정상적으로 동작하는지 테스트할 수 있습니다. 

> adb로 URI Scheme 방식의 딥링크를 테스트하기 위해서는 adb가 설치되어 있어야 합니다. (참고: [Android adb를 활용한 테스트](https://phoby.github.io/adb))

### Android 장비와 연결

* Android 장비에 데이터 통신이 가능한 케이블을 연결하고 adb가 설치된 macOS와 연결합니다.

``` shell
$ adb devices

List of devices attached
06157df6f347d70e	device
```

### adb 명령으로 딥링크 테스트

`URI Scheme 방식의 딥링크`를 테스트하는 명령은 아래와 같습니다.

``` shell
   $ adb shell am start
            -a android.intent.action.VIEW
            -d {URI} {APP PACKAGE NAME}
```

* `-a` *action* : [인텐트](https://developer.android.com/guide/components/intents-filters?hl=ko) 작업을 지정하며, 한 번만 선언할 수 있습니다. (예: android.intent.action.VIEW)
* `-d` *data_uri* : 딥링크 URI를 지정하며, 한 번만 선언할 수 있습니다. (예: baemin-beta://something)

> 주의 1 : 딥링크 URI에 파라메터가 여러개 있는 경우 `&`를 `\&`로 변경해야 제대로 동작합니다.
> 주의 2 : 모든 딥링크가 위와 같은 방식으로 테스트가 가능한 것은 아닙니다.

