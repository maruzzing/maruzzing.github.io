---
layout: post
title: React Native 환경 설정하기
date: 2020-09-23
comments: true
categories: [Study, rnative]
tags: [React Native, Environment Variable]
excerpt: Dev 레벨에서만 개발하면 상관 없겠지만 앱을 배포하고 관리하기 위해서는 Dev, Staging, Production 등으로 개발 환경을 구분해서 운영하게 된다.
featured-image: images/ev_ios_scheme3.png
---

Dev 레벨에서만 개발하면 상관 없겠지만 앱을 배포하고 관리하기 위해서는 Dev, Staging, Production 등으로 개발 환경을 구분해서 운영하게 된다. React Native에서는 개발 환경을 어떻게 설정할 수 있는지 알아보자.

## 라이브러리 설치

리액트 네이티브에서 .env를 사용하기 위해 [`react-native-config`](https://github.com/luggit/react-native-config)를 설치한다.

```bash
$ npm i react-native-config
cd ios && pod install && cd ..
``` 

### 안드로이드 추가 설정

`android/settings.gradle` 파일에 아래 내용을 추가하고, 

```java
include ':react-native-config'
project(':react-native-config').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-config/android')
``` 

`android/app/build.gradle` 파일의 두번쨰 줄에 아래 내용을 추가해 준다.

```java
apply from: project(':react-native-config').projectDir.getPath() + "/dotenv.gradle"
``` 

## .env 파일 작성

이제 `.env`, `.env.staging`, `.env.production` 파일을 작성해 준다. `.env` 파일들은 `.gitignore`에 추가해 주는 것도 잊지 말자.

**.env**

```
BACKEND_URL=http://localhost:3000
``` 

**.env.staging**

```
BACKEND_URL= // backend url for staging
``` 

**.env.production**

```
BACKEND_URL= // backend url for production
``` 

이제, 환경별로 빌드를 할 때마다 해당하는 .env 파일을 참조할 수 있도록 설정해 주어야 한다.

## ios

- Xcode의 Product > Scheme > Edit Scheme 을 열고 기본 Scheme이 선택된 상태에서 Duplicate Scheme을 클릭한다.

<div style='display: flex; justify-content: center;'>
  <img src="/images/ev_ios_scheme.png" alt="ev_ios_scheme" width="600em">
</div>

- 복사된 Scheme의 이름을 [앱이름].staging 으로 변경한다.

- Product > Scheme > Edit Scheme 을 열고 [앱이름].staging을 선택한 뒤 Build > Pre-actions에서 New Run Script Action을 아래와 같이 추가해 준다.

```
echo ".env.staging" > /tmp/envfile
```

<div style='display: flex; justify-content: center;'>
  <img src="/images/ev_ios_scheme2.png" alt="ev_ios_scheme2" width="600em">
</div>

- 추가적으로 각 작업별로 Build Configuration을 추가해 줄 수 있는데, 기본적으로 debug, release를 선택할 수 있으며, staging 등 다른 환경의 configuration을 따로 설정해 준 경우 여기서 선택해 줄 수 있다.

<div style='display: flex; justify-content: center;'>
  <img src="/images/ev_ios_scheme3.png" alt="ev_ios_scheme3" width="600em">
</div>

- production 등 다른 환경도 동일하게 설정해 주고, 원하는 환경의 scheme을 선택하여 빌드하면 된다.

<div style='display: flex; justify-content: center;'>
  <img src="/images/ev_ios_scheme4.png" alt="ev_ios_scheme4" width="600em">
</div>

## android

- `app/guild.gradle` 파일에 아래 내용을 추가해 준다.

```java
project.ext.envConfigFiles = [
        debug: ".env",
        releasestaging: ".env.staging",
        release: ".env.production"
]
apply from: project(':react-native-config').projectDir.getPath() + "/dotenv.gradle"
```

- `app/guild.gradle` 파일의 `buildTypes` 부분에 아래 내용을 추가해 준다.

```java
buildTypes {
...
    releaseStaging {
        initWith release
        matchingFallbacks = ['release']
    }
}
```

- 앱을 다시 빌드하면 Build Variants에 debug, release와 더불어 releaseStaging이 추가된 것을 확인할 수 있다. staging의 apk 빌드는 `./gradlew assembleReleaseStaging` 커맨드로 진행한다.