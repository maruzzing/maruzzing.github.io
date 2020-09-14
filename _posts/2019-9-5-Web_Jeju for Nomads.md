---
layout: post
title: Web_Jeju for Nomads
date: 2019-09-05
comments: true
categories: [projects]
tags: [ToyProject, Solo, Full-Stack]
excerpt: 디지털 노마드를 위한 제주도 카페 정보 공유 웹페이지
mainImage: /images/jeju-demo.png
---

![Jeju for Nomads Main](/images/jeju_for_nomad_theme.png "Jeju for Nomads Main")

<div class='innerBoxOutlined'>
<table class='project_detail'>
  <tr class='project_detail row'>
    <td class='project_detail title'>Period</td>
    <td class='project_detail content'>2019년 6월 30일 ~ 7월 12일<br>8월 26일 ~ 9월 4일 (수정 및 배포)</td>
  </tr>
  <tr class='project_detail row'>
    <td class='project_detail title'>Platform</td>
    <td class='project_detail content'>web</td>
  </tr>
  <tr class='project_detail row'>
    <td class='project_detail title'>Role</td>
    <td class='project_detail content'> React와 Node.js 사용, 풀스택 개발
    <br>- React 및 Redux 사용<br>
- 상태관리를 위한 Redux (Redux-saga) 적용<br>
- Google Maps API 활용, 구글맵 기반의 정보 제공<br>
- Express.js 프레임워크 활용 서버 구축<br>
- 사진 및 정보 게시 및 수정을 위한 CRUD 구현<br>
- Multer, MulterS3를 활용하여 이미지 업로드 구현<br>
- 관계형 DB, MySQL 사용 DB 구축<br>
- AWS CodePipeline을 활용한 배포자동화 구축<br>
</td>
  </tr>
  <tr class='project_detail row'>
    <td class='project_detail title'>Tech Stack</td>
    <td class='project_detail content'>
    <a href='https://facebook.github.io/react/' target='_blank'>
    <img  class='stack_logo' src="/images/stack_logo_rn.png" alt="React" /></a>
    <a href='https://redux.js.org/' target='_blank'>
    <img class='stack_logo' src="/images/stack_logo_redux.png" alt="Redux" /></a>
    <a href='https://www.styled-components.com/' target='_blank'>
    <img class='stack_logo' src="/images/stack_logo_styledComp.png" alt="StyledComponent" /></a>
    <a href='https://nodejs.org/en/' target='_blank'>
    <img class='stack_logo' src="/images/stack_logo_node.png" alt="nodeJs" /></a>
    <a href='https://expressjs.com/' target='_blank'>
    <img class='stack_logo' src="/images/stack_logo_express.png" alt="ExpressJs"/></a><br>
    <a href='https://www.mysql.com/' target='_blank'>
    <img class='stack_logo' src="/images/stack_logo_mysql.png" alt="MySQL"/></a>
    <a href='https://aws.amazon.com/' target='_blank'>
    <img class='stack_logo' src="/images/stack_logo_aws.png" alt="AWS"/></a>
</td>
  </tr>
</table>
</div><br>

## Overview

평소에도 노트북 들고 카페에서 자주 작업을 하는데 제주도 한달 살기를 준비하며 '제주도 일하기 좋은 카페' 라고 검색창에 검색해 봐도 인스타 감성의 카페들만 나오고 노트북을 사용할 수 있는 환경을 갖춘 카페 정보는 많이 나오지 않았다. 제주도까지 가서 스타벅스만 전전할 순 없는 법. 제주도를 찾을 디지털 노마드들을 위한 카페 정보를 공유할 수 있는 웹사이트를 만들기로 했다.

카페에 대한 기본 정보는 구글맵 API에서 제공받고, 추가적으로 와이파이, 콘센트, 주차, 화장실, 그리고 따뜻한 아메리카노 한 잔의 가격에 대한 정보와 직접 찍은 사진을 게시했다.

<br>

이 프로젝트는 제주를 시작으로 다른 나라, 다른 도시의 시리즈도 추가될 것이다.

coming soon 😎<br><br>

## Key Features

### 구글맵 기반의 정보 제공

