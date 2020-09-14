---
layout: post
title: KeyboardAvoidingView 적용하기
date: 2019-09-24
comments: true
categories: [Study, rnative]
tags: [React Native, Keyboard]
excerpt: 처음 리액트 네이티브로 개발을 하고 디바이스로 테스트 할 때 가장 당황했던 것이 바로 키보드에 관한 것이었다. 시뮬레이터로 뷰를 보면서 개발할 때 하드웨어 키보드를 쓰다 보니 실제 모바일 기기에서 키보드가 올라왔을 때의 상황을 인지하지 못한 것이다.
---

처음 리액트 네이티브로 개발을 하고 디바이스로 테스트 할 때 가장 당황했던 것이 바로 키보드에 관한 것이었다. 시뮬레이터로 뷰를 보면서 개발할 때 하드웨어 키보드를 쓰다 보니 실제 모바일 기기에서 키보드가 올라왔을 때의 상황을 인지하지 못한 것이다. 그렇게 아무런 조치도 해주지 않으면 아래와 같이 키보드가 뷰를 가려버린다. 🤔

<div class='simulContainer'>
<img src="/images/turtle-demo-gif10.gif" alt="turtle-demo-gif10" width="250em" style= 'border-bottom-right-radius: 25px; border-bottom-left-radius: 25px;' />
</div>

이 문제를 간단히 해결할 수 있는 방법으로 리액트 네이티브에서 제공하는 [KeyboardAvoidingView](https://facebook.github.io/react-native/docs/keyboardavoidingview#docsNav) API를 적용하는 것이다.
`View`로 감쌌던 컴포넌트를 `KeyboardAvoidingView`로 감싸주고, 몇 가지 props를 넘겨주면 끝!

```react
import {KeyboardAvoidingView} from 'react-native';

<KeyboardAvoidingView style={styles.container} behavior="padding" enabled>
  ...
</KeyboardAvoidingView>;
```

하지만 `ScrollView` 내에선 작동하지 않는 단점이 있다. 이 때 사용할 수 있는 것이 [react native keyboard aware scroll view](https://github.com/APSL/react-native-keyboard-aware-scroll-view)이다. 이 또한, `ScrollView`로 감쌌던 컴포넌트를 `KeyboardAwareScrollView`로 감싸주고, 몇 가지 props를 넘겨주면 되는데, 포커스 된 `TextInput`에 까지 스크롤 하기 위해 아래 코드를 추가해 주면 된다.

```react
import ReactNative from 'react-native';
import { KeyboardAwareScrollView } from 'react-native-keyboard-aware-scroll-view'

...

_scrollToInput = (reactNode) => {
  this.scroll.props.scrollToFocusedInput(reactNode)
}

...

render(){
  return(
    <KeyboardAwareScrollView
      innerRef={ref => {
        this.scroll = ref
      }}>
      <View>
        <TextInput
          onFocus={(event) => {
            this._scrollToInput(ReactNative.findNodeHandle(event.target))
          }}
        />
      </View>
    </KeyboardAwareScrollView>
  )
}
```

이렇게 하면 아래와 같이 포커스 된 `TextInput`까지 자동으로 스크롤 되어 키보드가 `TextInput`을 가리지 않게 된다.

<div class='simulContainer'>
<img src="/images/turtle-demo-gif11.gif" alt="turtle-demo-gif11" width="250em" style= 'border-bottom-right-radius: 25px; border-bottom-left-radius: 25px;' />
</div>
