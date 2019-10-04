---
layout: post
title: TypeScript와 React Navigation
date: 2019-10-04
comments: true
categories: [Study, rnative]
tags: [React Native, TypeScript, React Navigation]
excerpt: 리액트 네이티브 프로젝트에 타입스크립트를 적용해 보기로 했는데 처음으로 맞닥뜨린 문제가 React Navigation의 screen props는 어떻게 타입으로 정의하고, 전달할지 였다. 이 문제의 해결법을 가장 기본적인 사용자 인증 플로우로 정리해 보았다.
---

리액트 네이티브 프로젝트에 타입스크립트를 적용해 보기로 했는데 처음으로 맞닥뜨린 문제가 React Navigation의 screen props는 어떻게 타입으로 정의하고, 전달할지 였다. 이 문제의 해결법을 가장 기본적인 사용자 인증 플로우로 정리해 보았다.

## navigation.tsx

먼저, AuthLoadingScreen에서 asyncStorage에 토큰이 있는지 확인하고, 있으면 HomeScreen으로, 없으면 SignInScreen으로 라우팅 해주는 SwitchNavigator를 AppContainer로 작성해 보자.

```typescript
import { createAppContainer, createSwitchNavigator } from "react-navigation";
import SignInScreen from "./components/auth/SignInScreen";
import AuthLoadingScreen from "./components/auth/AuthLoadingScreen";
import HomeScreen from "./components/home/HomeScreen";

const AuthSwitch = createSwitchNavigator(
  {
    AuthLoading: AuthLoadingScreen,
    App: HomeScreen,
    Auth: SignInScreen
  },
  {
    initialRouteName: "AuthLoading"
  }
);
const AppContainer = createAppContainer(AuthSwitch);

export default AppContainer;
```

## AuthLoadingScreen.tsx

App.tsx에서 AppContainer를 렌더링 해주면 AuthLoadingScreen이 화면에 표시될 것이다. 이제 AuthLoadingScreen을 작성해 보려고 하는데, 다양한 방법으로 가능하다.

### [React Navigation Hooks](https://github.com/react-navigation/hooks)와 react의 `useContext` 사용하기

`React.useContext(NavigationContext)`를 사용해서 navigation props를 받아올 수 있다.
콘솔에 찍어보면, 아래와 같이 screen props가 잘 받아져 온 것을 확인할 수 있다.

![navigation](/images/navigation.png "navigation")

이를 **함수형 컴포넌트**로 활용 해 보면,

```typescript
import * as React from "react";
import { SafeAreaView, ActivityIndicator, StyleSheet } from "react-native";
import AsyncStorage from "@react-native-community/async-storage";
import { NavigationContext } from "react-navigation";

const AuthLoadingScreen = () => {
  const navigation = React.useContext(NavigationContext);
  React.useEffect(() => {
    _checkAsyncToken();
  }, []);
  const _checkAsyncToken = async () => {
    const userToken = await AsyncStorage.getItem("userToken");
    navigation.navigate(userToken ? "App" : "Auth");
  };
  return (
    <SafeAreaView style={styles.container}>
      <ActivityIndicator />
    </SafeAreaView>
  );
};

export default AuthLoadingScreen;
```

### NavigationInjectedProps 사용하기

React Navigation의 `NavigationInjectedProps`도 사용할 수 있다.

```typescript
import * as React from "react";
import { SafeAreaView, ActivityIndicator, StyleSheet } from "react-native";
import AsyncStorage from "@react-native-community/async-storage";
import { NavigationInjectedProps, withNavigation } from "react-navigation";

class AuthLoadingScreen extends React.Component<NavigationInjectedProps> {
  componentDidMount = () => {
    this._checkAsyncToken();
  };
  _checkAsyncToken = async () => {
    const userToken = await AsyncStorage.getItem("userToken");
    this.props.navigation.navigate(userToken ? "App" : "Auth");
  };
  render() {
    return (
      <SafeAreaView style={styles.container}>
        <ActivityIndicator />
      </SafeAreaView>
    );
  }
}

export default withNavigation(AuthLoadingScreen);
```

### Params 타입화 하기

혹은 타입화된 Params 사용을 위해 `Navigation` 타입을 정의해서 사용하는 방법도 있다. 이때 React Navigation의 `NavigationScreenProp`과 `NavigationState` 는 타입정의에 쓸 수 있다.

```typescript
import * as React from "react";
import { SafeAreaView, ActivityIndicator, StyleSheet } from "react-native";
import AsyncStorage from "@react-native-community/async-storage";
import { NavigationScreenProp, NavigationState } from "react-navigation";

interface NavigationParams {
  text: string;
}
type Navigation = NavigationScreenProp<NavigationState, NavigationParams>;

interface Props {
  navigation: Navigation;
}
class AuthLoadingScreen extends React.Component<Props> {
  componentDidMount = () => {
    this._checkAsyncToken();
  };
  _checkAsyncToken = async () => {
    const userToken = await AsyncStorage.getItem("userToken");
    this.props.navigation.navigate(userToken ? "App" : "Auth");
  };
  render() {
    return (
      <SafeAreaView style={styles.container}>
        <ActivityIndicator />
      </SafeAreaView>
    );
  }
}

export default AuthLoadingScreen;
```
