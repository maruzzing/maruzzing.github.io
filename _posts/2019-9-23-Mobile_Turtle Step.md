---
layout: post
title: Mobile_Turtle Step
date: 2019-09-23
comments: true
categories: [projects]
tags: [ToyProject, Solo, Full-Stack]
excerpt: 시각화된 목표 달성률을 볼 수 있는 투두 앱
mainImage: /images/turtle-step-main.png
---

![Turtle Step Title](/images/turtle_step_title.png "Turtle Step Title")

<div class='innerBoxOutlined'>
<table class='project_detail'>
  <tr class='project_detail row'>
    <td class='project_detail title'>Period</td>
    <td class='project_detail content'>2019년 7월 20일 ~ 진행중</td>
  </tr>
  <tr class='project_detail row'>
    <td class='project_detail title'>Platform</td>
    <td class='project_detail content'>iOS 모바일 어플리케이션</td>
  </tr>
  <tr class='project_detail row'>
    <td class='project_detail title'>Role</td>
    <td class='project_detail content'> React Native와 Node.js 사용, 풀스택 개발
    <br>- 상태관리를 위한 Redux (Redux-saga) 적용<br>
- 매일 특정 시간 푸시알림 구현<br>
- 주간 캘린더 구현<br>
- svg, victory-native 활용, 비주얼 차트 구현<br>
- Lottie 활용, 애니메이션 렌더링<br>
- Express.js 프레임워크 활용 서버 구축<br>
- passport 모듈 활용, local 및 소셜 로그인 구현<br>
- nodemailer 모듈 활용, 임시 비밀번호 메일 발송 구현<br>
- python-shell 모듈 활용, python 코드 연동<br>
- 관계형 DB, MySQL 사용 DB 구축<br>
- HTTPS 통신환경 구축<br>
- 제플린을 이용한 디자이너와 협업<br>
</td>
  </tr>
  <tr class='project_detail row'>
    <td class='project_detail title'>Tech Stack</td>
    <td class='project_detail content'>
    <a href='https://facebook.github.io/react/' target='_blank'>
    <img  class='stack_logo' src="/images/stack_logo_rn.png" alt="React" /></a>
    <a href='https://redux.js.org/' target='_blank'>
    <img class='stack_logo' src="/images/stack_logo_redux.png" alt="Redux" /></a>
    <a href='https://nodejs.org/en/' target='_blank'>
    <img class='stack_logo' src="/images/stack_logo_node.png" alt="nodeJs" /></a>
    <a href='https://expressjs.com/' target='_blank'>
    <img class='stack_logo' src="/images/stack_logo_express.png" alt="ExpressJs"/></a>
    <a href='https://www.python.org/' target='_blank'>
    <img class='stack_logo' src="/images/stack_logo_python.png" alt="python"/></a>
    <a href='http://www.passportjs.org/' target='_blank'>
    <img class='stack_logo' src="/images/stack_logo_passport.png" alt="passport"/></a><br>
    <a href='https://www.mysql.com/' target='_blank'>
    <img class='stack_logo' src="/images/stack_logo_mysql.png" alt="MySQL"/></a>
    <a href='https://aws.amazon.com/' target='_blank'>
    <img class='stack_logo' src="/images/stack_logo_aws.png" alt="AWS"/></a>
</td>
  </tr>
</table>
</div><br>

## Overview

직장 생활을 할 때 매일 아침 할일 목록을 작성하는 것으로 하루를 시작했다. 하지만 할 일을 나열하는 것 보다 더 중요한 것은 하루를 마무리 하면서 그래서 오늘 하루는 어떻게 보냈는지 회고하는 시간이 아닐까?
<br>
이런 생각에서 시작한 Turtle Step.
<br>
매일 아침 특정 시간에 알람이 울리면, 하루 동안 할 일을 등록한다. 주기적인 일은 반복 패턴을 설정해 놓으면 자동으로 입력되어 진다. 할 일을 끝낼 때 마다 완료 체크를 하면 오늘의 진척률을 그래프로 확인할 수 있다. 하루 일과를 마치고 알림이 울리면 오늘 하루를 돌아보며 회고를 작성한다. 작성되어진 회고는 일별 기록으로 다시 볼 수 있고, 주간 및 월간 통계자료도 시각화된 자료로 확인 할 수 있다.

