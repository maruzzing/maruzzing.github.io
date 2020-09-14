---
layout: post
title: React Native 로컬 푸시알림 구현
date: 2019-09-08
comments: true
categories: [Study, rnative]
tags: [React Native, Push Notification]
excerpt: 현재 진행중인 React Native 앱 프로젝트에 매일 일정한 시간에 푸시알림이 오는 기능이 필요해, React Native Push Notifications 모듈료 로컬 푸시알림을 구현해 보았다.
---

현재 진행중인 React Native 앱 프로젝트에 매일 일정한 시간에 푸시알림이 오는 기능이 필요해, [React Native Push Notifications](https://github.com/zo0r/react-native-push-notification) 모듈을 사용해 로컬 푸시알림을 구현해 보았다.

### 필요 모듈 설치

먼저, [React Native Push Notifications](https://github.com/zo0r/react-native-push-notification)와 [@react-native-community/push-notification-ios](https://github.com/react-native-community/react-native-push-notification-ios)를 설치한다. 공식문서에 따라 차근차근 따라하면 된다.

### App.js에 AppState eventListener 추가

[`AppState`](https://facebook.github.io/react-native/docs/0.40/appstate#docsNav)는 앱이 'foreground'에 있는지, 'background'에 있는지를 알려준다. `addEventListener`와 `removeEventListener`를 통해 상태 변화를 감지할 수 있다.

```react
import PushNotification from 'react-native-push-notification';
class App extends React.Component {
  constructor(props) {
    super(props);
  }

  componentDidMount = () => {
    AppState.addEventListener('change', this.handleAppStateChange);
  };

  componentWillUnmount = () => {
    AppState.removeEventListener('change', this.handleAppStateChange);
  };

  render() {
    return (
      <View>
        <Text>Hello World!</Text>
      </View>
    );
  }
}
```

### handleAppStateChange 함수 추가

AppState가 `background`가 되면 원하는 시간에 푸시알림이 오도록 설정한다.

```react
  handleAppStateChange = appState => {
      if (appState === 'background') {
        PushNotification.localNotificationSchedule({
            message: "My Notification Message", // (required)
            date: new Date(Date.now() + 60 * 1000) // in 60 secs
        });
      }
  }
```

`message`와 `date` 외에도 [다양한 설정값](https://github.com/zo0r/react-native-push-notification#local-notifications)을 줄 수 있다.
이 때, `repeatType` 값으로 'day'를 추가해 주면 매일 동일한 시간에 푸시알림이 온다. 자세한 내용은 [Repeating Notifications](https://www.npmjs.com/package/react-native-push-notification#repeating-notifications)에서 확인할 수 있다.

### Notification 액션 정의

'PushController.js' 파일에 알림을 클릭했을 때 이후 동작을 정의하고, App.js에 렌더한다.

```react
// PushController.js
import PushNotification from 'react-native-push-notification';

export default class PushController extends React.Component {
  componentDidMount() {
    PushNotification.configure({
      onNotification: function(notification) {
        console.log('NOTIFICATION:', notification);
      }
    });
  }

  render() {
    return null;
  }
}

// App.js
...
  render() {
    return (
      <View>
        <Text>Hello World!</Text>
        <PushController />
      </View>
    );
  }
```
