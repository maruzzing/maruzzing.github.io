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

![react-testing-library](/images/react-testing-library.png "react-testing-library")

많은 테스트 프레임워크나 라이브러리가 있지만 [Jest](https://jestjs.io/)나 [Enzyme](https://airbnb.io/enzyme/)를 많이 사용한다고 한다.하지만! [React Testing Library](https://github.com/testing-library/react-testing-library)를 사용해 보기로... 그 이유는 React Testing Library가 사용자가 이용하는 관점에서 테스트를 하는 **Behavior Driven Test(행위 주도 테스트)**를 작성하기에 적합하기 때문이다.

## 라이브러리 설치 및 설정

먼저 [React Testing Library](https://github.com/testing-library/react-testing-library)와 [jest-dom](https://github.com/testing-library/jest-dom)을 설치한다. React Testing Library는 실제 브라우저 DOM을 기준으로 테스트를 진행하기 때문에 jest-dom을 설치하는 것이다.

```bash
npm install --save-dev @testing-library/react
npm install --save-dev @testing-library/jest-dom
```

그리고 `src/setupTests.js` 파일에 아래 두 가지를 추가한다. 첫 번째 라인은 각 테스트가 끝날 때 마다 jest-dom에 렌더링된 내용을 지우게 하는 것이다. 두 번째 라인은 jest-dom이 제공하는 테스팅 [matcher](https://github.com/testing-library/jest-dom#table-of-contents)들이 있는데, 이것을 jest test 러너가 불러올 수 있게 하는 것이다.

```react
import "@testing-library/react/cleanup-after-each";
import "@testing-library/jest-dom/extend-expect";
```

## 테스트 코드 작성

### React Testing Library 주요 API

React Testing Library는 jest-dom에 테스트 하고 싶은 컴포넌트를 `render()`해서 그 DOM에 그려진 내용으로 테스트 하는 것이다. 렌더된 특정 텍스트나, 라벨로 DOM을 서치하는 `getByText`, `getByLabelText`, 등의 쿼리가 있고, 컴포넌트에 `data-testid`를 부여해 그 id로 확인하는 `getByTestId` 등의 쿼리가 있다. ([더 많은 쿼리 보기](https://testing-library.com/docs/dom-testing-library/cheatsheet)) 또한, 동적 컴포넌트의 테스트를 위해 jest-dom에 액션을 주는 `fireEvent` API도 있다.

### 정적 테스트 코트 작성

먼저, name을 입력할 수 있는 `Form` 컴포넌트를 만들고 렌더링된 정적인 화면을 테스트 해보자.

```react
// src/Form.js
import React, { useState } from "react";

const Form = ({ onSubmit }) => {
  const [name, setName] = useState("");
  const handleSubmit = event => {
    onSubmit(name);
    event.preventDefault();
  };
  return (
    <div className="top">
      <form onSubmit={handleSubmit}>
        <label>
          Name:
          <input
            data-testid="nameInput"
            type="text"
            name="name"
            value={name}
            onChange={({ target: { value } }) => setName(value)}
          />
        </label>
        <input type="submit" value="Submit" />
      </form>
    </div>
  );
};
export default Form;
```

`Form` 컴포넌트가 위와 같을 때, 뷰가 제대로 그려졌는지 확인하기 위해서 아래와 같은 테스트 코드를 작성할 수 있다. test 화면에 초록불이 켜졌을 때의 희열이란 🤠

```react
// src/Form.test.js
import React from "react";
import { render } from "@testing-library/react";
import Form from "./Form";

describe("<Form />", () => {
  it("renders input", () => {
    const { getByLabelText, getByText, getByTestId } = render(<Form />);
    const label = getByLabelText("Name:");
    const input = getByTestId("nameInput");
    const button = getByText("Submit");
    expect(label).toBeInTheDocument();
    expect(input).toBeInTheDocument();
    expect(button).toBeInTheDocument();
  });
});

```

### 동적 테스트 코트 작성

<iframe src="https://codesandbox.io/embed/quizzical-khayyam-m9hnw?fontsize=14" title="react-test-library-ex" allow="geolocation; microphone; camera; midi; vr; accelerometer; gyroscope; payment; ambient-light-sensor; encrypted-media; usb" style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;" sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"></iframe>

<span class="reference">참고자료</span>

- [React Testing Library 사용법](https://www.daleseo.com/react-testing-library/)
