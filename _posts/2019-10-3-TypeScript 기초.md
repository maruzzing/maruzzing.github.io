---
layout: post
title: TypeScript 기초
date: 2019-10-03
comments: true
categories: [Study, react]
tags: [Tutorial, TypeScript]
excerpt: TypeScript는 MS에서 개발한 자바스크립트의 슈퍼셋(상위언어)로 Javascript에 타입을 지정함으로써 더욱 가독성이 높고 견고한 코드를 짤 수 있도록 도와준다.
---

TypeScript는 MS에서 개발한 자바스크립트의 슈퍼셋(상위언어)로 Javascript에 타입을 지정(정적 타입 언어)함으로써 더욱 가독성이 높고 안정적인 코드를 짤 수 있도록 도와준다. 또한, 인터프리터 언어인 자바스크립트와 다르게 컴파일 언어이기 때문에 코드를 작성하면서 오류를 줄이고, IDE의 코드 어시스트 기능을 지원받을 수 있는 장점이 있다.

### 변수 선언

타입 스크립트에서는 아래와 같이 변수를 선언할 때 타입을 지정하여 선언해 주어야 하며, 선언된 타입과 다른 타입의 값이 할당되는 경우 오류가 발생한다. `undefined`와 `null`의 타입은 각각 `undefined`와 `null`이다. 또한, 변수가 일정한 값만 가질 수 있을 때는 `const fontWeight: "thin" | "regular" | "bold"` 식으로 지정해 줄 수 있다.

```typescript
const name: string;
const visible: boolean = false;
const numbers: number[] = [1, 2, 3];
const time: number = 0;

const timer: number | undefined = undefined;
const nullableString: string | null = null;

const fontWeight: "thin" | "regular" | "bold" = "regular";
```

### 함수

함수의 경우에도, 인자의 타입이 명시되어야 하며, 함수 실행 후 리턴 값의 타입도 명시해 줄 수 있다. return 값이 없는 경우 `void`로 표시하면 된다.

```typescript
function (x:number, y:number):number {
    return x + y;
}

function (x:number, y:number):void {
    this.setState({
        sum:  x + y;
    })
}

function reducer(numbers:number[]):number{
    return numbers.reduce((accu, curr) => accu+curr, 0)
}
```

### 인터페이스

### 제네릭
