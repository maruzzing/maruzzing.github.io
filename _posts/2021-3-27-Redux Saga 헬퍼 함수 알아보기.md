---
layout: post
title: Redux Saga 헬퍼 함수 알아보기
date: 2021-03-27
comments: true
categories: [Study, react]
tags: [redux-saga]
excerpt: redux-saga에서는 스토어에 지정된 액션들이 dispatch 될 떄 task를 만들기 위해 내부 함수를 감싸는 몇몇 helper 함수(Effect Creator)를 제공한다. 그 중 액션을 감지하기 위한 목적으로 takeEvery와 takeLatest를 많이 사용 했었는데, 며칠 전 팀원이 takeLeading을 사용 했기에 호기심이 생겨 세 가지 헬퍼 함수의 기능에 대해 알아보기로 했다.
featured-image: images/redux_saga_helper_main.png
---

redux-saga에서는 스토어에 지정된 액션들이 dispatch 될 떄 task를 만들기 위해 내부 함수를 감싸는 몇몇 helper 함수(`Effect Creator`)를 제공한다.

![Redux Saga Effect Creator](/images/redux_saga_helper_main.png "Redux Saga Effect Creator")

그 중 액션을 감지하기 위한 목적으로 `takeEvery`와 `takeLatest`를 많이 사용 했었는데, 며칠 전 팀원이 `takeLeading`을 사용 했기에 호기심이 생겨 세 가지 헬퍼 함수의 기능에 대해 알아보기로 했다.

### [takeEvery](https://redux-saga.js.org/docs/api/#takeeverypattern-saga-args)

`takeEvery`는 `redux-thunk`와 비슷한 기능으로 **액션이 발생할 때 마다 task를 실행**한다.

```javascript
const takeEvery = (patternOrChannel, saga, ...args) =>
  fork(function* () {
    while (true) {
      const action = yield take(patternOrChannel);
      yield fork(saga, ...args.concat(action));
    }
  });
```

이전 작업이 완료되지 않았더라도 액션이 발생하면 새로운 task를 동시에 실행할 수 있으며, 순서가 보장되지 않는다. 따라서 동시에 중복으로 발생해도 문제 없는 상황에서 사용될 수 있다.

`takeLatest`와 `takeLeading`은 `takeEvery`와 달리 **어느 순간에 단 하나의 동일한 task만 실행**되게 한다.

### [takeLatest](https://redux-saga.js.org/docs/api/#takelatestpattern-saga-args)

`takeLatest`는 액션이 발생했을 떄 이전에 동일한 task가 실행되고 있다면 그것을 취소 하고 **최신 task만 실행**되게 한다.

```javascript
const takeLatest = (patternOrChannel, saga, ...args) =>
  fork(function* () {
    let lastTask;
    while (true) {
      const action = yield take(patternOrChannel);
      if (lastTask) {
        yield cancel(lastTask); // cancel is no-op if the task has already terminated
      }
      lastTask = yield fork(saga, ...args.concat(action));
    }
  });
```

task가 취소 되었는지는 finally block안에서 `yield cancelled()`를 사용하여 확인할 수 있으며, 항상 최신의 액션이 처리되기 때문에 **최신의 데이터**를 받아와야 하거나, 액션이 dispatch 될 떄 마다 **파라미터가 변경되어 결과 값이 달라지는 경우** 사용할 수 있다.

액션이 dispatch 될 때마다 이전 요청이 취소된다고 하더라도 이미 서버로 api 요청은 진행되었을 수 있으므로 response 값이 같거나 동기화가 중요하지 않는 api 요청의 경우에는 불필요한 중복 요청이 발생할 수 있으므로 이를 방지하기 위해 `takeLeading`을 사용하는 것이 더욱 적절할 것 같다.

### [takeLeading](https://redux-saga.js.org/docs/api/#takeleadingpattern-saga-args)

`takeLeading`은 한 가지 액션이 발생하고 task가 실행되는 동안에는 동일한 액션이 감지되지 않고 **최초의 요청만 처리**한다.

```javascript
const takeLeading = (patternOrChannel, saga, ...args) =>
  fork(function* () {
    while (true) {
      const action = yield take(patternOrChannel);
      yield call(saga, ...args.concat(action));
    }
  });
```

위에서 언급한 것 처럼 항상 같은 response를 가지는 요청이나, 처리 순서가 중요한 요청에서 효율적으로 사용할 수 있다.
