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

<img class='simulImg' src="/images/jeju-demo-gif2.gif" alt="jeju-demo-gif"  />

<img class='simulImg' src="/images/jeju-demo-gif1.gif" alt="jeju-demo-gif"  />

### admin 페이지 게시판 CRUD 구현

<img class='simulImg' src="/images/jeju-demo-gif4.gif" alt="jeju-demo-gif"  />

<img class='simulImg' src="/images/jeju-demo-gif3.gif" alt="jeju-demo-gif"  />

### AWS CodePipeline을 활용한 배포자동화 구축

<div style='display: flex; justify-content: center;'>
<img  src="/images/code_pipeline.png" alt="Code Pipeline" width='85%' /></div>
<br>

## Review

<br>
**다음 프로젝트 때 시도해 보고 싶은 것들**

- Redux-Saga 도입
- [Athentication flows](https://reactnavigation.org/docs/en/auth-flow.html) 재구현
- 소셜로그인 재구현
- 푸시알람 재구현
- Socket.io로 실시간 통신 구현
- android 앱 개발

<br>
