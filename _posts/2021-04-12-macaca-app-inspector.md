---
title: Macaca App Inspector
date: 2021-04-12 03:00 +0900
tags:
  - appium
  - inspector
  - macaca 
  - android
  - ios
---

`Appium` 같은 도구를 이용해서 모바일 테스트 자동화를 수행해야할 때 반드시 필요한 유틸이 `Appium Inspector` 또는 `UI Automator Viewer` 같은 도구 입니다.

이 도구들은 테스트 대상이되는 앱의 스냅샷, 그 스냅샷의 UI를 구성하는 엘리먼트(elements)의 트리 그리고, 선택한 엘리먼트의 속성 정보를 알 수 있도록 해줍니다.

이런 엘리먼트 트리 정보나 속성 정보들은 테스트에 필요한 엘리리먼트를 식별할 수 있게 해줍니다.

이 글에서는 이와 같은 도구들 중 사용하기 쉬운 `macaca app-inspector`의 사용 법에 대해서 알아 보도록 하겠습니다.

<br>

# 설치
> Macaca App-Inspector 설치를 위해서는 `node.js` 가 설치 되어있어야 합니다.

<br>

## macaca-cli 설치
``` shell
$ npm install -g macaca-cli 
```

`macaca-cli` 를 설치했다면 테스트 대상 모바일 플랫폼에 맞도록 환경 설정이 필요합니다.

> Android SDK 또는 Xcode 설치는 [Macaca 환경 설정 문서](https://macacajs.github.io/guide/environment-setup.html)를 참조해주세요.

아래 명령으로 테스트 대상 모바일 플랫폼 환경이 준비되었는지 확인할 수 있습니다.

``` shell
$ macaca doctor
```
아래 그림처럼 Android 플랫폼은 환경이 준비된 것을 확인할 수 있습니다.
![](/images/2021-04-12/macaca-doctor.png)

<br>

## App Inspector 설치

아래 명령으로 App Inspector를 설치할 수 있습니다.

``` shell
$ npm install -g app-inspector 
```

<br>

# CLI 환경에서 시작

App Inspector 설치가 완료되면, 아래 명령으로 실행할 수 있습니다.

> 여기서 잠깐!! 위 명령을 실행할려면 `DEVICE ID` 가 반드시 필요하며 아래에서 가볍게 알아보도록 하겠습니다.

``` shell
$ app-inspector -u ${YOUR_DEVICE_ID}
```

위 명령을 실행하면, 아래와 같은 로그 정보가 표시됩니다.
``` shell
>> UIAutomatorWD http server ready
>> Android device started: R3CN80CXKVY
>> inspector start at: http://172.30.1.13:5678
```

그 후에 브라우저가 실행되고 `http://172.30.1.13:5678` 링크를 엽니다. 

![](/images/2021-04-12/app-inspector)

<br>

# 정리

테스트에 필요한 엘리먼트 트리 정보나 속성 정보를 식별할 수 있도록 도와주는 `macaca app-inspector` 에 대해 알아보았습니다. 설치도 쉽고, 사용법도 간단해서 자주 사용하고 있습니다.

혹시, 다른 도구 사용 중 불편함을 느끼셨다면 한번 사용해 보시길 추천드립니다.
그리고, 더 좋은 도구가 있다면 추천도 부탁드립니다.

<br>

## [참고] DEVICE ID 얻는 방법

### `Android` 플랫폼
Android는 설치된 `adb` 로 연결된 장치 ID를 얻을 수 있습니다.

``` shell
$ adb devices

List of devices attached
R3CN80CXKVY	device		# 실제 장치
emulator-5554	device		# 에뮬레이터 장치
```

### `iOS` 플랫폼
iOS는 실제 장치와 시뮬레이터 장치 정보를 얻는 방법이 다릅니다.

* 시뮬레이터 장치 정보
``` shell
$ xcrun simctl list
```
위 명령을 실행하면 `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` 형태의 `UDID` 값을 얻을 수 있습니다.

* 실제 장치 정보   
iOS 실제 연결된 장치 정보는 Xcode > Window > Devices and Simulators 를 통해서 확인하거나, Finder 에서 연결된 장치를 선택하면 `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` 형태의 `UDID` 값을 얻을 수 있습니다.

