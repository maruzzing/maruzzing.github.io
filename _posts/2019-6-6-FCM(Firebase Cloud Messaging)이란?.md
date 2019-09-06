---
layout: post
title: FCM(Firebase Cloud Messaging)이란?
date: 2019-06-06
comments: true
categories: [Study, etc]
tags: [FCM, Cloud Messaging]
excerpt: 현재 진행하고 있는 프로젝트에서 Sendbird라는 솔루션을 사용해 실시간 채팅 기능을 구현하고 있다. 대부분의 실시간 메시징 기능은 SendBird SDK로 구현이 가능하지만, 푸시알람 기능은 FCM(Firebase Cloud Messaging)을 통해서 제공되기 때문에 FCM이 뭔지 알아보았다.
---

현재 진행하고 있는 프로젝트에서 [Sendbird](https://sendbird.com/)라는 솔루션을 사용해 실시간 채팅 기능을 구현하고 있다. 대부분의 실시간 메시징 기능은 SendBird SDK로 구현이 가능하지만, 푸시알람 기능은 **FCM(Firebase Cloud Messaging)**을 통해서 제공되기 때문에 FCM이 뭔지 알아보았다.

## FCM(Firebase Cloud Message) 동작원리

### 앱을 위한 키를 FCM 서버를 통해 얻는 단계

- 스마트폰에 앱이 설치되는 순간 시스템에서 이 앱을 위해 Firebase 서버에 키 획득을 위한 요청을 보냄
- Firebase 서버에서 키를 만들어 스마트폰에 전달
- 앱에 전달된 키를 서버에 전송(실시간 데이터 푸시 기능이 필요한 곳은 서버이며, 서버에서 전달된 키를 이용하여 데이터를 전송함)
- 서버는 전달된 키를 db에 저장하여 영속화 함

<div style='display: flex; justify-content: center'>
    <img src="/images/fcm1.jpeg" alt="fcm" width="350em">
</div>

### 서버에서 데이터를 스마트폰에 전달하는 절차

앱과 서버가 소켓으로 연결되어 있다면 직접 네트워크 프로그램을 통해 데이터를 앱에 전달하면 됨. 하지만 FCM 같은 푸시 서비스는 소켓 연결 프로그램을 이용하는 방식이 아니고, Firebase 서버를 이용하여 데이터가 앱에 전달되게 하는 방식임. 즉, 앱과 서버가 소켓으로 연결되지 않아도 서버의 데이터를 앱에 보낼 수 있게 됨.

- 서버에서 데이터를 스마트폰에 전달하기 위해 DB에서 키를 획득. 이 키는 스마트폰에 설치된 앱을 식별할 수 있는 유일성이 확보된 값
- DB의 키와 실제 앱에 전송하고자 하는 데이터를 Firebase 서버에 전달. Firebase 서버와는 HTTP 통신이 이용되며 Firebase 서버에서 원하는 방식대로 데이터를 구성하여 요청이 이루어짐.
- Firebase 서버에서는 전달받은 키값을 식별해 어떤 스마트폰의 어떤 앱인지를 식별하여 특정 스마트폰의 앱을 실행하여 데이터를 전달.

<div style='display: flex; justify-content: center'>
  <img src="/images/fcm2.jpeg" alt="fcm" width="350em">
</div>
