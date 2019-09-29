---
layout: post
title: React Testing Library로 유닛테스트 해보기
date: 2019-09-29
comments: true
categories: [Study, react]
tags: [React Testing Library, unit test]
excerpt: 처음 개발을 배울 때 테스트 주도 개발이 대세라는 이야기를 들었을 때 '코드를 테스트 하기 위해 또 다른 코드를 작성해야 한다고?' 하며 겁먹었던 기억이 난다 ㅎㅎ 그런데 공부를 하다보니 조금 더 깔끔한 코드, 효율적인 코드에 대해 고민하게 되고 이참에 테스트에 대해 공부해 보기로 했다.
---

처음 개발을 배울 때 테스트 주도 개발이 대세라는 이야기를 들었을 때 '코드를 테스트 하기 위해 또 다른 코드를 작성해야 한다고?' 하며 겁먹었던 기억이 난다 ㅎㅎ 그런데 공부를 하다보니 조금 더 깔끔한 코드, 효율적인 코드에 대해 고민하게 되고 이참에 테스트에 대해 공부해 보기로 했다.

특히나 내가 주로 사용하는 Javascript는 동적 타입언어인 데다가 코드 문제를 즉시 확인해주는 컴파일러가 없기 때문에 실행하기 전에 오류를 발견하기 어렵고, 발견 하더라도 버그 발견 시점이 코드 구현 시점에서 멀어질수록 원인을 찾기 어렵다. 💦

많은 테스트 프레임워크나 라이브러리가 있지만 [Jest](https://jestjs.io/)나 [Enzyme](https://airbnb.io/enzyme/)를 많이 사용한다고 한다.하지만! [React Testing Library](https://github.com/testing-library/react-testing-library#basic-example)를 사용해 보기로... 그 이유는 React Testing Library가 사용자가 이용하는 관점에서 테스트를 하는 Behavior Driven Test(행위 주도 테스트)를 작성하기에 적합하기 때문이다.