<br>

## Key Features

### Todo List 추가

카테고리 별로 Todo List를 추가하고, 상세 페이지에서 반복 패턴을 선택할 수 있도록 했다. Todo List는 하루 일정을 마무리 하기 전 까진 서버에 저장되지 않고 AsyncStorage에서 저장/관리 되어 불필요한 서버 트래픽을 줄였다. 또한 일정이 마무리 되면 반복 패턴이 있는 일정 외에는 AsyncStorage에서 삭제하여 메모리를 유연하게 관리했다.

<div class='simulContainer'>
<img src="/images/turtle-demo-gif1.gif" alt="turtle-demo-gif1" width="250em" style= 'border-bottom-right-radius: 25px; border-bottom-left-radius: 25px; margin-right:35px;' />
<img src="/images/turtle-demo-gif2.gif" alt="turtle-demo-gif2" width="250em" style= 'border-bottom-right-radius: 28px; border-bottom-left-radius: 28px; margin-left:35px;' />
</div>

### 주간 캘린더 구현

주 단위의 캘린더를 라이브러리를 쓰지 않고 원하는 기능만 넣어 구현했다. 일요일을 시작으로 한 주간 리뷰가 있는 날은 따로 마킹하였고, 지난 일정을 확인 하다가도 '오늘'로 바로 돌아올 수 있는 버튼을 만들어 편리하게 사용할 수 있게 하였다.

<div class='simulContainer'>
<img class='simulImg' src="/images/turtle-demo-gif3.gif" alt="turtle-demo-gif3" width="250em"   />
</div>

### 달성률 통계자료 시각화

