---
title: Jenkins 로 테스트 자동으로 실행하기
date: 2019-02-02 01:00 +0900
tags:
  - docker
  - jenkins
  - Jenkins Job
  - Github
  - Webhook
  - Test
---
# Jenkins
`젠킨스(이하 Jenkins)`가 무엇인지 모르는 분은 거의 없을거라고 생각합니다. jenkins는 개발 작업 자동화 뿐 아니라 소스 코드 저장소 대한 지속적인 통합과 지속적인 배포 환경을 구축하기 위한 간단한 방법을 제공하는 도구입니다. 

[Jenkins 공식 웹사이트](https://jenkins.io)

이 포스트에서는 Jenkins를 활용하여 github 저장소에 있는 테스트 코드가 push 됐을 때 자동으로 테스트를 실행하는 과정을 정리해 보겠습니다.

# Jenkins 설치

가장 최신 버전의 `jenkins` 이미지를 다운로드 합니다.
``` shell
$ docker pull jenkins/jenkins:lts
Using default tag: latest
latest: Pulling from library/jenkins
55cbf04beb70: Downloading [===============================================>   ]  43.12MB/45.31MB
1607093a898c: Download complete
9a8ea045c926: Download complete
d4eee24d4dac: Downloading [==========>                                        ]  10.16MB/50.06MB
c58988e753d7: Download complete
794a04897db9: Download complete
70fcfa476f73: Download complete
0539c80a02be: Downloading [===>                                               ]  10.17MB/133.9MB
54fefc6dcf80: Pull complete
911bc90e47a8: Pull complete
38430d93efed: Pull complete
7e46ccda148a: Pull complete
c0cbcb5ac747: Pull complete
35ade7a86a8e: Pull complete
aa433a6a56b1: Pull complete
841c1dd38d62: Pull complete
b865dcb08714: Pull complete
5a3779030005: Pull complete
12b47c68955c: Pull complete
1322ea3e7bfd: Pull complete

Digest: sha256:eeb4850eb65f2d92500e421b430ed1ec58a7ac909e91f518926e02473904f668
Status: Downloaded newer image for jenkins:latest
```

최신 버전의 이미지 다운로드가 완료되면, `docker images` 명령으로 다운로드 받은 이미지 목록을 확인할 수 있습니다.

``` shell
$ docker images

REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
jenkins/jenkins     latest              cd14cecfdb3a        6 months ago        696MB
```

`jenkins`를 새로운 `docker container`에서 구동합니다.
각 옵션에 대해 간단히 설명하면.. [참고](https://docs.docker.com/engine/reference/commandline/run/)
- `-d` : 실행되는 컨터이너를 백그라운드로 실행하고 컨테이너 ID를 출력합니다.
- `-p` : 컨테이너에서 사용하는 포트를 호스트 포트로 매핑 합니다.
- `-v` : 호스트에 있는 파일이나 디렉토리를 지정하여 컨테이너에 마운트 합니다. 이것은 컨테이너 내부에서 변경된 파일들이 컨테이너가 삭제됐을 때 함께 삭제되는 것을 방지하는 방법 중 하나입니다. 아래 예제에서 호스트의 `/Users/ultrasparc/workspace/jenkins` 디렉토리를 컨테이너의 `/var/jenkins_home`에 마운트하여 파일을 저장하거나 활용할 수 있습니다.
- `--name`

``` shell
$ docker run -d -p 8080:8080 -v ~/workspace/jenkins:/var/jenkins_home --name=jenkins cd14cecfdb3a
b17c29bf5ed4f5070229a9b54ff369dc5dfe6a62cdb1a7a0a6f4196692620560
```
컨테이너 ID가 `b17c29bf5ed4`이고 이름이 `jenkins`로 할당된 컨테이너가 실행되었습니다.

``` shell
$ docker container ls -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
b17c29bf5ed4        jenkins/jenkins     "/bin/tini -- /usr/l…"   9 minutes ago       Up 9 minutes        0.0.0.0:8080->8080/tcp, 50000/tcp   jenkins
```

그럼 웹 브라우저에 `http://localhost:8080`을 입력해서 본격적으로 `jenkins` 설치를 진행해 보겠습니다.

# Jenkins 설정
브라우저에서 접속하면 아래와 같이 `Administrator password`를 등록해야 다음 단계로 진행할 수 있습니다. 

<p align="center">
  <img src="/images/2019-02-02/getting-started.png">
</p>

하지만, 호스트 환경에서는  `/var/jenkins_home/secrets/initialAdminPassword` 경로에 파일이 존재하지 않습니다. 왜냐하면, 저 경로는 컨테이너 내부에 생성된 경로이기 때문입니다. 
따라서, `initialAdminPassword` 파일에 접근하기 위해서는 컨테이너 생성할 때 `-v` 옵션으로 볼륨 설정한 `~/workspace/jenkins/secrets/initialAdminPassword` 에서 확인할 수 있습니다.

``` shell
$ cat ~/workspace/jenkins/secrets/initialAdminPassword
# 아래 출력된 값을 복사해서 붙여넣기하면 됩니다.
9afbcbb541124299b586e9921d82b72f 
```

위와 같이 볼륨 설정을 별도로 하지 않은 경우에는 `docker logs` 명령으로도 확인할 수 있습니다.

``` shell
$ docker logs jenkins

Running from: /usr/share/jenkins/jenkins.war
webroot: EnvVars.masterEnvVars.get("JENKINS_HOME")
Jan 29, 2019 11:57:50 PM Main deleteWinstoneTempContents
WARNING: Failed to delete the temporary Winstone file /tmp/winstone/jenkins.war
....
*************************************************************

Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:

9afbcbb541124299b586e9921d82b72f

This may also be found at: /var/jenkins_home/secrets/initialAdminPassword

*************************************************************
....
```

## 플러그인 설치
플러그인 설치를 위한 선택 화면이 노출되면 (잘 모를땐) 권장 플러그인 설치를 선택합니다.

<p align="center">
  <img src="/images/2019-02-02/customize-jenkins.png">
</p>

여러가지 플러그인들을 설치합니다.

<p align="center">
  <img src="/images/2019-02-02/install-plugins.png">
</p>

## First Admin User 생성
플러그인들이 설치가 완료되면, Admin User를 생성해 줍니다.

<p align="center">
  <img src="/images/2019-02-02/create-first-admin-user.png">
</p>

## 설정 완료
이제 모든 설정 과정이 끝났습니다. 

<p align="center">
  <img src="/images/2019-02-02/jenkins-is-ready.png">
</p>

# Jenkins Job 등록

jenkins 설정 과정이 끝나면 새로운 Job을 만들어 보겠습니다. 

<p align="center">
  <img src="/images/2019-02-02/jenkins-new-item.png">
</p>

## 새로운 Item 등록
`새 작업` 항목을 선택하고, `item name`을 입력합니다.
그리고, `Freestyle project`를 선택해서 새로운 job을 생성해 보겠습니다.

<p align="center">
  <img src="/images/2019-02-02/enter-an-item.png">
</p>

## General
General 항목을 아래와 같이 설정합니다.

<p align="center">
  <img src="/images/2019-02-02/general.png">
</p>

## 소스 코드 관리

Jenkins를 통해서 자동으로 실행할 테스트 프로젝트의 소스 코드 저장소 정보를 입력합니다.
> 주의: 위 이미지처럼 저장소 경로를 입력할 때 프로토콜을 `https`로 해야합니다.

<p align="center">
  <img src="/images/2019-02-02/source-code-repository.png">
</p>

소스코드 저장소에 접근하기 위한 자격증명(Credentials)을 지정해야 합니다.
저는 `username`과 `password`로 자격증명을 등록했습니다.

<p align="center">
  <img src="/images/2019-02-02/jenkins-credential.png">
</p>

## 빌드 유발

빌드 유발은 소스 코드 관리에 등록한 소스 저장소의 `master branch`에 코드가 `push` 될 때 하도록 **Github hook trigger for GITScm polling** 을 선택합니다. github 저장소에서 설정이 필요하며 아래에서 정리하겠습니다.

이렇게 빌드가 유발됐을 때 테스트가 실행되로록 `Build > Add Build Step > Execute shell`을 선택하고 `gradle` 명령어를 입력하고 저장합니다.

<p align="center">
  <img src="/images/2019-02-02/run-build.png">
</p>

## Job 등록 완료

위 과정을 모두 마쳤다면 등록된 Job 목록을 확인할 수 있습니다.

<p align="center">
  <img src="/images/2019-02-02/job-complete.png">
</p>

# Github 연동

`소스 코드 관리`에서 설정한 소스 코드 저장소의 `master branch`에 `push`가 발생했을 때, 자동으로 테스트가 실행될 수 있도록 설정합니다.

Github 소스 코드 저장소로 이동합니다. `Settings > Webhooks > Add webhook` 으로 새로운 Webhook을 추가 합니다.

Payload URL에는 `{jenkins url}/github-webhook/` 을 입력합니다.
Content type은 `application/json` 을 선택합니다.

Webhook 설정이 완료되면 `Recent Deliveries`에 `ping` 테스트 결과를 확인할 수 있습니다.

<p align="center">
  <img src="/images/2019-02-02/github-webhook.png">
</p>

설정된 대로 Github Webhook이 잘 동작하는지 확인하기 위해서 테스트로 소스 코드 저장소에 변경된 코드를 `push` 해보겠습니다.

Jenkins에서는 Github push 이벤트로 발생된 Webhook 요청을 받아 테스트를 수행하게 됩니다.

<p align="center">
  <img src="/images/2019-02-02/run-build-job.png">
</p>

# 정리

여기까지 Jenkins를 docker container로 설치하고 빌드가 가능하도록 설정했습니다.
그리고, Github Webhook을 통해 소스 코드 저장소에 변경된 코드가 push 되면 자동으로 빌드가 유발되고 테스트가 실행될 수 있도록 설정하는 과정을 정리했습니다.



