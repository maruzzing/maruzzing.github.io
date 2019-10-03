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

함수의 경우에도, 인자의 타입이 명시되어야 하며, 함수 실행 후 리턴 값의 타입도 명시해 줄 수 있다. return 값이 없는 경우 `void`로 설정해 주면 된다.

```typescript
function (x:number, y:number):number {
    return x + y;
}

function ():void {
    this.setState({
        visible: !this.state.visible
    })
}

function reducer(numbers:number[]):number{
    return numbers.reduce((accu, curr) => accu+curr, 0)
}
```

### interface

클래스나 객체를 위한 타입선언으로, 클래스가 특정 조건을 만족해야 함을 명시한다. 이는 다른 정적타입 언어에서도 많이 쓰이는 개념이다.

```typescript
interface Shape {
  getArea(): number;
}

class Rect implements Shape {
  width: number;
  height: number;

  constructor(width: number, height: number) {
    this.width = width;
    this.height = height;
  }
  getArea() {
    return width * height;
  }
}

const rect = new Rect(4, 8);

function getRecArea(rect: Rect) {
  return rect.getArea();
}
```

`implements` 키워드로 `Rect`이라는 클래스가 `Shape` 인터페이스를 구현함을 선언하는 것이다.
class 서언 부분을 좀 더 간단하게 표현하면, 아래와 같으며, `private` 혹은 `public`으로 선언해주면 된다.

```typescript
class Rect implements Shape {
  constructor(private width: number, private height: number) {}
  getArea() {
    return width * height;
  }
}
```

인터페이스가 객체를 만들때도 쓰이는데, 아래와 같이 사용할 수 있다.

```typescript
// interface 미사용
const student: { name: string; hobby: string } = {
  name: "Steve",
  hobby: "soccer"
};

// interface 사용
interface Student {
  name: string;
  hobby: string;
}
const student: Student = {
  name: "Steve",
  hobby: "soccer"
};
```

특정 프로퍼티가 있을 수도 있고, 없을 수도 있다면, 아래와 같이 물음표(?)로 표기할 수 있다. 만약 물음표로 표시하지 않은 상태에서 Student 인터페이스를 구현한 객체에 hobby 값이 없다면 오류가 나타난다.

```typescript
interface Student {
  name: string;
  hobby?: string;
}
```

인터페이스 간의 상속도 가능한데, Student의 모든 속성을 가진 Person이라는 인터페이스를 만든다면, 아래와 같이 표현할 수 있다. 이 경우 Classmate는 name, hobby, 그리고 age 속성을 모두 가지게 된다.

```typescript
interface Student {
  name: string;
  hobby: string;
}

interface Classmate extends Student {
  age: number;
}
```

### Type Alias

interface는 새로운 Type을 정의하는데, Type Alias는 실제 Type을 정의하는 것은 아니고, 이미 만들어져 있는 Type의 refer로 정의하는 것이다. 상속을 받을 수는 있지만 상속 할 수는 없다.

```typescript
interface Student {
  name: string;
  hobby: string;
}

type Classmate = Student & {
  age: number;
};

type FontWeight = "thin" | "regular" | "bold";
const fontWeight: FontWeight = "thin";
```

### Generics

제네릭은 어떠한 클래스, 함수, 인터페이스 등에서 사용할 타입을 그 클래스, 함수, 인터페이스를 사용할 때 결정하는 프로그래밍 기법이다.

```typescript
// Generics 를 쓰지 않을 경우
function wrap(param: any) {
  return { param };
}

// Generics 를 쓸 경우
function wrap<T>(param: T) {
  return { param };
}

// 어떤 type을 넣느냐에 따라 type이 결정되고, 이후 type이 지켜진다.
const wrapped = wrap(10);

interface Itmes<T, V> {
  list: T[];
  value: V;
}

const items: Items<string, number> = {
  list: ["a", "b", "c"],
  value: 10
};
```
