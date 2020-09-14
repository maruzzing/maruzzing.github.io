---
layout: post
title: RN Dark & Light Theme 적용하기
date: 2020-05-08
comments: true
categories: [Study, rnative]
tags: [React Native, Styled-Components, Redux-Toolkit]
excerpt: iOS 13버전 부터 기본 배경색을 검은색으로 사용하는 다크모드 기능이 추가 되면서 다크모드를 지원하는 앱이 많아졌다. 그래서 Styled-Components와 Redux-Toolkit을 사용해 다크모드를 지원하는 방법을 알아보았다. 
---

iOS 13버전 부터 기본 배경색을 검은색으로 사용하는 다크모드 기능이 추가 되면서 다크모드를 지원하는 앱이 많아졌다. 그래서 Styled-Components와 Redux-Toolkit을 사용해 다크모드를 지원하는 방법을 알아보았다. 

# Styled-Components 설정하기
### Styled-Components 설치
```bash
$ yarn add styled-components
$ yarn add -D @types/styled-components
$ cd ios && pod install && cd ..
```

### Theme 정의

먼저, 타입스크립트를 쓰고 있기 때문에 `/src/styles/styled.d.ts` 파일에 Default Theme의 Type을 정의한다. 
```javascript
import 'styled-components/native';

declare module 'styled-components/native' {
  export interface DefaultTheme {
    color: {
      main: string;
      white: string;
      bg: string;
    };
  }
}
```

이제 `/src/styles/theme.ts` 파일에 `dark`와 `light` theme의 값을 정의한다.
```javascript
import {DefaultTheme} from 'styled-components/native';

const dark: DefaultTheme = {
  color: {
    main: '#19dda3',
    white: '#ffffff',
    bg: '#121212',
  },
};

const light: DefaultTheme = {
  ...dark,
  color: {
    main: '#00f2ab',
    white: '#ffffff',
    bg: '#ffffff',
  },
};

export {dark, light};
```

# Redux-Toolkit 설정하기

모바일의 컬러 모드를 상태값으로 관리하여 변화에 따라 앱 전역에 해당 theme이 적용되도록 하기 위해 redux로 상태값을 관리하려고 한다.
이전에 redux를 쓰면서 코드량이 많아져 고통스러웠는데(심지어 타입스크립트와 함께 쓰니 그 고통이 두배!😤),
이번에 리덕스 툴킷을 사용해 보니 정말 간편하고 효율적이어서👍 간단한 예제이지만 여기서도 redux-toolkit을 사용하기로 했다. 

### Redux-Toolkit 설치
```bash
$ yarn add @reduxjs/toolkit react-redux @types/react-redux redux-thunk 
$ cd ios && pod install && cd ..
```

### store 생성
`src/reducers/index.ts`에 store를 만들어 준다. 
redux-toolkit에서는 이전과는 완전히 다른 폴더 구조를 제안하고 있지만, 아직 어떤 것이 좋을지 고민하고 있는 단계라
이 예제 에서는 reducers 폴더에서 리덕스와 관련된 모든 내용을 관리할 예정이다.  

```javascript
import { Action, configureStore, ThunkAction } from '@reduxjs/toolkit';

export const store = configureStore({
  reducer: {},
});

export type RootState = ReturnType<typeof store.getState>;
export type AppThunk = ThunkAction<void, RootState, unknown, Action<string>>;
```

### themeSlice 생성
모바일의 컬러 모드에 따라 앱의 theme state를 적용하는 action과 reducer를 `createSlice`를 이용하여 아래와 같이 만든다.
react-native의 [Appearance API](https://reactnative.dev/docs/appearance) 사용하면 모바일의 컬러 모드를 확인할 수 있다.

```javascript
import {createSlice, PayloadAction} from '@reduxjs/toolkit';
import {Appearance, ColorSchemeName} from 'react-native';

const initialState: ColorSchemeName = Appearance.getColorScheme() || 'light';

const themeSlice = createSlice({
  name: 'theme',
  initialState,
  reducers: {
    setTheme: (state, action: PayloadAction<ColorSchemeName>) =>
      action.payload || 'light',
  },
});

const {actions, reducer} = themeSlice;
export const {setTheme} = actions;
export default reducer;
```

`/src/reducers/index.ts` 에 `themeReducer` 적용하면 redux 관련 설정은 완료! 
```javascript
...
import themeReducer from './themeSlice';

export const store = configureStore({
  reducer: {
    theme: themeReducer,
  },
});
...
```


# 앱 전역에 Theme 상태값 적용하기

먼저 index.js에서 redux `Provider`를 이용해 전체 프로젝트에 store를 적용해 준다.
```javascript
import React from 'react';
import {AppRegistry} from 'react-native';
import {Provider} from 'react-redux';
import App from './src/App';
import {name as appName} from './app.json';
import {store} from './src/reducers';

console.disableYellowBox = true;

const AppConatiner = () => (
  <Provider store={store}>
    <App />
  </Provider>
);

AppRegistry.registerComponent(appName, () => AppConatiner);
```

그리고 `/src/App.tsx` 파일에서 Styled-Components의 `ThemeProvider`를 이용하여 앱 전역에 theme을 적용해 준다.
```javascript
import React from 'react';
import {Appearance} from 'react-native';
import styled, {ThemeProvider} from 'styled-components/native';
import {RootState} from './reducers';
import {dark, light} from './styles/theme';
import {setTheme} from './reducers/themeSlice';

const Container = styled.SafeAreaView`
  flex: 1;
  justify-content: center;
  align-items: center;
  background-color: ${(props) => props.theme.color.bg};
`;

function App() {
  const theme = useSelector((state: RootState) => state.theme);

  return (
    <ThemeProvider theme={theme === 'dark' ? dark : light}>
      <Container />
    </ThemeProvider>
  );
}


export default App;
```

추가적으로 `Appearance.addChangeListener`를 사용하여 모바일의 컬러 모드가 변경되면 앱의 `theme` 상태도 변경될 수 있도록 한다.
```javascript
function App({theme}: Props) {
...
	const dispatch = useDispatch();
	  useEffect(() => {
	    const subscription = Appearance.addChangeListener(({colorScheme}) => {
	      dispatch(setTheme(colorScheme));
	    });
	    return () => subscription.remove();
	  }, [dispatch]);
...
}
```

