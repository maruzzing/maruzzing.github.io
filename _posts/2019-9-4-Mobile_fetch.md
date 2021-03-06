---
layout: post
title: Mobile_fetch
date: 2019-09-04
comments: true
categories: [projects]
tags: [ToyProject, Team, Front-End]
excerpt: 해외에서 구할 수 있는 상품을 여행자를 통해 직구할 수 있도록 도와주는 서비스 플랫폼
mainImage: /images/fetch-demo2.png
---

![Fetch Main](/images/fetch-main.png "Fetch Main")

<!-- ![Fetch Demo](/images/fetch-demo.png "Fetch Demo") -->

<div class='innerBoxOutlined'>
<table class='project_detail'>
  <tr class='project_detail row'>
    <td class='project_detail title'>Period</td>
    <td class='project_detail content'>2019년 5월 27일 ~ 6월 19일 (약 3주)</td>
  </tr>
  <tr class='project_detail row'>
    <td class='project_detail title'>Team</td>
    <td class='project_detail content'>프론트엔드 2명, 백엔드 2명 (총 4명)</td>
  </tr>
  <tr class='project_detail row'>
    <td class='project_detail title'>Platform</td>
    <td class='project_detail content'>iOS 모바일 어플리케이션</td>
  </tr>
  <tr class='project_detail row'>
    <td class='project_detail title'>Role</td>
    <td class='project_detail content'>React Native 프론트엔드 개발
    <br>- 효과적인 상태관리를 위해 Redux 사용<br>
- SendBird 활용, 실시간 채팅 구현<br>
- Firebase Cloud Messaging 활용, 푸시알람 구현<br>
- local 및 소셜 회원가입, 로그인 구현<br>
- 디바이스별 레이아웃 최적화<br>
</td>
  </tr>
  <tr class='project_detail row'>
    <td class='project_detail title'>Tech Stack</td>
    <td class='project_detail content'>
    <a href='https://facebook.github.io/react-native/' target='_blank'>
    <img  class='stack_logo' src="/images/stack_logo_rn.png" alt="React Native" /></a>
    <a href='https://redux.js.org/' target='_blank'>
    <img class='stack_logo' src="/images/stack_logo_redux.png" alt="Redux" /></a>
    <a href='https://sendbird.com' target='_blank'>
    <img class='stack_logo' src="/images/stack_logo_sendbird.png" alt="SendBird" /></a>
    <a href='https://firebase.google.com/docs/cloud-messaging' target='_blank'>
    <img class='stack_logo' src="/images/stack_logo_fcm.png" alt="Firebase Cloud Message(FCM)"/></a>
</td>
  </tr>
</table>
</div>

<!-- <div style='display: flex; justify-content: center'>
<img src="/images/fetch-demo-gif3.gif" alt="fetch-demo-gif" width="200em" height='400em' style='border-radius:30px; margin:15px' />
<img src="/images/fetch-demo-gif1.gif" alt="fetch-demo-gif" width="200em" height='400em' style='border-radius:30px; margin:15px' />
<img src="/images/fetch-demo-gif2.gif" alt="fetch-demo-gif" width="200em" height='400em' style='border-radius:30px; margin:15px' />
</div> -->

## Overview

유명 관광지 보다 특색있는 편집숍이나 현지 브랜드를 구경하는 것을 즐기다 보니 해외 여행을 갈 때마다 친구들로 부터 특정 물건을 사다달라는 요청을 받거나, 현지를 구경하다 가족이나 친구가 마음에 들어 할 만한 물건이 있으면 사다줄까? 하고 '구매 대행'을 자처하기도 했었다.<br><br>이러한 경험으로 부터 아이디어를 얻어 탄생한 **fetch**. 해외에서 구할 수 있는 상품을 여행자를 통해 직구할 수 있도록 여행자와 구매자를 매칭 해주는 플랫폼이다.
<br><br>
여행자는 자신의 여행 일정을, 구매자는 구매 요청 물품을 등록하여 조건에 맞는 사람들끼리 자유롭게 거래를 진행할 수 있도록 실시간 채팅 기능과 거래 진행현황을 확인할 수 있는 마이페이지 기능 등을 구현했다.
<br><br>

<span class="movie_btn"><a href="https://youtu.be/39ta7_sM07Y">시연영상 보러가기</a></span><br><br>

## Key Features

### 회원가입 및 로그인

자체 플랫폼 회원가입과 Google 계정을 이용한 회원가입으로 유저를 관리했다. 자체 플랫폼 회원가입 기능에서는 정규식을 사용해서 이메일이나 비밀번호의 유효성을 바로 검사하여 화면에 표시해 주어 사용성을 향상시켰다.<br>

