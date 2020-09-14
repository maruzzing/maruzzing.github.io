---
layout: post
title: webView 내부에 Lottie로 애니 구현하기
date: 2019-09-25
comments: true
categories: [Study, rnative]
tags: [React Native, mail]
excerpt: 이전 포스트에서 다루었듯, react native에서 소셜 로그인을 웹뷰를 사용해 구현했다. 기능 구현을 하고나니 마음에 안드는 점을 발견하게 되었는데, 소셜 어카운트로 로그인이 되고 서버에서 회원가입/로그인 작업이 되기 까지 시간이 걸리는데 그 동안 웹뷰엔 빈 화면만 표시되는 것이다.
---

[이전 포스트](/study/rnative/Passport와-JWT를-이용하여-Auth-구현하기-3/)에서 다루었듯, react native에서 소셜 로그인을 웹뷰를 사용해 구현했다. 기능 구현을 하고나니 마음에 안드는 점을 발견하게 되었는데, 소셜 어카운트로 로그인이 되고 서버에서 회원가입/로그인 작업이 되기 까지 시간이 걸리는데 그 동안 웹뷰엔 빈 화면만 표시되는 것이다.

<div class='simulContainer'>
<img src="/images/react-native-webview-lottie.gif" alt="react-native-webview-lottie" width="250em" style= 'border-bottom-right-radius: 25px; border-bottom-left-radius: 25px;' />
</div>

그래서 통신 시간 동안 lottie로 애니메이션을 구현해 보기로 했다.

웹뷰 내에 삽입하는 거라 [웹에서 로티를 구현하는 방법](https://josephkhan.me/lottie-web/)을 참고해서 `injectedJavaScript`에 스크립트 코드도 넣어보고 이런저런 난리를 피워봤지만 실패하고 [React Native WebView 공식문서](https://github.com/react-native-community/react-native-webview/blob/master/docs/Reference.md#injectedjavascript)를 찬찬히 읽어보고 다른 방향으로 접근해 본 결과 그냥 간단하게 해결할 수 있었다. 허허.. 😅

React Native WebView 에는 `onLoad`, `onLoadStart` 등과 함께 `onLoadProgress`라는 props가 있는데, `nativeEvent`의 `progress`, 즉 진척도를 알 수 있다. 먼저, `nativeEvent.progress`를 콘솔에 찍어보면, 웹뷰 내 콘텐츠 로딩이 시작되고 마무리 될 때까지 0.1에서 1까지 진척도가 순차적으로 찍히는 것을 확인할 수 있었다.

```react
      <WebView
        ref={ref => (this.webview = ref)}
        onLoadProgress={({ nativeEvent }) => {
          console.log(nativeEvent.progress)
        }}
        originWhitelist={['*']}
        source={props.source}
        style={styles.webviewStyle}
        userAgent={userAgent}
        useWebKit={true}
        javaScriptEnabled={true}
        injectedJavaScript={INJECTED_JAVASCRIPT}
        onMessage={_handleMessage}
      />
```

이를 활용하여 진척도가 0.1일 때 LottieView 컴포넌트를 로딩하고, 1일 때 언로딩하는 코드를 짜 보았다. 함수형 컴포넌트에서 state 관리가 필요해 react hooks도 도입해 보았다.

```react
const SocialWebview = props => {
...
  const handleProgress = nativeEvent => {
    if (nativeEvent.progress === 0.1) {
      setVisible(true);
    } else if (nativeEvent.progress === 1) {
      setVisible(false);
    }
  };
...
  return (
    <SafeAreaView style={styles.container}>
      <WebView
        ref={ref => (this.webview = ref)}
        onLoadProgress={({ nativeEvent }) => {
          handleProgress(nativeEvent);
        }}
        originWhitelist={['*']}
        source={props.source}
        style={styles.webviewStyle}
        userAgent={userAgent}
        useWebKit={true}
        javaScriptEnabled={true}
        injectedJavaScript={INJECTED_JAVASCRIPT}
        onMessage={_handleMessage}
      />
      {visible && (
        <View style={styles.lottieContiner}>
          <LottieView
            source={require('../../assets/animation/ani_splash_turtle.json')}
            style={styles.lottieView}
            autoPlay
            loop
          />
        </View>
      )}
    </SafeAreaView>
  );
};
```

그 결과 아래와 같이 잘 구현 되었다. 뿌듯!

<div class='simulContainer'>
<img src="/images/react-native-webview-lottie2.gif" alt="react-native-webview-lottie2" width="250em" style= 'border-bottom-right-radius: 25px; border-bottom-left-radius: 25px;' />
</div>
