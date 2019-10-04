---
layout: post
title: React Native X TypeScript 시작하기
date: 2019-10-03
comments: true
categories: [Study, rnative]
tags: [React Native, TypeScript]
excerpt: TypeScript 공부를 시작했으니 이번엔 React Native에 TypeScript를 적용해보자. 먼저 프로젝트 만들기!
---

TypeScript 공부를 시작했으니 이번엔 React Native에 TypeScript를 적용해보자. 먼저 프로젝트 만들기! [공식문서](https://facebook.github.io/react-native/blog/2018/05/07/using-typescript-with-react-native)에 너무 잘 나와있어 따라하면 무리없이 시작할 수 있다.

### react native 프로젝트 시작하기

먼저, `react-native init`을 사용해 react native 프로젝트를 생성한다.

```bash
react-native init MyAwesomeProject
cd MyAwesomeProject
```

### typescript 관련 dependencies 설치

이제 typescript를 적용하는데 필요한 dependencies를 설치한다.

```bash
yarn add --dev typescript
yarn add --dev react-native-typescript-transformer
yarn tsc --init --pretty --jsx react-native
touch rn-cli.config.js
yarn add --dev @types/react @types/react-native
```

바로 위에서 `touch`로 생성한 `rn-cli.config.js` 파일에 다음 내용을 추가한다.

```javascript
module.exports = {
  getTransformModulePath() {
    return require.resolve("react-native-typescript-transformer");
  },
  getSourceExts() {
    return ["ts", "tsx"];
  }
};
```

그리고 `tsconfig.json` 파일에 컴파일 옵션들이 정의되어 있는데, es5로 설정되어 있다. 만약 es6 문법을 쓸 예정이라면, 아래 내용을 바꿔주면 된다.

```javascript
...
    "target": "es6" // 'es5"
...
```

### 파일명 바꾸기

App.js를 App.tsx로 변경하고, 앞으로 컴포넌트 파일들은 `.tsx` 확장자로 생성한다.
주의 할 점은 index.js 파일은 변경하지 않고 그대로 두어야 한다.

끄읕!
