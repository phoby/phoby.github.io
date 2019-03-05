---
title: Selenide로 테스트 자동화 시작하기 - 2
date: 2019-03-05 23:53 +0900
tags:
  - Automation Test
  - Selenide
  - Selenium
  - WebDriver
  - IntelliJ
  - Gradle
  - Java
---
지난 글([Selenide로 테스트 자동화 시작하기 - 1](https://phoby.github.io/start-web-automation-test-width-selenide/))에서는 Gradle 멀티 프로젝트를 생성하는 과정에 대해 정리했었습니다.
이번에는 `build.gradle` 파일에 테스트에 필요한 라이브러리 의존성을 추가하고 간단한 테스트 코드를 gradle 명령으로 실행하는 방법까지 정리해보겠습니다.

> 예제 코드는 [github](https://github.com/phoby/web-testing)에서 확인할 수 있습니다. 

ntelliJ에서 `mobile-web` 모듈의 `build.gradle` 파일을 열면 아래 코드와 같이 생성되어 있을 겁니다. (그래야 해요..)

``` groovy
plugins {
    id 'java'
}

group 'io.github.phoby'
version '1.0-SNAPSHOT'

sourceCompatibility = 1.8

repositories {
    mavenCentral()
}

dependencies {
    testCompile group: 'junit', name: 'junit', version: '4.12'
}
```

> `build.gradle`에 대해 깊이 이해하고 싶은 경우 [Gradle](https://gradle.org/) 문서를 참고해주세요.

우선 `Selenide` 최신 버전을 [중앙 저장소](https://search.maven.org)에서 검색합니다. 

<p align="center">
  <img src="/images/2019-03-05/selenide-2-01.png" alt="search.maven.org">
</p>

위 검색 결과를 참고해서 `build.gradle` 파일의 `dependencies`에 라이브러리를 추가합니다.

의존 라이브러리는 `그룹(Group ID) : 이름(Artifact ID) : 버전(Version)` 순서대로 작성하며 이 규칙대로 추가하면 아래와 같습니다.

``` groovy
dependencies {
  testCompile group: 'junit', name: 'junit', version: '4.12'
  compile group: 'com.codeborne', name: 'selenide', version: '5.2.0'
}
```

이렇게 `build.gradle` 파일에 기본적인 의존성 라이브러리를 추가했습니다. 그러면, 단지 2개의 라이브러리 정보만 추가했을 뿐인데 의존 관계에 있는 라이브러리들을 자동으로 다운로드 받고 사용할 수 있도록 해줍니다.

<p align="center">
  <img src="/images/2019-03-05/selenide-2-02.png" >
</p>

자 여기까지 진행했으면 IntelliJ에서 터미널을 열고 `gradle`을 실행해 보겠습니다.

``` shell
$ ./gradlew clean test
# 또는
$ gradle clean test
```

아무 내용도 없지만, 빌드가 성공한 것을 확인할 수 있습니다. 
만약 빌드가 실패한다면 `build.gradle` 파일이 잘못 구성되었을 수 있습니다.

<p align="center">
  <img src="/images/2019-03-05/selenide-2-03.png" style="width: 200px">
</p>

# 테스트 코드 추가

이제 구글(https://www.google.com)에 접속한 후에 검색을 시도하는 코드를 추가해보겠습니다.

`mobile-web` 모듈의 `main` 디렉토리와 `test` 디렉토리에 각각 package를 추가합니다.
그리고, `main` 디렉토리에는 `GooglePage`라는 클래스를 생성합니다.
`test` 디렉토리에는 `GooglePageTest`라는 테스트 클래스를 생성합니다.

<p align="center">
  <img src="/images/2019-03-05/selenide-2-04.png" style="width: 300px">
</p>

### GooglePage.java
``` java
package io.github.phoby.selenide.mobile;

import static com.codeborne.selenide.Selectors.byName;
import static com.codeborne.selenide.Selenide.$;

public class GooglePage {

   public void search(String query) {
      $(byName("q")).setValue(query).pressEnter();
   }
}
```

### GooglePageTest.java
``` java
package io.github.phoby.selenide.mobile;

import org.junit.Test;

import static com.codeborne.selenide.CollectionCondition.sizeGreaterThan;
import static com.codeborne.selenide.Condition.text;
import static com.codeborne.selenide.Selenide.*;

public class GooglePageTest {
   
   @Test
   public void example() {
      open("https://www.google.com");
      new GooglePage().search("Selenide로 테스트 자동화하기");
      
      $$("#ires .g").shouldHave(sizeGreaterThan(1));
      $$("#ires .g").get(0).shouldHave(text("Selenide로 테스트 자동화 시작하기"));
      
      sleep(1000);
   }
}
```

코드에 대한 설명을 하기전에 터미널 창을 열고 `./gradlew` 명령으로 테스트를 실행해 보도록 하겠습니다.

``` shell
$ ./gradlew clean test

<============-> 93% EXECUTING [3s]
> :mobile-web:test > 0 tests completed
> :mobile-web:test > Executing test io.github.phoby.selenide.mobile.GooglePageTest
```
빌드 스크립트가 실행되면서 자동으로 크롬 브라우저가 실행되며, `GooglePageTest.java` 파일에 정의한 검색어 `Selenide로 테스트 자동화하기`가 검색 창에 입력됩니다.

<p align="center">
  <img src="/images/2019-03-05/selenide-2-05.png">
</p>

그 후에는 검색된 결과 페이지가 노출됩니다. 
저의 블로그 글이 가장 상단에 노출되는 군요!! 

<p align="center">
  <img src="/images/2019-03-05/selenide-2-06.png">
</p>

터미널 창에서도 빌드(테스트)가 성공적으로 완료된 것을 확인할 수 있습니다.

``` shell
$ ./gradlew clean test

BUILD SUCCESSFUL in 26s
5 actionable tasks: 4 executed, 1 up-to-date
```

# 정리

지금까지 `Selenide`로 테스트 자동화를 하기 위해 빌드 스크립트인 `build.gradle` 파일에 의존 라이브러리를 추가하고 아주 간단한 예제 코드를 실행해 봤습니다.
`Selenium`을 사용해보신 분들을 아시겠지만, `Selenium`에 비해 매우 간결하게 브라우저를 자동화 할 수 있습니다.

다음 글에서는 이번 글에서 추가한 코드를 중심으로 `Selenide` 사용 방법을 몇번에 나눠 정리해 보도록 하겠습니다.

---

# 참고

- https://selenide.org
- https://araikuma.tistory.com/466?category=782572
- http://kwonnam.pe.kr/wiki/gradle/java
- https://github.com/selenide/selenide/wiki/Selenide-vs-Selenium