일별, 기간별 목표 달성률 통계 자료를 차트로 시각화 하였다.
차트 그래프는 [victory-native](https://github.com/FormidableLabs/victory-native) 라이브러리를 활용하기도 하고 [react-native-svg](https://github.com/react-native-community/react-native-svg)를 이용해 직접 그리기도 했다.

간단한 통계이지만 연습 차원에서 서버 쪽에서는 python 코드를 연동해 보기도 했다.

<div class='simulContainer'>
<img src="/images/turtle-demo-gif6.gif" alt="turtle-demo-gif6" width="250em" style= 'border-bottom-right-radius: 26px; border-bottom-left-radius: 26px; margin-right:35px;' />
<img src="/images/turtle-demo-gif7.gif" alt="turtle-demo-gif7" width="250em" style= 'border-bottom-right-radius: 26px; border-bottom-left-radius: 26px; margin-left:35px;' />
</div>

### 로컬 푸시알림 구현

매일 일정시간에 푸시알림을 설정하여 잊지 않고 투두 리스트와 회고를 작성할 수 있게 하였다.

<div class='simulContainer'>
<img src="/images/turtle-demo-gif4.gif" alt="turtle-demo-gif4" width="250em" style= 'border-bottom-right-radius: 25px; border-bottom-left-radius: 25px; margin-right:35px;' />
<img src="/images/turtle-demo-gif5.gif" alt="turtle-demo-gif5" width="250em" style= 'border-bottom-right-radius: 28px; border-bottom-left-radius: 28px; margin-left:35px;' />
</div>

### 소셜 로그인 및 임시 비밀번호 메일 발송 구현

요즘 많은 앱서비스들이 비밀번호 찾기, 가입 인증 등을 이메일 전송을 활용하여 진행하기 때문에 이번 프로젝트에서는 [nodemailer](https://nodemailer.com/about/) 모듈을 활용하여 이메일로 임시 비밀번호를 발송하는 걸 시도해 보았다. 임시 비밀번호는 [uuid](https://www.npmjs.com/package/uuid)로 랜덤한 문자열과 숫자 조합을 만들었다.

그리고 [passport](http://www.passportjs.org) 모듈을 활용하여 구글, 페이스북, 카카오톡, 네이버의 소셜 로그인을 구현했다. 이 과정에서 페이스북 소셜 로그인은 https 통신 환경이 필요하기 때문에 AWS Certificate Manager를 활용해 https 통신 환경도 구축해 보았다.

<div class='simulContainer'>
<img src="/images/turtle-demo-gif8.gif" alt="turtle-demo-gif8" width="250em" style= 'border-bottom-right-radius: 28px; border-bottom-left-radius: 28px;' />
</div>

### Lottie를 활용한 애니메이션 렌더링

모바일이나 웹페이지에 애니메이션을 구현하기 위해서 gif를 사용해 왔는데, gif는 용량이 무겁고, 해상도 대응이 어렵다는 단점이 있기 때문에, 요즘 gif의 훌륭한 대체재로 [Lottie](https://github.com/react-native-community/lottie-react-native)라는 라이브러리가 주목받고 있다고 하여 이번 프로젝트에서 사용해 보았다.

<div class='simulContainer'>
<img src="/images/turtle-demo-gif9.gif" alt="turtle-demo-gif9" width="250em" />
</div>

## Review

테스트 플라이트로 테스트를 해보고 있는데 아직 부족한 부분이 많은 것 같아 실제로 출시 하기까진 시간이 조금 더 걸릴 것 같다. 하지만 이번 프로젝트를 하면서 제플린으로 디자이너와 협업도 해보고, 다양한 시도를 많이 해봐서 배운점이 많았다. react native를 사용했음에도 iso 개발에만 집중해 android 쪽은 소홀했던 것이 가장 아쉽지만 계속 다듬어 가야 겠다.

<br>

## 관련 포스트

**Front-end**

- [리액트 네이티브에 폰트 설정하기](/study/rnative/리액트-네이티브에-폰트-설정하기/)
- [리액트 네이티브 팝업 만들기](/study/rnative/리액트-네이티브-팝업-만들기/)
- [Passport와 JWT를 이용하여 Auth 구현하기[3]](/study/rnative/Passport와-JWT를-이용하여-Auth-구현하기-3/)
- [React Native에서 Lottie 사용하기](/study/rnative/React-Native에서-Lottie-사용하기/)
- [React Native에서 Redux-Saga 적용하기](/study/rnative/React-Native에서-Redux-Saga-적용하기/)
- [NavigationService로 스크린 전환하기](/study/rnative/NavigationService로-스크린-전환하기/)
- [React Navigation 커스텀 헤더의 back button](/study/rnative/React-Navigation-커스텀-헤더의-back-button/)
- [React Native 로컬 푸시알림 구현](/study/rnative/React-Native-로컬-푸시알림-구현/)
- [React Native 커스텀 Tab Bar 만들기](/study/rnative/React-Native-커스텀-Tab-Bar-만들기/)
- [React Native 주간 달력 구현하기](/study/rnative/React-Native-주간-달력-구현하기/)
- [주차 구하기](/study/javascript/주차-구하기/)
- [KeyboardAvoidingView 적용하기](/study/rnative/KeyboardAvoidingView-적용하기/)
- [메일발송기능 적용하기](/study/rnative/메일발송기능-적용하기/)

**Back-end**

- [Passport와 JWT를 이용하여 Auth 구현하기[1]](/study/nodejs/Passport와-JWT를-이용하여-Auth-구현하기-1/)
- [Passport와 JWT를 이용하여 Auth 구현하기[2]](/study/nodejs/Passport와-JWT를-이용하여-Auth-구현하기-2/)
- [Passport와 JWT를 이용하여 Auth 구현하기[4]](/study/nodejs/Passport와-JWT를-이용하여-Auth-구현하기-4/)
- [sequelize associate와 include 사용하기](/study/nodejs/sequelize-associate와-include-사용하기/)
- [node.js에서 python 사용하기(feat. python-shell)](/study/nodejs/node.js에서-python-사용하기/)
- [node.js로 메일보내기(feat. nodemailer)](/study/nodejs/node.js로-메일보내기/)

**DevOps**

- [AWS RDS 한글 깨짐 해결방법](/study/etc/AWS-RDS-한글-깨짐-해결방법/)
- [Amazone Linux의 TimeZone 변경하기](/study/etc/Amazone-Linux의-TimeZone-변경하기/)
- [AWS SSL/TLS 등록하기](/study/etc/AWS-SSL-TLS-등록하기/)
