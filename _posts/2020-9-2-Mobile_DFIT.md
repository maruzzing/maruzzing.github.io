---
layout: post
title: Mobile_DFIT
date: 2020-09-02
comments: true
categories: [projects]
tags: [Published,ToyProject, Team, Full-Stack]
excerpt: 쉽게 기록 하는 운동 일지 앱
mainImage: /images/dfit-main.png
---

![DFIT Title](/images/dfit-title.png "DFIT Title")

<div class='innerBoxOutlined'>
<table class='project_detail'>
  <tr class='project_detail row'>
    <td class='project_detail title'>Period</td>
    <td class='project_detail content'>2020년 5월 19일 ~ 7월 13일<br>
    (출시 후 지속 업데이트 중)</td>
  </tr>
    <tr class='project_detail row'>
    <td class='project_detail title'>Team</td>
    <td class='project_detail content'>개발자 2명, 기획자 1명, 디자이너 1명</td>
  </tr>
  <tr class='project_detail row'>
    <td class='project_detail title'>Platform</td>
    <td class='project_detail content'>iOS & android 모바일 어플리케이션</td>
  </tr>
  <tr class='project_detail row'>
    <td class='project_detail title'>Role</td>
    <td class='project_detail content'> React Native와 Node.js 사용, 풀스택 개발<br>
    - TypeScript 사용<br>
- 상태관리를 위한 Redux (Redux-toolkit) 적용<br>
- Dark 모드 지원<br>
- 주간 & 월간 캘린더 구현<br>
- Universal Links 활용 비밀번호 찾기 구현<br>
- 환경 별 환경 변수 설정 및 Code-Push 활용 배포<br>
- Express.js 프레임워크 활용 서버 구축<br>
- 관계형 DB, MySQL 사용 DB 구축<br>
- HTTPS 통신환경 구축<br>
- 제플린을 이용한 디자이너와 협업<br>
</td>
  </tr>
  <tr class='project_detail row'>
    <td class='project_detail title'>Tech Stack</td>
    <td class='project_detail content'>
    <a href='https://reactnative.dev/' target='_blank'>
    <img  class='stack_logo' src="/images/stack_logo_rn.png" alt="ReactNative" /></a>
    <a href='https://www.typescriptlang.org/' target='_blank'>
    <img  class='stack_logo' src="/images/stack_logo_ts.png" alt="Typescript" /></a>
    <a href='https://redux.js.org/' target='_blank'>
    <img class='stack_logo' src="/images/stack_logo_redux.png" alt="Redux" /></a>
    <a href='https://www.styled-components.com/' target='_blank'>
    <img class='stack_logo' src="/images/stack_logo_styledComp.png" alt="StyledComponent" /></a>
    <a href='https://nodejs.org/en/' target='_blank'>
    <img class='stack_logo' src="/images/stack_logo_node.png" alt="nodeJs" /></a>
    <a href='https://expressjs.com/' target='_blank'>
    <img class='stack_logo' src="/images/stack_logo_express.png" alt="ExpressJs"/></a>
    <a href='https://typeorm.io/#/' target='_blank'>
    <img class='stack_logo' src="/images/stack_logo_typeorm.png" alt="TypeORM"/></a>
    <a href='https://www.mysql.com/' target='_blank'>
    <img class='stack_logo' src="/images/stack_logo_mysql.png" alt="MySQL"/></a>
    <a href='https://aws.amazon.com/' target='_blank'>
    <img class='stack_logo' src="/images/stack_logo_aws.png" alt="AWS"/></a>
</td>
  </tr>
    <tr class='project_detail row'>
    <td class='project_detail title'>Download</td>
    <td class='project_detail content'>
    <a href='https://play.google.com/store/apps/details?id=com.dfit&hl=ko' target='_blank'>
    <img  class='download_button' src="/images/googleplaystore.png" alt="React" /></a>
    <a href='https://apps.apple.com/kr/app/%EB%94%94%ED%95%8F-%EC%89%BD%EA%B2%8C-%EA%B8%B0%EB%A1%9D-%ED%95%98%EB%8A%94-%EC%9A%B4%EB%8F%99-%EC%9D%BC%EC%A7%80/id1521764155' target='_blank'>
    <img class='download_button' src="/images/appstore.png" alt="Redux" /></a>
</td>
  </tr>
</table>
</div><br>
## Overview

