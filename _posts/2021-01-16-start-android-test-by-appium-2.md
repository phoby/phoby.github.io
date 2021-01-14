---
title: Android 앱 테스트 자동화 - 2
date: 2021-01-16 03:00 +0900
tags:
  - android
  - android-sdk
  - avd
  - emulator
  - appium 
  - java
  - test
---

오늘은 지난 글에 이어서 `AVD(Android Virtual Device)` 패키지들을 설치해보도록 하겠습니다.

## AVD 패키지 설치

AVD 패키지들을 설치하기 위해서는 `android-sdk`가 설치된 경로로 이동합니다.

``` shell
$ cd ~/android-sdk/tools/bin
```

이동 후에는 아래와 같이 `sdkmanager`를 이용해서 설치 가능한 패키지 목록을 확인할 수 있습니다.

> 만약 sdkmanager 실행 시 `java.lang.NoClassDefFoundError: javax/xml/bind/annotation/XmlSchema` 에러가 발생하는 경우 Java 버전을 문제일 수 있습니다. (저는 openjdk version "1.8.0_232"를 사용하고 있습니다.)

``` shell
# 설치된 패키지와 설치 가능한 패키지 목록 확인
$ sdkmanager --list

Installed packages:=====================] 100% Computing updates...
  Path               | Version | Description                    | Location
  -------            | ------- | -------                        | -------
  build-tools;30.0.3 | 30.0.3  | Android SDK Build-Tools 30.0.3 | build-tools/30.0.3/
  tools              | 26.1.1  | Android SDK Tools 26.1.1       | tools/

Available Packages:
  Path                                                   | Version  | Description
  -------                                                | -------  | -------
  add-ons;addon-google_apis-google-15                    | 3        | Google APIs
  ... 생략 ...
  system-images;android-30;google_apis_playstore;x86     | 9        | Google Play Intel x86 Atom System Image
  system-images;android-30;google_apis_playstore;x86_64  | 10       | Google Play Intel x86 Atom_64 System Image  
```  

필요한 패키지들을 설치합니다.
> 테스트에는 android 11 (API 버전 30)을 사용할 예정이므로 해당 버전에 맞는 패키지를 설치합니다.

``` shell
# build-tools 설치
$ sdkmanager "build-tools;30.0.3"
# Warning 이 발생하는 이유를 모르겠습니다.. 일단 무시;;
Warning: File /usr/local/share/android-sdk/.android/repositories.cfg could not be loaded.
[=======================================] 100% Computing updates...

# emulator 설치
$ sdkmanager emulator
Warning: File /usr/local/share/android-sdk/.android/repositories.cfg could not be loaded.
[=======================================] 100% Unzipping... emulator/lib/pc-bios

# platform-tools 설치
$ sdkmanager platform-tools
Warning: File /usr/local/share/android-sdk/.android/repositories.cfg could not be loaded.
[=======================================] 100% Unzipping... platform-tools/systr

# android 11 platform 설치
$ sdkmanager "platforms;android-30"
Warning: File /usr/local/share/android-sdk/.android/repositories.cfg could not be loaded.
[=======================================] 100% Unzipping... android-11/framework

# 마지막으로, android 11에 해당하는 system-image 설치
$ sdkmanager "system-images;android-30;google_apis;x86_64"
Warning: File /usr/local/share/android-sdk/.android/repositories.cfg could not be loaded.
[=======================================] 100% Unzipping... x86_64/data/system/d
```

이제 필요한 패키지들 설치가 완료되었습니다. `sdkmanager --list` 명령으로 패키지들이 모두 잘 설치되었는지 확인해보겠습니다.

``` shell
$ sdkmanager --list

Warning: File /usr/local/share/android-sdk/.android/repositories.cfg could not be loaded.
Installed packages:=====================] 100% Computing updates...
  Path                                        | Version | Description                                | Location
  -------                                     | ------- | -------                                    | -------
  build-tools;30.0.3                          | 30.0.3  | Android SDK Build-Tools 30.0.3             | build-tools/30.0.3/
  emulator                                    | 30.3.5  | Android Emulator                           | emulator/
  patcher;v4                                  | 1       | SDK Patch Applier v4                       | patcher/v4/
  platform-tools                              | 30.0.5  | Android SDK Platform-Tools                 | platform-tools/
  platforms;android-30                        | 3       | Android SDK Platform 30                    | platforms/android-30/
  system-images;android-30;google_apis;x86_64 | 10      | Google APIs Intel x86 Atom_64 System Image | system-images/android-30/google_apis/x86_64/
  tools                                       | 26.1.1  | Android SDK Tools 26.1.1                   | tools/
```

AVD 패키지 설치가 완료되었으니, AVD를 생성해보도록 하겠습니다.
AVD 생성을 위해서는 `avdmanager`를 사용합니다.

``` shell
$ avdmanager --help

Usage:
      avdmanager [global options] [action] [action options]
      Global options:
  -s --silent     : Silent mode, shows errors only.
  -v --verbose    : Verbose mode, shows errors, warnings and all messages.
     --clear-cache: Clear the SDK Manager repository manifest cache.
  -h --help       : Help on a specific command.

Valid actions are composed of a verb and an optional direct object:
-   list              : Lists existing targets or virtual devices.
-   list avd          : Lists existing Android Virtual Devices.
-   list target       : Lists existing targets.
-   list device       : Lists existing devices.
- create avd          : Creates a new Android Virtual Device.
-   move avd          : Moves or renames an Android Virtual Device.
- delete avd          : Deletes an Android Virtual Device.
```

테스트에 사용할 AVD를 아래 명령으로 생성합니다.

``` shell
# AVD 생성 가능한 장치 목록을 조회합니다
$ avdmanager list device

# "PIXEL XL"로 에뮬레이팅되는 AVD를 생성 합니다.
$ avdmanager create avd -f -n PIXEL_XL_API_30 -k "system-images;android-30;google_apis;x86_64" --device "pixel_xl"
Auto-selecting single ABI x86_64========] 100% Fetch remote repository...

# -n : AVD 이름을 지정합니다.
# -k : AVD 패키지를 지정합니다.
# --device : AVD 장치를 지정합니다.
```

AVD가 생성되었으면 `emulator`를 실행해보도록 하겠습니다.

``` shell
$ emulator -avd PIXEL_XL_API_30
```

![](/images/2021-01-16/emulator.png)

# 정리
지금까지 `AVD (Android Virtual Device)` 관련 패키지를 설치하고 테스트할 AVD를 생성하고 emulator 구동까지 확인했습니다.
다음 시간부터는 통합개발환경(IDE)에서 프로젝트를 생성한 후 테스트 코드를 작성해보도록 하겠습니다.
