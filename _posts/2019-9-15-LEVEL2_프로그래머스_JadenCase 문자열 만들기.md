---
layout: post
title: LEVEL2_프로그래머스_JadenCase 문자열 만들기
date: 2019-09-15
comments: true
categories: [Study, algorithm]
tags: [Javascript]
excerpt: JadenCase란 모든 단어의 첫 문자가 대문자이고, 그 외의 알파벳은 소문자인 문자열입니다. 문자열 s가 주어졌을 때, s를 JadenCase로 바꾼 문자열을 리턴하는 함수, solution을 완성해주세요.
---

### 문제

JadenCase란 모든 단어의 첫 문자가 대문자이고, 그 외의 알파벳은 소문자인 문자열입니다. 문자열 s가 주어졌을 때, s를 JadenCase로 바꾼 문자열을 리턴하는 함수, solution을 완성해주세요.

### 풀이

```javascript
function solution(s) {
  let str = s[0].toUpperCase();
  for (let i = 1; i < s.length; i++) {
    if (s[i - 1] === " ") {
      str += s[i].toUpperCase();
    } else {
      str += s[i].toLowerCase();
    }
  }
  return str;
}
```