메모장에 오늘 한 운동을 기록하던 팀원의 니즈에 의해 탄생하게 된 운동 기록 앱.<br>
평소 헬스장을 가거나 웨이트 운동을 하지 않기 떄문에 더욱 어려웠지만 그만큼 사용하기 쉬운 플로우를 만들려고 팀원 모두 많은 고민을 했던 프로젝트이다.
<br>
운동을 쉽게 계획하고, 수행하고, 기록할 수 있도록 만들었으며, 실 사용자를 대상으로 진행 한 설문조사를 바탕으로 추가 업데이트도 계획하고 있다.
<br>
앞으로 수행한 운동에 대한 통계 기능📈도 꼭 추가하고 싶다.
<br><br>

## Key Features

### Dark 모드 지원
나 부터가 다크모드를 애정하기 때문에 앱을 만들게 된다면 다크모드도 꼭 지원해 보고 싶었다. 특히나 이번 디자인이 다크모드가 잘 어울리는 디자인이라 더욱 욕심이 났던 기능 😁. 시스템 다크모드 뿐만 아니라 사용자가 설정 페이지에서 Theme 설정을 따로 할 수 있게 만들었다. 
<div class='simulContainer'>
<img class='simulImg' src="/images/dfit-demo-gif-3.gif" alt="dfit-demo-gif" width="250em" />
</div>


### 회원가입 및 로그인

회원가입에서 사용자 이탈이 일어날 수 있으므로 회원가입 플로우를 간단하게 만들기 위해 팀원 모두 고민을 많이 했다. 그 결과 회원가입/로그인을 모두 이메일, 비밀번호로 간단하게 진행할 수 있도록 했으며, 비밀번호 찾기도 Universal Link를 이용해 메일에서 버튼을 누르면 바로 앱의 비밀번호 변경 창이 열리도록 하여 사용성을 향상시켰다.<br>
<div class='simulContainer'>
<img class='simulImg' src="/images/dfit-demo-gif-1.gif" alt="dfit-demo-gif" width="250em" style='margin-right:30px;' />
<img class='simulImg' src="/images/dfit-demo-gif-2.gif" alt="dfit-demo-gif" width="250em" style='margin-left:30px;' />
</div>

### 주간/월간 캘린더 구현
주간 캘린더와 월간 캘린더를 라이브러리를 쓰지 않고 원하는 기능만 넣어 자체적으로 구현했다. 주간 캘린더는 좌우 스크롤로 날짜를 이동하게 했으며, 월간 캘린더는 좌우 버튼으로 이동할 수 있도록 구현했다. 
<br>
<div class='simulContainer'>
<img class='simulImg' src="/images/dfit-demo-gif-4.gif" alt="dfit-demo-gif" width="250em" style='margin-right:30px;' />
<img class='simulImg' src="/images/dfit-demo-gif-5.gif" alt="dfit-demo-gif" width="250em" style='margin-left:30px;' />
</div>

### Scrollable 탭뷰 구현
카테고리별 운동 목록을 Scrollable TabView로 구현했으며, 운동 아이템을 추가, 수정, 삭제를 한 경우 해당 아이템으로 포커싱 되도록 하여 사용성을 향상시켰다.<br>
<div class='simulContainer'>
<img class='simulImg' src="/images/dfit-demo-gif-6.gif" alt="dfit-demo-gif" width="250em" style='margin-right:30px;' />
<img class='simulImg' src="/images/dfit-demo-gif-7.gif" alt="dfit-demo-gif" width="250em" style='margin-left:30px;' />
</div>

### What's Next? 사용자 피드백 반영
앱이 출시되고, 적은 수이지만 꾸준히 사람들이 사용해 주는 것이 너무 신기하고 감사했다 🙇‍♀️. 그래서 실제 유저에게 피드백을 받아 업데이트를 진행하기로 했다. CodePush를 이용해 설문조사 팝업을 띄우고, 설문조사를 진행했으며 곧 일부 피드백을 반영한 업데이트가 계획되어 있다.<br>
<div class='simulContainer'>
<img class='simulImg' src="/images/dfit-review-aos.jpg" alt="dfit-review-aos" width="250em" style='margin-right:30px;' />
<img class='simulImg' src="/images/dfit-review-ios.png" alt="dfit-review-ios" width="250em" style='margin-left:30px;' />
</div>

