---
layout: post
title: React Native에서 쿠키 사용하기
date: 2020-04-06
comments: true
categories: [Study, rnative]
tags: [React Native, Authentication, Cookie]
excerpt: 리액트 네이티브에서 쿠키🍪로 인증을 기능을 구현하는 방법을 알아보기로 했다. ios와 android에서 웹처럼 자동으로 쿠키를 저장, 관리하는 방법이 있지만,,,, 네이티브 코드를 만지기엔🤢,,,, React Native에서 asyncStorage를 이용하여 수동으로 저장, 관리할 수 있는 방법으로 구현해 보았다. 
---

리액트 네이티브에서 쿠키🍪로 인증을 기능을 구현하는 방법을 알아보기로 했다. ios와 android에서 웹처럼 자동으로 쿠키를 저장, 관리하는 방법이 있지만,,,, 네이티브 코드를 만지기엔🤢,,,, React Native에서 asyncStorage를 이용하여 수동으로 저장, 관리할 수 있는 방법으로 구현해 보았다. (feat. axios)

먼저, axiosInstance를 생성하여 `withCredentials: true`로 설정한다.

```javascript
const axiosInstance = axios.create({
  baseURL: baseUrl,
  withCredentials: true,
});
```

쿠키는 response.headers의 `'set-cookie'`값에서 확인할 수 있으므로 아래와 같이 서버 응답값으로 부터 쿠키를 추출한다.
여기서 쿠키값을 asyncStorage에 저장하는 이유는 앱을 재시작 했을 때도 인증을 유지하기 위함이다. 

```javascript
export const setCookie = cookie => {
  axiosInstance.defaults.headers.Cookies = JSON.parse(cookie);
};

export const storeData = async (key, value) => {
  try {
    await AsyncStorage.setItem(key, value);
  } catch (e) {
    console.log(e);
  }
};

export const signIn = async data => {
  try {
    const response = await axiosInstance.post('sign/in', data);
    const [cookie] = response.headers['set-cookie'];
    setCookie(JSON.stringify(cookie));
    await storeData('cookie', JSON.stringify(cookie));
    return cookie;
  } catch (e) {
    console.log(e);
    return null;
  }
};
```

따라서, 앱 시작 시 인증 절차를 아래와 같이 진행할 수 있다.

```javascript
export default function AuthLoadingScreen(props){
  useEffect(()=>{
    _bootstrapAsync = async () => {
      const cookie = await getData('cookie');
      if (cookie) {
        setCookie(cookie);
      }
      props.navigation.navigate(cookie ? 'App' : 'Auth');
    };
    _bootstrapAsync()
  }, [])

  return (
    <SafeAreaView style={styles.container}>
      <ActivityIndicator />
    </SafeAreaView>
  );
}
```