---
layout: post
title: React Native 커스텀 Tab Bar 만들기
date: 2019-09-09
comments: true
categories: [Study, rnative]
tags: [React Native, React Navigation, TabBar]
excerpt: 현재 진행중인 프로젝트에서 상단 탭바 구현이 필요한데, React Navigation에서 제공하는 것 만으로는 디자인적으로 구현이 어려웠다. 그래서 커스텀 Tab Bar 를 적용하는 방법에 대해 알아보았다.
---

현재 진행중인 프로젝트에서 상단 탭바 구현이 필요한데, React Navigation에서 제공하는 것 만으로는 디자인적으로 구현이 어려웠다. 그래서 커스텀 Tab Bar 를 적용하는 방법에 대해 알아보았다.

간단하게 요약하자면, `createMaterialTopTabNavigator` 의 TabNavigatorConfig 옵션으로 `tabBarComponent`를 커스텀 컴포넌트로 설정해 주면 된다.

### Navigation.js 정의

먼저 navigation.js 파일에서 TopTabNavigator의 RouteConfigs를 정의해 주고, TabNavigatorConfig로 `tabBarComponent: TopTabBar` 속성을 정의해 준다.

```react
const TopTabNavigator = createMaterialTopTabNavigator(
  {
    route1: {
      screen: Route1Screen,
      params: {
        title: 'Tab1'
      }
    },
    route2: {
      screen: Route2Screen,
      params: {
        title: 'Tab2'
      }
    }
  },
  {
    tabBarComponent: TopTabBar
  }
);
```

### TopTabBar 컴포넌트 정의

이제, TopTabBar 컴포넌트를 정의해 주어야 한다. `navigation`을 `props`로 전달받는데, `navigation.state.routes`에 navigation.js에서 정의해 주었던 route가 array 형태로 전달되어진다. 이 routes를 커스텀 Tab 컴포넌트로 매핑해 주면 된다.
그냥 렌더하면 탭바가 statusbar 영역을 침범하게 되는데, `react-native-status-bar-height` 모듈을 사용하면 statusbar의 높이를 알 수 있으므로 이 부분을 style로 보완해 준다.

```react
import { getStatusBarHeight } from 'react-native-status-bar-height';
import Tab from './Tab';

const TopTabBar = props => {
  const { navigation } = props;
  const routes = navigation.state.routes;
  const containerStyle = {
    marginTop: getStatusBarHeight(),
    height: 36,
    backgroundColor: 'white',
    flexDirection: 'row',
    justifyContent: 'center',
    alignItems: 'center'
  };
  return (
    <View style={containerStyle}>
      {routes.map((route, index) => {
        return (
          <Tab
            key={route.key}
            title={route.params.title}
            onPress={() => navigation.navigate(route.routeName)}
            focused={navigation.state.index === index}
            index={index}
          />
        );
      })}
    </View>
  );
};
export default TopTabBar;
```

### Tab 컴포넌트 정의

TabBar를 구성하는 Tab 컴포넌트를 정의해 준다.

```react
const Tab = ({ focused, title, onPress }) => {
  const textStyle = {
    fontSize: 16,
    color: focused ? '#535356' : '#999999',
  };
  const bottomBarStyle = {
    height: 4,
    borderRadius: 4,
    backgroundColor: 'blue'
  };
  return (
    <TouchableOpacity onPress={onPress}>
      <Text style={textStyle}>{title}</Text>
      {focused ? <View style={bottomBarStyle} /> : null}
    </TouchableOpacity>
  );
};

export default Tab;
```