[Google Maps Javascript API](https://developers.google.com/maps/documentation/javascript/tutorial)를 사용하여 구글지도를 기반으로 data를 보여주는 방식을 택했다.

카페 콘텐츠 등록을 할 때, 구글 지도 검색을 통해 'placeId'와 '좌표'를 DB에 저장했다. 이 때 저장한 '좌표' 정보로 정보 리스트를 지도 위의 위치 아이콘으로 렌더하고, 특정 좌표의 아이콘을 선택하면 'palceId'로 운영시간, 주소, 리뷰 등 추가적인 정보를 불러오는 방식으로 구현했다.

<img class='simulImg' src="/images/jeju-demo-gif2.gif" alt="jeju-demo-gif"  /><br>

또한, 프리뷰에서 모든 정보를 불러오는 것이 아니라, 'see more'를 클릭했을 때 상세 이미지와 코멘트 등 추가 정보를 서버에 요청해 불필요한 API 요청을 줄였다.

<img class='simulImg' src="/images/jeju-demo-gif1.gif" alt="jeju-demo-gif"  />

### admin 페이지 게시판 CRUD 구현

일반적인 게시판 기능처럼 admin 페이지에서는 정보를 추가하고 수정하고 삭제할 수 있도록 CRUD를 구현했다.

이 때, 이미지 업로드는 multer와 multer-s3 모듈을 사용하여 s3에 이미지를 저장하고, db에는 저장된 이미지 url만 저장하는 방식으로 구현했다.

<img class='simulImg' src="/images/jeju-demo-gif4.gif" alt="jeju-demo-gif"  />

<img class='simulImg' src="/images/jeju-demo-gif3.gif" alt="jeju-demo-gif"  />

### AWS CodePipeline을 활용한 배포자동화 구축

<div style='display: flex; justify-content: center;'>
<img  src="/images/code_pipeline.png" alt="Code Pipeline" width='85%' /></div>
<br>

이번 프로젝트에서는 AWS를 좀 더 적극적으로 활용해 보았다.

서버와 DB는 AWS EC2에, 클라이언트는 S3에 배포했는데,<br>
추가적으로 서버는 AWS CodePipeline을 도입하여 깃헙의 마스터 브랜치에 코드를 푸시하거나 머지하는 이벤트가 발생하면 자동적으로 EC2에 배포될 수 있도록 했다.
<br>

클라이언트의 경우, terminal에서 CLI 커맨드 `yarn build && yarn deploy` 만으로 자동적으로 빌드된 파일이 배포될 수 있도록 했다.

## Review

이번 프로젝트는 1차적으로 개발을 하고 한 달이 지나서야 배포를 위해 코드를 다시 열어보게 되었다. 하지만 보면 볼수록 코드에 마음에 들지 않는 부분이 마음에 걸려 리팩토링을 진행하게 되었다. 이 과정에서 속상하기도 하고, 그만큼 많이 배울 수 있었던 것 같다.

또한, 배포를 처음부터 혼자 차근히 진행하고 '배포 자동화'를 시도해 본 과정이 너무 재미있었다. 하면 할 수록 새롭고 신기한 코딩의 세계다!

버전업을 하게 된다면 콘센트가 있는 곳, 주차장이 있는 곳 등으로 필터링을 할 수 있게 하고싶다.

<br>
**다음 프로젝트 때 시도해 보고 싶은 것들**

- CI 도입
- 필터링

<br>

## 관련 포스트

**Front-end**

- [Styled Components 적용하기](/study/react/Styled-Components-적용하기/)
- [리액트에서 구글맵 연동하기](/study/react/리액트에서-구글맵-연동하기/)
- [리액트로 이미지 업로더 구현하기](/study/react/리액트로-이미지-업로더-구현하기/)
- [이미지 업로드 전 미리보기](/study/react/이미지-업로드-전-미리보기/)
- [구글맵 infowindow에 컴포넌트 띄우기](/study/react/구글맵-infowindow에-컴포넌트-띄우기/)
- [FileReader를 활용하여 file을 DataUrl로 읽기](/study/react/FileReader를-활용하여-file을-DataUrl로-읽기/)

**Back-end**

- [multer 사용하여 이미지 업로드 구현하기](/study/nodejs/multer-사용하여-이미지-업로드-구현하기/)
- [multer-s3를 이용하여 AWS S3 이미지 업로드하기](/study/nodejs/multer-s3를-이용하여-AWS-S3-이미지-업로드하기/)

**DevOps**

- [EC2(아마존 리눅스)에 서버와 DB(mysql) 배포하기[1]](</study/etc/EC2(아마존-리눅스)에-서버와-DB(mysql)-배포하기-1/>)
- [EC2(아마존 리눅스)에 서버와 DB(mysql) 배포하기[2]](</study/etc/EC2(아마존-리눅스)에-서버와-DB(mysql)-배포하기-2/>)
- [EC2(아마존 리눅스)에 서버와 DB(mysql) 배포하기[3]](</study/etc/EC2(아마존-리눅스)에-서버와-DB(mysql)-배포하기-3/>)
- [AWS CodeDeploy 사용하여 배포 자동화 하기](/study/etc/AWS-CodeDeploy-사용하여-배포-자동화-하기/)
- [CLI 커맨드로 리액트 앱 AWS S3에 배포하기](/study/etc/CLI-커맨드로-리액트-앱-AWS-S3에-배포하기/)
