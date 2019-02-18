---
title: Jenkins 빌드 실행 결과를 Slack 알림으로 받기 
date: 2019-02-18 01:00 +0900
tags:
  - Slack
  - jenkins
  - Jenkins Job
  - Test
  - Notification 
---
많은 개발자 분들이 개발하는 과정에서 테스트, 빌드, 배포를 자동화하는데 Jenkins를 사용하고 있습니다.
저는 개발자는 아니지만, RestAssured, Selenide 등의 도구로 테스트 코드를 작성하고 Jenkins를 통해 테스트가 자동으로 실행되도록 하고 있습니다.
그러다보니 가끔 Jenkins에 접속해서 테스트가 성공했는지 실패했는지 확인해왔습니다.

하지만, 이 글에서는 매번 Jenkins에 접속해야만 테스트 결과를 확인할 수 있었던 것을 Slack 알림으로 대체한 과정을 정리했습니다.

# Slack 시작하기

> 이미 Slack을 이용하고 있다면 `Jenkins App 추가' 단계로 이동해주세요.

`Slack`은 커뮤니케이션을 위한 메신저형 협업도구 입니다. 
커뮤니케이션과 협업에 사용해온 메일이나 메신저의 한계를 벗어나 실시간 커뮤니케이션과 협업이 가능해 많은 개발자와 개발 조직에서 사용하고 있습니다.

[Slack 시작하기](https://slack.com/get-started) 링크를 통해 시작할 수 있습니다.

## 새로운 `workspace` 및 `channel` 생성

새로운 `workspace`를 생성합니다.
<p align="center">
  <img src="/images/2019-02-18/start-with-a-workspace.png">
</p>

새로운 `workspace` 생성을 위해 이메일 주소를 입력하고 'Confirm' 버튼을 클릭합니다.
<p align="center">
  <img src="/images/2019-02-18/create-a-new-workspace.png">
</p>

Slack에서 이메일 주소로 여섯자리 인증 코드를 보내줍니다. 인증 코드 6자리를 입력합니다.
<p align="center">
  <img src="/images/2019-02-18/check-your-email.png">
</p>

회사 또는 팀 이름을 입력합니다.
<p align="center">
  <img src="/images/2019-02-18/your-company-or-team.png">
</p>

채널 이름을 입력합니다.
<p align="center">
  <img src="/images/2019-02-18/your-team-is-working-on.png">
</p>

채널에 초대할 팀 구성원을 추가합니다. 나중에 추가할수도 있으므로 지금은 살포시 건너 뛰도록 하겠습니다. 
<p align="center">
  <img src="/images/2019-02-18/add-teammates.png">
</p>

드디어 `#test-notification`이라는 채널이 생성됐습니다.
<p align="center">
  <img src="/images/2019-02-18/see-your-channel-in-slack.png">
</p>

## Jenkins 앱 추가

이제 Slack 에서 Jenkins 와 연동하기 위한 설정을 진행해 보겠습니다.
<p align="center">
  <img src="/images/2019-02-18/add-apps.png">
</p>

Jenkins 를 검색하면 `Jenkins CI`이 조회됩니다. Install 버튼을 클릭합니다.
<p align="center">
  <img src="/images/2019-02-18/browse-apps.png">
</p>

Jenkins CI 앱 정보 페이지가 나오면, 왼쪽의 `Install` 버튼을 클릭합니다.
<p align="center">
  <img src="/images/2019-02-18/jenkins-ci.png">
</p>

Jenkins 알림을 받을 채널을 선택하고, `Add Jenkins CI integration` 버튼을 클릭해서 설치를 완료합니다.
<p align="center">
  <img src="/images/2019-02-18/add-jenkins-ci-integration.png">
</p>

설치 과정이 마무리되면 Slack과 Jenkins의 연동 가이드가 나오며, 가이드 절차에 따라 설정해주면 됩니다. 특히, `Step 3`의 `Base URL`과 `Integration Token`은 Jenkins 설정에 필요한 값으로 따로 기록해 두는 것이 좋습니다.

# Jenkins 설정

## Slack Notification Plugin 설치

Jenkins 대시보드에서 `Manage Jenkins` 메뉴를 클릭합니다.
<p align="center">
  <img src="/images/2019-02-18/manage-jenkins.png">
</p>

`Manage Plugin` 메뉴를 선택하고 `Available` 탭에서 `Slack Notification` 플러그인을 검색하고 설치합니다.
<p align="center">
  <img src="/images/2019-02-18/plugin-slack-notification.png">
</p>

## Slack Notification 설정

`Slack Notification` 설치가 완료되면, `Manage Jenkins`에서 `Configure System`을 클릭합니다.

`Global Slack Notifier Settings` 항목을 다음과 같이 설정합니다.
연동 가이드의 `Base URL`을 `Slack compatible app URL`에 입력합니다. 그리고, `Integration Token`을 `Integration Token Credential ID`로 등록 합니다.

> Secret text 로 Credential ID를 생성하면 됩니다.

마지막으로, 알림을 받을 채널 ID를 입력하고 테스트 합니다. 테스트 결과가 `Success`로 노출되면 성공!!
<p align="center">
  <img src="/images/2019-02-18/slack-notifi-setting.png">
</p>

테스트 알림이 Slack 채널로 전송됩니다.
<p align="center">
  <img src="/images/2019-02-18/test-notifi.png">
</p>

## 빌드 후 조치 설정

Slack과 Jenkins 연동을 위한 설정은 이제 거의 마무리 됐습니다. 마지막으로 Jenkins 에서 Job이 실행될 때 `빌드 후 조치` 설정을 통해서 테스트(빌드) 성공/실패 여부를 Slack 알림을 통해 확인할 수 있도록 설정을 해야합니다.

알림을 받을 `프로젝트 > Configure > Post-build Actions` 항목으로 이동한 후에 `Slack Notifications`를 선택합니다.
<p align="center">
  <img src="/images/2019-02-18/add-post-build-action.png">
</p>

Slack 알림 받을 상황을 선택합니다. 저는 빌드가 시작할 때와 빌드 성공했을 때 그리고 빌드가 실패했을 때 알림을 받도록 설정했습니다.
<p align="center">
  <img src="/images/2019-02-18/slack-notifications.png">
</p>

저장하면 이제 모든 과정이 끝났으니 빌드를 한번 실행해 보겠습니다.

13번째 빌드가 시작했을 때와 빌드(테스트)가 성공했을 때 슬랙 채널로 알림 메시지가 잘 들어오는 것을 확인할 수 있습니다.
<p align="center">
  <img src="/images/2019-02-18/slack-notification-by-jenkins.png">
</p>

# 마무리

업무 중 많이 사용하는 Slack과 Jenkins를 조금만 시간을 들여 연동하면, 귀찮은 일들을 좀 더 쉽고 빠르게 처리할 수 있게 되었습니다. 지금은 Slack 메시지가 단순하지만, 메시지 커스터마이징을 하면 좀 더 가시적으로 바꿀 수 있을 텐데요.. 기회되면 그것도 한번 정리해 봐야겠습니다.

# 참고

https://slack.com
https://medium.com/appgambit/integrating-jenkins-with-slack-notifications-4f14d1ce9c7a
https://dogbirdfoot.tistory.com/16
