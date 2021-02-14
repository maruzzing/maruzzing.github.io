---
layout: post
title: 프로그래머스_2021 KAKAO BLIND RECRUITMENT
date: 2021-02-14
comments: true
categories: [Study, algorithm]
tags: [Javascript]
excerpt: 2021 카카오 신입공채 1차 온라인 코딩 테스트 문제 차근차근 풀어보기
hidden: true
---

2021 카카오 신입공채 1차 온라인 코딩 테스트 문제 차근차근 풀어보기
[[참고]](https://tech.kakao.com/2021/01/25/2021-kakao-recruitment-round-1/)

## [신규 아이디 추천](https://programmers.co.kr/learn/courses/30/lessons/72410)

```javascript
function replaceDot (str) {
    str = str.replace(/[.]{2,}/g, '.');
    str = str.replace(/^[.]/, '');
    str = str.replace(/[.]$/, ''); 
    return str;
}

function solution(new_id) {
    const regExpId = /[^0-9a-z-_.]/g;
    new_id = new_id.toLowerCase();
    new_id = new_id.replace(regExpId, '');
    new_id = replaceDot(new_id);
    if(new_id.length === 0){
        return solution('a')
    }
    if(new_id.length > 15){
        return solution(new_id.slice(0, 15));
    }
    while(new_id.length < 3){
        new_id += new_id[new_id.length -1]
    }
    return new_id;
}
```

## [순위 검색](https://programmers.co.kr/learn/courses/30/lessons/72412)

**풀이1** (효율성 실패)
```javascript
function solution(info, query) {
    let answer = [];
    info = info.map(i => i.split(' '));
    query = query.map(q => q.split(/ and | /));
    query.forEach(q => {
        let count = 0; 
        const [qLan, qEnd, qCarrer, qFood, qScore] = q;
        for(let i = 0; i < info.length; i ++){
            const [lan, end, career, food, score] = info[i];
            if((qLan === '-' || lan === qLan) && (qEnd === '-' || end === qEnd) && (qCarrer === '-' || career === qCarrer) && (qFood === '-' || food === qFood) && (Number(score) >= Number(qScore))) count++;
        }
        answer.push(count);
    })
    return answer;
}
```
<br>

**풀이2** 

```javascript
function solution(info, query) {
    let answer = [];
    let infoMap = {};
    function genCase(splited, pointer, score){
        const key = splited.join(' ');
        if(infoMap[key]) { infoMap[key].push(score); } else { infoMap[key] = [score]; };
        for(let i = pointer; i < splited.length; i++){
            let temp = [...splited];
            temp[i] = '-';
            genCase(temp, i+1, score);
        };
    };
    
    info = info.map(i => i.split(' '));
    
    for(let i = 0; i < info.length; i++){
        const condition = info[i];
        const score = Number(condition.pop());
        genCase(condition, 0, score);
    };
    for(const key in infoMap){
        infoMap[key] = infoMap[key].sort((a, b) => a - b);
    }

    query = query.map(q => q.split(/ and | /));
    
    for(let i = 0; i < query.length; i++){
        const score = Number(query[i].pop());
        const key = query[i].join(' ');
        const value = infoMap[key];
        if(value){
            let start = 0; 
            let end = value.length; 
            while(start < end){
                const mid = Math.floor((start + end) / 2);
                if(value[mid] < score) start = mid + 1; 
                else end = mid;
            }
            answer.push(value.length - start);
        } else {
            answer.push(0);
        }
    }
    return answer;
}
```