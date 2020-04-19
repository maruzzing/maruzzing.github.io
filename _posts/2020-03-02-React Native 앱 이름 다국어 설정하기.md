---
layout: post
title: React Native 앱 이름 다국어 설정하기
date: 2020-03-02
comments: true
categories: [Study, rnative]
tags: [React Native, Localize]
excerpt: 모바일 기기의 언어 설정에 따라 앱 아이콘 아래에 표시되는 앱 이름도 다국어로 설정할 수 있는데 안드로이드와 ios에서 이를 설정하는 방법을 알아보자. 
---

모바일 기기의 언어 설정에 따라 앱 아이콘 아래에 표시되는 앱 이름도 다국어로 설정할 수 있는데 안드로이드와 ios에서 이를 설정하는 방법을 알아보자. 

## ios

1.xcode > File > New > File에서 [InfoPlist.strings] 이름의 String File을 프로젝트 내 Base.lprj 폴더 내에 만든다.

![Localize Appname ios](/images/localize_appname_ios_1.png "Localize Appname ios")

![Localize Appname ios](/images/localize_appname_ios_2.png "Localize Appname ios")


2.InfoPlist.strings(base)에 디폴트로 보여졌으면 하는 앱 이름을 입력하고, 타겟의 Display Name도 수정한다.
```
"CFBundleDisplayName" = "Turtle Step";
"CFBundleName" = "Turtle Step";
```

![Localize Appname ios](/images/localize_appname_ios_6.png "Localize Appname ios")


3.프로젝트 단 Info 탭 Localization에 원하는 언어를 추가한다.

![Localize Appname ios](/images/localize_appname_ios_3.png "Localize Appname ios")

![Localize Appname ios](/images/localize_appname_ios_4.png "Localize Appname ios")


4.3번에서 추가한 언어의 InfoPlist.strings 파일이 자동으로 생성되는데, 아래와 같이 언어별 Localization 파일을 설정한다.

![Localize Appname ios](/images/localize_appname_ios_5.png "Localize Appname ios")

👉 디바이스 언어를 변경하면 잘 적용되었는지 확인할 수 있다.

![Localize Appname ios](/images/localize_appname_ios_7.png "Localize Appname ios")



## android

1../android/app/src/main/res/values/strings.xml 파일에 기본으로 사용할 앱 이름을 입력한다.

```xml
// /values/strings.xml
<resources>
    <string name="app_name">Turtle Step</string>
</resources>
```

2../android/app/src/main/res/ 폴더 내에 [values-ko] 이름의 폴더를 만들고 strings.xml 파일을 생성한 후 한국어 환경에서 사용할 이름 입력하는데, 추가 언어는 [언어 코드](http://www.loc.gov/standards/iso639-2/php/code_list.php)에 따라 생성한다.

```xml
// /values-ko/strings.xml

<resources>
    <string name="app_name">터틀스텝</string>
</resources>
```

👉 디바이스 언어를 변경하면 잘 적용되었는지 확인할 수 있다.

![Localize Appname aos](/images/localize_appname_aos_1.png "Localize Appname aos")
