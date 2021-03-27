---
layout: post
title: axios interceptors로 토큰 리프레시 하기
date: 2021-03-05
comments: true
categories: [Study, rnative]
tags: [React Native, axios, authentication]
excerpt: 사용자 인증은 많은 서비스에서 필수적인 부분이다. 사용자 인증에는 크게 세션/쿠키 인증과 토큰 기반 인증이 있는데, 대표적인 토큰 기반의 인증으로 JWT(Json Web Token)을 많이 사용한다.
featured-image:
---

사용자 인증은 많은 서비스에서 필수적인 부분이다. 사용자 인증에는 크게 세션/쿠키 인증과 토큰 기반 인증이 있는데, 대표적인 토큰 기반의 인증으로 **JWT(Json Web Token)**을 많이 사용한다.

JWT 토큰 인증 방식은 제 3자에게 토큰이 탈취되는 경우 보안에 취약하다는 단점이 있어 토큰에 유효기간을 부여하여 보안을 강화하게 되는데, 유효기간이 짧으면 사용자는 계속 로그인을 다시 해야하기 때문에 불편해 진다. 여기서 사용할 수 있는 것이 **refresh token 을 활용한 토큰 리프레시**다.

![JWT Auth Flow](/images/jwt_auth_flow.png "JWT Auth Flow")

AccessToken 보다 유효기간이 긴 RefreshToken이라는 인증 장치를 하나 더 사용해 AccessToken을 재발급 해주는 것이다. RefreshToken 마저 만료되는 경우, 유저는 다시 로그인을 진행하게 된다.

요청에 대한 응답이 401 Error인 경우, refreshToken을 가지고 Token Refresh api 호출하는 방식으로 구현할 수 있으며, **[axios interceptors](https://github.com/axios/axios#interceptors)**를 이용해 구현해 보겠다.

### axios interceptors

**[axios interceptors](https://github.com/axios/axios#interceptors)**는 `then`이나 `catch`로 처리되기 전에 요청(request)나 응답(response)을 가로채 어떠한 작업을 수행할 수 있게 한다.

```javascript
// 요청(request) interceptor
axios.interceptors.request.use(
  function (config) {
    // 요청을 보내기 전 수행할 작업
    return config;
  },
  function (error) {
    // 오류 요청 가공
    return Promise.reject(error);
  }
);

// 응답(response) interceptor
axios.interceptors.response.use(
  function (response) {
    // 200대 response를 받아 응답 데이터를 가공하는 작업
    return response;
  },
  function (error) {
    // 200대 이외의 오류 응답을 가공
    return Promise.reject(error);
  }
);
```

### error response에 대한 interceptor 작성

요청에 대한 응답이 401 Error인 경우, refreshToken을 가지고 Token Refresh api 호출해야 하므로 error response에 대한 interceptor를 작성한다.

```javascript
axios.interceptors.response.use(
  (response) => {
    return response;
  },
  async (error) => {
    const {
      config,
      response: { status },
    } = error;
    if (status === 401) {
      if (error.response.data.message === "TokenExpiredError") {
        const originalRequest = config;
        const refreshToken = await AsyncStorage.getItem("refreshToken");
        // token refresh 요청
        const { data } = await axios.post(
          `http://localhost:3000/refresh/token`, // token refresh api
          {
            refreshToken,
          }
        );
        // 새로운 토큰 저장
        const {
          accessToken: newAccessToken,
          refreshToken: newRefreshToken,
        } = data;
        await AsyncStorage.multiSet([
          ["accessToken", newAccessToken],
          ["refreshToken", newRefreshToken],
        ]);
        axios.defaults.headers.common.Authorization = `Bearer ${newAccessToken}`;
        originalRequest.headers.Authorization = `Bearer ${newAccessToken}`;
        // 401로 요청 실패했던 요청 새로운 accessToken으로 재요청
        return axios(originalRequest);
      }
    }
    return Promise.reject(error);
  }
);
```

### 다중 요청 대응 코드 추가

여기까지 작성하면 토큰 만료 시, 토큰을 재발급 빧아 요청을 다시 보내는 것 까지 해결 되었다. 하지만 한 가지 문제가 있는데, 앱을 구동하다 보면 여러 요청이 동시에 혹은 짧은 시간 간격으로 진행되는데, 위의 코드로는 한 요청으로 토큰이 재발급 되는 동안, 다른 요청도 동시에 토큰이 재발급을 요청하게 되기 때문에 토큰이 여러번 재발급되어 이 전에 발급한 토큰은 유효하지 않게 되는 오류가 발생한다.

이를 해결하기 위해 **토큰이 재발급 되는 동안 다른 요청들은 서버에 보내지 않고 모아 두었다가 새로운 토큰 발급이 완료된 후에 한꺼번에 요청하는 코드를 추가**해 주어야 한다.

```javascript
let isTokenRefreshing = false;
let refreshSubscribers = [];

const onTokenRefreshed = (accessToken) => {
  refreshSubscribers.map((callback) => callback(accessToken));
};

const addRefreshSubscriber = (callback) => {
  refreshSubscribers.push(callback);
};

axios.interceptors.response.use(
  (response) => {
    return response;
  },
  async (error) => {
    const {
      config,
      response: { status },
    } = error;
    const originalRequest = config;
    if (status === 401) {
      if (!isTokenRefreshing) {
        // isTokenRefreshing이 false인 경우에만 token refresh 요청
        isTokenRefreshing = true;
        const refreshToken = await AsyncStorage.getItem("refreshToken");
        const { data } = await axios.post(
          `http://localhost:3000/refresh/token`, // token refresh api
          {
            refreshToken,
          }
        );
        // 새로운 토큰 저장
        const {
          accessToken: newAccessToken,
          refreshToken: newRefreshToken,
        } = data;
        await AsyncStorage.multiSet([
          ["accessToken", newAccessToken],
          ["refreshToken", newRefreshToken],
        ]);
        isTokenRefreshing = false;
        axios.defaults.headers.common.Authorization = `Bearer ${newAccessToken}`;
        // 새로운 토큰으로 지연되었던 요청 진행
        onTokenRefreshed(newAccessToken);
      }
      // token이 재발급 되는 동안의 요청은 refreshSubscribers에 저장
      const retryOriginalRequest = new Promise((resolve) => {
        addRefreshSubscriber((accessToken) => {
          originalRequest.headers.Authorization = "Bearer " + accessToken;
          resolve(axios(originalRequest));
        });
      });
      return retryOriginalRequest;
    }
    return Promise.reject(error);
  }
);
```
