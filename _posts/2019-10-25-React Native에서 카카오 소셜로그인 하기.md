---
layout: post
title: React Native에서 카카오 소셜로그인 하기
date: 2019-10-25
comments: true
categories: [Study, rnative]
tags: [React Native, Auth, Social Login]
excerpt: 네이버와 카카오톡 소셜로그인은 javascript나, ios, android sdk는 제공하지만 react-native를 위한 sdk는 따로 없기 때문에 native module을 사용하거나, (있다면) 관련 라이브러리를 사용해야 한다.
---

네이버와 카카오톡 소셜로그인은 javascript나, ios, android sdk는 제공하지만 react-native를 위한 sdk는 따로 없기 때문에 [native module](https://facebook.github.io/react-native/docs/native-modules-ios)을 사용하거나, (있다면) 해당 라이브러리를 사용해야 한다. 지난 프로젝트에서는 passport 라이브러리를 사용하여 웹뷰로 소셜로그인을 진행했지만 이번엔 React Native Seoul에서 만든 [react-native-kakao-login](https://github.com/react-native-seoul/react-native-kakao-login) 라이브러리를 사용해 보기로 했다.

## install

먼저, 모듈을 설치하고 추가적으로 ios와 android 설정을 진행한다.

```bash
yarn add @react-native-seoul/kakao-login
```

### kakao API 사용 등록

kakao 에서 제공하는 API를 사용하기 위해서는 [kakao developers](https://developers.kakao.com) 페이지에서 앱 등록을 진행해야 한다. 계정 로그인을 한 후, 앱 만들기 버튼을 통해 앱을 만들면 앱 키를 발급받을 수 있다. 네이티브 SDK를 사용할 예정이기 때문에 네이티브 앱 키가 필요하다.

### ios 설정

먼저, [kakao developers](https://developers.kakao.com) 페이지의 앱에 ios 플랫폼을 추가한다. 이때 번들 ID를 필수로 입력해야 하는데, `.xcworkspace` General 탭에서 확인할 수 있다.

플랫폼 등록을 마쳤으면, (RN >= 0.60 의 경우) pod install을 해주는데, `./ios/Podfile`에 아래 내용을 추가해 준 뒤 pod install을 해주면 된다.

```bash
target 'yourApp' do
...
pod 'KakaoOpenSDK', '~> 1.16.0'
...
end
```

그리고 [카카오 공식문서](https://developers.kakao.com/docs/ios/getting-started#%EA%B0%9C%EB%B0%9C%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EC%84%A4%EC%A0%95)를 참고해서 몇 가지 추가로 설정해 주어야 하는데,

1. 먼저 `info.plist` 파일에 `LSApplicationQueriesSchemes`의 키로 `kakao{네이티브 앱 키}`와 `kakaokompassauth`를 추가한다.

2. 그리고 `info` 탭에서 URL Types를 추가해 주는데, URL Schemes에 `kakao{네이티브 앱 키}`를 등록해 준다.

3. `AppDelegate.m` 파일에 아래 내용을 추가해 준다.

```swift
#import <KakaoOpenSDK/KakaoOpenSDK.h>

- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url
                                      sourceApplication:(NSString *)sourceApplication
                                              annotation:(id)annotation {
    if ([KOSession isKakaoAccountLoginCallback:url]) {
        return [KOSession handleOpenURL:url];
    }

    return false;
}

- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url
                                                options:(NSDictionary<NSString *,id> *)options {
    if ([KOSession isKakaoAccountLoginCallback:url]) {
        return [KOSession handleOpenURL:url];
    }

    return false;
}

- (void)applicationDidBecomeActive:(UIApplication *)application
{
    [KOSession handleDidBecomeActive];
}
```

### android 설정

안드로이드도 , [kakao developers](https://developers.kakao.com) 페이지의 플랫폼을 추가해 주어야 한다. 먼저 패키지 명과 마켓 URL을 입력해야 하는데, 패키지 명을 입력하면 마켓 URL은 자동으로 입력된다. 패키지 명은 `./android/app/src/manifests/AndroidMenifest.xml` 파일의 `manifest` 태그에서 확인할 수 있다.

1. `bundle.gridle` 파일에 아래 내용을 추가해 준다.

```
subprojects {
    repositories {
        mavenCentral()
        maven { url 'http://devrepo.kakao.com:8088/nexus/content/groups/public/' }
    }
}
```

2. `./android/app/src/res/values` 폴더에 `kakao_strings.xml` 파일을 만들고, 아래와 같이 카카오 앱키를 입력한 뒤 저장한다.

```xml
<resources>
    <string name="kakao_app_key">{kakao_app_key}</string>
</resources>
```

3. `./android/app/src/manifests/AndroidMenifest.xml` 파일의 `allowBackup`를 `true`로 수정해 주고, `application` 태그 내부에 아래 내용을 추가해 준다.

```xml
...
    <meta-data
        android:name="com.kakao.sdk.AppKey"
        android:value="@string/kakao_app_key" />
...
```

여기까지 하고 카카오 로그인을 진행하면 `Login Failed:E_AUTHORIZATION_FAILED invalid android_key_hash or ios_bundle_id or web_site_url`라는 에러 메시지를 받게 되는데, 안드로이드는 키해시를 개발자 콘솔에 입력해주어야 정상적으로 동작하기 때문이다.

키해시는 디버그와 릴리즈 용으로 나뉘는데, 먼저 디버그용 키해시를 얻기 위해 `./android/app/src/java/MainApplication.java` 파일의 `onCreate()` 함수 내에 아래 내용을 추가하고 빌드하면 키해시를 확인할 수 있다.

```java
import android.content.pm.PackageInfo;
import android.content.pm.PackageManager;
import android.content.pm.Signature;
import android.util.Base64;
import android.util.Log;

import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;

...
  @Override
  public void onCreate() {
    super.onCreate();
    SoLoader.init(this, /* native exopackage */ false);
    initializeFlipper(this); // Remove this line if you don't want Flipper enabled
      try {
          PackageInfo info = getPackageManager().getPackageInfo(
                  "com.rn_social_login",
                  PackageManager.GET_SIGNATURES);
          for (Signature signature : info.signatures) {
              MessageDigest md = MessageDigest.getInstance("SHA");
              md.update(signature.toByteArray());
              Log.d("KeyHash:", Base64.encodeToString(md.digest(), Base64.DEFAULT));
          }
      } catch (PackageManager.NameNotFoundException e) {

      } catch (NoSuchAlgorithmException e) {
    }
  }
```

![kakao_login_keyhash](/images/kakao_login_keyhash.png "kakao_login_keyhash")

이렇게 얻어진 키해시 값을 kakao developers 어플리케이션 안드로이드 플랫폼에 입력해 주면 정상적으로 작동된다.

## 구현

구현은 간단한데, `KakaoLogins`로 클래스를 호출하고, `login()`, `getProfile()`, `logout()` 등의 API를 사용하면 된다.

```react
import * as React from "react";
import { SafeAreaView, Text, TouchableOpacity } from "react-native";
import AsyncStorage from "@react-native-community/async-storage";
import KakaoLogins from "@react-native-seoul/kakao-login";
import { NavigationScreenProp, NavigationState } from "react-navigation";

if (!KakaoLogins) {
  console.error("KakaoLogins Module is Not Linked");
}

type Navigation = NavigationScreenProp<NavigationState>;

interface Props {
  navigation: Navigation;
}
class SignInScreen extends React.Component<Props> {
  kakaoLogin = async () => {
    try {
      let result = await KakaoLogins.login();
      if (result) {
        await this.getProfile();
        await AsyncStorage.setItem("userToken", result.accessToken);
        this.props.navigation.navigate("App");
        console.log(`Login Finished:${JSON.stringify(result)}`);
      }
    } catch (err) {
      if (err.code === "E_CANCELLED_OPERATION") {
        console.log(`Login Cancelled:${err.message}`);
      } else {
        console.log(`Login Failed:${err.code} ${err.message}`);
      }
    }
  };

  getProfile = async () => {
    try {
      let result = await KakaoLogins.getProfile();
      await console.log(`Get Profile Finished:${JSON.stringify(result)}`);
    } catch (err) {
      console.log(`Get Profile Failed:${err.code} ${err.message}`);
    }
  };
  render() {
    return (
      <SafeAreaView>
        <TouchableOpacity onPress={this.kakaoLogin}>
          <Text>SignIn with Kakao</Text>
        </TouchableOpacity>
      </SafeAreaView>
    );
  }
}

export default SignInScreen;
```

네이버 로그인도 진행을 해야 하는데, `@react-native-seoul/naver-login`에 react native 0.60 이상 버전의 업데이트 내용이 반영이 잘 되어있지 않아 native module을 직접 만들어서 사용해봐야 겠다.
