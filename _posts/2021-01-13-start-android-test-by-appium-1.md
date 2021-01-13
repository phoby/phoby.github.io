---
title: Android 앱 테스트 자동화 - 1
date: 2021-01-13 03:00 +0900
tags:
  - android
  - android-sdk
  - appium 
  - java
  - test
---

모바일 어플리케이션의 테스트를 자동화하는 방법에는 여러가지가 있습니다. 상용 도구를 사용하는 방법도 있고, 삽질해가면서 직접 개발하는 방법도 있습니다.
저는 주로 후자를 선호하는데요.. 그래서, 이번에는 `Appium`으로 android 기반의 어플리케이션 테스트를 자동화하는 과정을 진행해보도록 하겠습니다.

Appium은 native, hybrid and mobile web 등 모든 유형의 모바일 어플리케이션을 테스트할 수 있는 오픈소스 테스트 자동화 프레임워크이며 좀 더 자세한 내용은 검색해보시면 됩니다.

# Appium 설치

가장먼저 `node`를 설치합니다.

``` shell
# node 설치
$ brew install node

# Appium 설치
$ npm install -g appium

# Appium 실행 가능 상태 체크할 수 있는 툴 설치
$ npm install -g appium-doctor
```

설치가 완료되면 `appium-doctor` 명령을 실행해서 Appium 실행 가능 상태를 체크해봅니다.

``` shell
$ appium-doctor --android
```

아마 처음 Appium 설치하신 분들은 여러가지 WARN 메시지보고 깜짝 놀라셨을 수도 있지만, 가장 중요한 것은 빨간색 X 표시되어있는 몇가지 항목만 주의해서 보시면 됩니다.

![](/images/2021-01-13/appium-doctor-1.jpg)

환경변수에 `ANDROID_HOME`과 `JAVA_HOME` 등이 설정되어 있지 않아서 발생하는 문제이므로 다음 단계에서 `android-sdk`를 설치하고 환경변수 설정을 진행해 보도록 하겠습니다.

> JAVA 설치는 [SDK! 으로 Java 버전 관리하기](https://phoby.github.io/sdkman/) 글을 참고해주세요-

# android-sdk 설치
``` shell
$ brew install --cask android-sdk
```
