---
layout: post
title: React Native 주간 달력 구현하기
date: 2019-09-09
comments: true
categories: [Study, rnative]
tags: [React Native, Date, Calendar]
excerpt: 월간 달력이 아닌, 주간달력을 구현해야 하는데 캘린더 라이브러리는 거의 월간단력을 지원해주고 있었다. 그래서 기초부터 아예 만들어버린 주간달력!
---

월간 달력이 아닌, 주간달력(아래 사진과 같은)을 구현해야 하는데 캘린더 라이브러리는 거의 월간단력을 지원해주고 있었다. 그래서 기초부터 만들어 본 주간달력!

<div style="display: flex; justify-content: center; margin:10px 0">
<img src="/images/calendar_week_view.png" alt="calendar_week_view" width="400em">
</div>

### 오늘을 기준으로 일주일 치 Date 객체로 이루어 진 배열을 생성, state에 저장

```react
class Calendar extends React.Component {
  constructor(props) {
    super(props);
  }
  componentDidMount = () => {
    let now = new Date();
    let date = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    let week = this.makeWeekArr(date);
    this.setState({
        date, week
    })
  };

    makeWeekArr = date => {
    let day = date.getDay();
    let week = [];
    for (let i = 0; i < 7; i++) {
      let newDate = new Date(date.valueOf() + 86400000 * (i - day));
      week.push([i, newDate]);
    }
    return week;
  };
  ...
}
```

### 전주, 다음주 버튼 실행 시 새로운 week 배열 만들기

```react
  onPressArrowLeft = () => {
    let newDate = new Date(this.props.date.valueOf() - 86400000 * 7);
    let newWeek = this.makeWeekArr(newDate);
  this.setState({
        date: newDate, week: newWeek
    })
  };

  onPressArrowRight = () => {
    let newDate = new Date(this.props.date.valueOf() + 86400000 * 7);
    let newWeek = this.makeWeekArr(newDate);
  this.setState({
        date: newDate, week: newWeek
    })
  };
```

<br>
여기까지 하고 하나의 date 컴포넌트를 생성하여 week 배열을 매핑해주면 된다.

<div class='simulContainer'>
<img class='simulImg' src="/images/calendar_gif.gif" alt="fetch-demo-gif" width="350em"  style='margin-right:30px;' />
</div>
