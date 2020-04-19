---
layout: post
title: React Native 파일 시스템을 사용해 이미지 캐싱하기
date: 2020-03-01
comments: true
categories: [Study, rnative]
tags: [React Native, Cache, Image]
excerpt: 자주 사용하는 정적 이미지의 경우 접속 시 매번 이미지 서버에서 이미지를 가져오기 보다 이미지를 캐싱해서 사용하면 성능면에서 효율적인 경우가 있다. 그래서 파일 시스템을 이용해서 이미지를 캐싱하는 방법에 대해 알아 보았다. 📂
---

자주 사용하는 정적 이미지의 경우 접속 시 매번 이미지 서버에서 이미지를 가져오기 보다 이미지를 캐싱해서 사용하면 성능면에서 효율적인 경우가 있다. 그래서 파일 시스템을 이용해서 이미지를 캐싱하는 방법에 대해 알아 보았다. 📂 리액트 네이티브에서 파일을 생성하고 읽는 것을 간단하게 할 수 있도록 도와주는 라이브러리들이 있는데 그 중 [react-native-fs](https://github.com/itinance/react-native-fs)를 사용해 보겠다.

### 1. 모듈 설치

[react-native-fs](https://github.com/itinance/react-native-fs) 모듈을 설치한다.

```bash
yarn add react-natve-fs
cd ios && pod install && cd ..
```

react-native-fs 모듈에서 제공하는 `RNFS.CachesDirectoryPath`를 콘솔에 찍어보면 캐시 파일 경로를 확인할 수 있다.

```javascript
...
import RNFS from 'react-native-fs';
...
console.log(RNFS.CachesDirectoryPath);
...
```

### 2. CachedImage 컴포넌트 생성

자주 사용하는 이미지의 경우 `CachedImage`라는 컴포넌트를 만들어 `RNFS.CachesDirectoryPath`(캐시 폴더)에 해당 이미지가 있는지 확인하여, 있으면 캐시 폴더에서 이미지를 꺼내서 렌더하고, 없으면 이미지 서버 스토리지 경로로 부터 다운로드하여 캐시 폴더에 저장한다.

```javascript
import React, {useEffect, useState}  from 'react';
import {Image, View} from 'react-native';
import * as RNFS from 'react-native-fs';

interface Props {
  imageStyle: StyleProp<ImageStyle>;
  imageSource: {id: string; storageUri: string};
}

const CacheImage = ({imageStyle, imageSource}: Props) =>{
  const [source, setSource] = useState<undefined | {uri: string}>(undefined);
  const extension = Platform.OS === 'android' ? 'file://' : '';

  const path = `${extension}${RNFS.CachesDirectoryPath}/${imageSource.id}.jpg`

  const loadFile = (path: string) => {
    setSource({uri: path});
  };

  const downloadFile = (uri: string, path: string) => {
    RNFS.downloadFile({
      fromUrl: uri,
      toFile: path,
    }).promise.then(res => loadFile(path));
  };

  useEffect(() => {
    RNFS.exists(path).then(exists => {
      if (exists) {
        loadFile(path);
      } else {
        downloadFile(imageSource.storageUri, path);
      }
    });
  }, []);

  return <Image style={imageStyle} source={source} />;
}
```
<br>

안드로이드에서는 파일시스템에 접근하기 위해 파일 경로 앞에 `file://`를 붙여야 하며, [`RNFS.exists`](https://github.com/itinance/react-native-fs#existsfilepath-string-promiseboolean)로 파일이 존재하는지 확인하고, [`RNFS.downloadFile`](https://github.com/itinance/react-native-fs#downloadfileoptions-downloadfileoptions--jobid-number-promise-promisedownloadresult-)로 파일을 다운로드 한다.

<br>

이 밖에도 [react-native-fs](https://github.com/itinance/react-native-fs)를 사용하여 다양하게 파일시스템을 활용할 수 있다.