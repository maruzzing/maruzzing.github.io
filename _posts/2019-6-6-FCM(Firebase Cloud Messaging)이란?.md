---
layout: post
title: FCM(Firebase Cloud Messaging)이란?
date: 2019-06-06
comments: true
categories: [Study, etc]
tags: [FCM, Cloud Messaging, SendBird]
excerpt: 현재 진행하고 있는 프로젝트에서 Sendbird라는 솔루션을 사용해 실시간 채팅 기능을 구현하고 있다. 대부분의 실시간 메시징 기능은 SendBird SDK로 구현이 가능하지만, 푸시알람 기능은 FCM(Firebase Cloud Messaging)을 통해서 제공되기 때문에 FCM이 뭔지 알아보았다.
---

현재 진행하고 있는 프로젝트에서 [Sendbird](https://sendbird.com/)라는 솔루션을 사용해 실시간 채팅 기능을 구현하고 있다. 대부분의 실시간 메시징 기능은 SendBird SDK로 구현이 가능하지만, 푸시알람 기능은 **FCM(Firebase Cloud Messaging)**을 통해서 제공된다.

즉, socket.io나 SendBird 솔루션에서 실시간으로 메시지를 주고 받는 것은 양쪽 클라이언트가 모두 접속해 있을때 가능하다. 카톡처럼 앱의 다른 페이지에 있거나, 앱이 실행되지 않고 있을 때 푸시알람을 보내기 위해서는 FCM과 같은 Cloud Messaging 서버를 통해 상대방에게 데이터를 전달해야 한다. 그렇다면 FCM(Firebase Cloud Messaging)은 무엇이고 동작원리는 무엇인지 알아보자.

## FCM(Firebase Cloud Message) 이란

[FCM](https://firebase.google.com/docs/cloud-messaging)은 타겟 모바일에 푸시를 보낼 수 있도록 해주는 서비스로, Firebase 콘솔 이나 서버에서 푸시를 보낼 수 있다.

## FCM(Firebase Cloud Message) 동작원리

### 앱을 위한 키를 FCM 서버를 통해 얻는 단계

- 모바일에 앱이 설치되는 순간 Firebase 서버에 키 획득을 위한 요청을 보냄
- Firebase 서버에서 키를 만들어 모바일에 전달
- 모바일 앱에 전달된 키를 서버에 전송
- 서버는 전달받은 키를 db에 저장하여 타겟 모바일의 identification으로 사용

<div style='display: flex; justify-content: center'>
    <img src="/images/fcm1.jpeg" alt="fcm" width="350em">
</div>

### 서버에서 데이터를 스마트폰에 전달하는 절차

- 서버에서 데이터를 타겟 모바일에 전달하기 위해 DB에서 키를 획득
- DB의 키와 전송하고자 하는 데이터를 HTTP 통신으로 Firebase 서버에 전달
- Firebase 서버에서는 전달받은 키값을 식별해 어떤 모바일의 어떤 앱인지를 식별
- 식별된 모바일의 식별된 앱을 실행하여 데이터 전달 (타겟 모바일의 앱은 백그라운드에 있는 상태에서도 리스너로 이벤트를 감지함)

<div style='display: flex; justify-content: center'>
  <img src="/images/fcm2.jpeg" alt="fcm" width="350em">
</div>

---

SendBird의 경우, 앱이 실행될 때 online임을, 앱이 꺼질 때 offline임을 SendBird 서버에 알려줌으로써 특정 기기가 offline일 때 FCM 서버를 통해 푸시알림으로 메시지를 전송할 수 있다.