<div class='simulContainer'>
<img class='simulImg' src="/images/fetch-demo-gif4_no_frame.gif" alt="fetch-demo-gif" width="250em" height='265em' style='margin-right:30px;' />
<img class='simulImg' src="/images/fetch-demo-gif5_no_frame.gif" alt="fetch-demo-gif" width="250em"  height='265em' style=' margin-left:30px;' />
</div>

<br>
또한, 서버 단에서 [passport](http://www.passportjs.org/)로 소셜 로그인을 구현하고, Webview와 연결시켰다. 개인 프로젝트에서도 passport로 소셜로그인을 구현하고 있는데, 이 프로젝트에서 잘못 구현한 것을 발견했다. 😨 그래도 모바일에서 웹페이지를 띄울수 있게 해주는 Webview라는 걸 처음 접하면서 이런 저런 시도를 해보면서 신기하기도 하고 많이 배울 수 있었다.<br>
<div class='simulContainer'>
<img class='simulImg' src="/images/fetch-demo-gif6_no_frame.gif" alt="fetch-demo-gif" width="250em" style='border-bottom-right-radius:24px; border-bottom-left-radius:25px;' />
</div>

### 실시간 채팅 및 푸시알람

실시간 채팅 서비스 구현을 위해 socket.io로 구현을 할지, 솔루션을 사용할지 고민을 하다, 한 달이라는 짧은 개발 기간을 고려하여 SendBird라는 채팅 솔루션을 사용했다. SendBird에서 제공하는 채팅 관련 다양한 API를 사용하여 원하는 기능들을 보다 간단하게 구현하면서 전체적인 채팅 기능의 흐름이나을 경험할 수 있었을 뿐만 아니라, 비동기 처리나 Redux 등에 더 집중할 수 있었다.<br>

<div class='simulContainer'>
<img src="/images/fetch-demo-gif7_no_bg.gif" alt="fetch-demo-gif" width="500em" style= 'border-radius: 30px;' />
</div>

<br>
무엇보다 푸시알림을 구현하는데 많은 시간을 소비했다. SendBird가 제공하는 기본적인 실시간 채팅 API는 양쪽 클라이언트가 모두 접속해 있을때 가능하다. 카톡처럼 앱의 다른 페이지에 있거나, 앱이 실행되지 않고 있을 때 푸시알람을 보내기 위해서는 FCM과 같은 Cloud Messaging 서버를 통해 상대방에게 데이터를 전달해야 한다.<br>처음에 SendBird와 FCM이 어떻게 연결되어 있는지 등에 대한 개념이 부족해 많이 어렵게 느껴진 것 같고, 또한, 시뮬레이터에는 푸시알림을 지원하지 않는다는걸 몰라 한동안 삽질을 했었다. 😓<br>

<div class='simulContainer'>
<img class='simulImg' src="/images/fetch-demo-gif2_no_frame.gif" alt="fetch-demo-gif" width="250em" style='border-bottom-right-radius:24px; border-bottom-left-radius:25px;' />
</div>

### 마이페이지

마이페이지는 전체 앱에서의 다양한 페이지들을 자유롭게 이동해야하기 때문에 Navigation에 대한 고민이 많았던 페이지였다. 또한, 리스트 형태의 구성이 많다 보니 컴포넌트 mapping을 통해 간단하게 구현할 수 있어 리액트의 장점을 한껏 느껴본 부분이었다.

<div class='simulContainer'>
<img class='simulImg' src="/images/fetch-demo-gif8_no_frame.gif" alt="fetch-demo-gif" width="250em" style='border-bottom-right-radius:27px; border-bottom-left-radius:27px;' />
</div>

<br>

## Review

React만 다뤄본 상태에서 React Native와 Redux를 처음 시도하다 보니 여러가지 시행착오가 많았던 것 같다. 기능은 작동하지만 구조적으로 아쉬운 부분도 적지 않고 잘못 구현한 부분도 있었다. 코드 구조나, 클린코드에 대한 아쉬움은 개발을 하면서 계속 느끼고 고쳐나가야 할 숙명이겠지..<br>
매일 스마트폰을 쓰면서 손쉽게 접할 수 있는 기능들을 구현하기가 쉽지 않았던 것도 적잖은 충격..😂 소셜 로그인을 위한 WebView 사용이나, Push Notification에 많은 시간을 허비한 것 같지만, 그만큼 성장해 나가는 과정이라고 생각한다.

<br>
**다음 프로젝트 때 시도해 보고 싶은 것들**

- Redux-Saga 도입
- [Athentication flows](https://reactnavigation.org/docs/en/auth-flow.html) 재구현
- 소셜로그인 재구현
- 푸시알람 재구현
- Socket.io로 실시간 통신 구현
- android 앱 개발

<br>
