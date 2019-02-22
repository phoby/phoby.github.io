---
title: Selenide로 테스트 자동화 시작하기 - [1]
date: 2019-02-23 01:53 +0900
tags:
  - Automation Test
  - Selenide
  - IntelliJ
  - Gradle
  - Java
---
# Selenide

`Selenide`는 UI 테스트를 위한 간결한(concise) 테스트 자동화 프레임워크입니다. 
Selenium이나 [geb](https://gebish.org)등 다른 훌륭한 도구들이 있고 테스트 라이브러리 들이 있지만 Selenide는 동적 컨텐츠, Javascript, Ajax, timeout 등 불안정한 요소들을 해결하기 위해 만들어진 테스트 자동화 도구 입니다. 

Selenide의 특징들은 [Selenium vs Selenide](https://github.com/selenide/selenide/wiki/Selenide-vs-Selenium)에 자세히 나와있으니 참고해주세요.

> 시작하기 앞서 이 글의 내용은 다음과 같은 환경에서 테스트 했고, Java에 대해 기본적인 지식이 있다고 가정하고 있습니다.
    - MacOS X Mojave
    - Java : OpenJDK Zulu10.2+3 (build 10.0.1+9)
    - IntelliJ 2018.3
    - Gradle

# Gradle 프로젝트 구성하기 

많은 경우 웹 어플리케이션을 반응형 웹으로 개발하고 있지만 아직 모바일 용과 PC 환경에 최적화된 웹 어플리케이션을 따로 개발해서 서비스하는 경우가 많습니다.

따라서, Gradle 프로젝트를 구성할 때 `mobile-web`과 `desktop-web`을 별도 모듈로 구성하여 진행하는 것으로 하겠습니다.

## Project 생성

IntelliJ를 실행합니다.
`File > New > Project...` 메뉴를 선택하여 새로운 프로젝트를 생성합니다.

<p align="center">
  <img src="/images/2019-02-23/selenide-1-01.png" style="width: 500px">
</p>

`GroupId`, `ArtifactId` 그리고 `Version`을 입력합니다.

<p align="center">
  <img src="/images/2019-02-23/selenide-1-02.png" style="width: 500px">
</p>

> GroupId 는 프로젝트 그룹의 식별자, ArtifactId 는 프로젝트 이름이 됩니다.

`Gradle`을 위한 설정을 추가합니다.

<p align="center">
  <img src="/images/2019-02-23/selenide-1-03.png" style="width: 500px">
</p>

프로젝트와 모듈<sup>module</sup>을 설정한 후 'Finish' 버튼을 클릭합니다.

<p align="center">
  <img src="/images/2019-02-23/selenide-1-04.png" style="width: 500px">
</p>

새로운 프로젝트가 생성되었습니다.

<p align="center">
  <img src="/images/2019-02-23/selenide-1-05.png" style="width: 800px">
</p>

## Module 추가

앞서 말씀드린바와 같이 `mobile-web`과 `desktop-web`을 별로 모듈로 구성하기 위해 새로운 모듈을 추가하겠습니다.

`File > New > Module...` 메뉴를 선택하여 새로운 모듈을 추가합니다.

<p align="center">
  <img src="/images/2019-02-23/selenide-1-06.png" style="width: 500px">
</p>

모듈을 추가할 때 `Add as module to` 값이 프로젝트 생성할때 입력한 `ArtifactId`와 일치하는지 확인하고, 새로운 모듈을 위한 `ArtifactId` 값을 입력해줍니다.
`GroupId`와 `Version`은 프로젝트의 값을 상속(inherit)하도록 설정합니다.

<p align="center">
  <img src="/images/2019-02-23/selenide-1-07.png" style="width: 500px">
</p>

다음은 새롭게 생성하는 모듈 이름과 생성될 파일들의 위치를 변경할 수 있지만 'Finish' 버튼을 클릭해서 모듈 생성을 완료합니다.

<p align="center">
  <img src="/images/2019-02-23/selenide-1-08.png" style="width: 500px">
</p>

프로젝트 윈도우에 `desktop-web` 모듈과 `mobile-web` 모듈이 생성되고 빌드 도구인 `gradle`과 관련된 파일들이 생성됩니다.

<p align="center">
  <img src="/images/2019-02-23/selenide-1-09.png" style="width: 800px">
</p>

지금까지 진행한 과정을 모두 마쳤다면, 아래 이미지와 같은 프로젝트 구조가 생성됩니다.

<p align="center">
  <img src="/images/2019-02-23/selenide-1-10.png" style="width: 300px">
</p>

이렇게 생성된 모듈은 `Gradle`에서 각각의 프로젝트로 인식됩니다.
`settings.gradle` 파일을 더블 클릭해서 내용을 확인해보면 자동으로 모듈명이 포함<sup>include</sup>되어 있는 것을 확인할 수 있습니다.

``` groovy
rootProject.name = 'web-testing'
include 'desktop-web'
include 'mobile-web'
```

# 정리

웹 어플리케이션을 테스트하기 위해서 `Selenide`를 선택한 이유를 말씀드렸습니다. 그리고, 웹 어플리케이션 테스트를 자동화하기 위한 첫번째 단계로 프로젝트를 생성하고 멀티 모듈을 구성하는 방법까지 정리했습니다.

다음엔, `build.gradle` 파일에서 테스트를 위한 라이브러리 의존성을 추가하고 빌드(테스트) 실행하는 부분을 정리해보도록 하겠습니다.

# 참고
- https://selenide.org
- https://gradle.org
- https://jojoldu.tistory.com/123
- https://www.jetbrains.com/help/idea/creating-and-running-your-first-java-application.html
