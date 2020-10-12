---
layout: post
title: Redux-toolkit을 활용한 상태관리 [1]
date: 2020-10-10
comments: true
categories: [Study, react]
tags: [Redux-toolkit, TypeScript]
excerpt: 리덕스를 사용하다 보면 급격하게 늘어나는 코드 양과 그에 비례한 복잡함에 불만이 생기기도 한다. 이러한 단점을 보완한 Redux Toolkit이 공개 되었는데, Redux를 사용하기에 필수적인 로직들을 포함하고 있어 단순하지만 강력한 도구이다.
---

리덕스를 사용하다 보면 급격하게 늘어나는 코드 양과 그에 비례한 복잡함에 불만이 생기기도 한다. 이러한 단점을 보완한 [Redux Toolkit](https://redux-toolkit.js.org/)이 공개 되었는데, Redux를 사용하기에 필수적인 로직들을 포함하고 있어 단순하지만 강력한 도구이다.
<br>

TypeScript와 함께 Redux Toolkit을 사용하는 방법을 기록으로 남겨 보고자 한다.
<br>
본 포스팅 에서는 Redux Toolkit 사용법을 알아보고, 이어지는 포스팅에서는 Redux Toolkit을 사용하여 간단한 튜토리얼을 진행해 보려고 한다.

## 설치

```bash
npm install --save redux react-redux @reduxjs/toolkit
npm install --dev @types/react-redux
```

## Redux Store 만들기

`createStore`로 store를 생성하고, `configureStore`로 간단하게 store 세팅을 할 수 있는데, `reducer` 필드는 필수적으로 전달해야 하며, 추가적으로 `middleware` 등을 추가해 줄 수 있다. 기존처럼 `combineReducers`로 reducer들을 결합하지 않아도 자동적으로 reducer들을 결합해 주며, `redux-thunk` 미들웨어를 디폴트로 포함하고 있다.

```javascript
import { configureStore } from '@reduxjs/toolkit';
import { useDispatch } from 'react-redux';

export const store = configureStore({
  reducer: {},
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
export const useAppDispatch = () => useDispatch<AppDispatch>();
```

## createSlice로 action과 reducer 생성하기

### slice란,

[공식 문서](https://redux-toolkit.js.org/tutorials/intermediate-tutorial#understanding-slices)에 따르면 리덕스 앱은 상태트리 최상단에 `combineReducersjs` 함수로 결합한 root reducer 객체를 가지고 있는데, 이 객체에서 각각의 key/value 쌍을 `slice`라고 하며, 해당 slice를 업데이트 하는 reducer 함수를 `slice reducer`라고 한다.
<br>

### createSlice,

Redux Toolkit에서는 `createSlice` API로 action과 reducer를 간단하게 생성할 수 있다. 큰 장점 중 하나로 [immer의 produce](https://github.com/immerjs/immer)를 자체적으로 지원해 주기 때문에 state를 직접 변형(mutate) 할 수 있다.👍

```javascript
import { createSlice } from '@reduxjs/toolkit'

const todosSlice = createSlice({
    // 액션 타입 문자열의 prefix로 사용됨
  name: 'todos',
    // 초기 state 값
  initialState: [],
    // 리듀서 맵. key는 액션 타입 문자열이 되고(ex. todos/addTodo), 함수는 액션이 dispatch 될 때 실행되는 reducer
  reducers: {
    addTodo(state, action) {
      const { id, text } = action.payload
      state.push({ id, text, completed: false })
    },
    toggleTodo(state, action) {
      const todo = state.find(todo => todo.id === action.payload)
      if (todo) {
        todo.completed = !todo.completed
      }
    }
  }
})

const { actions, reducer } = todosSlice;
export const { addTodo, toggleTodo } = actions;
export default reducer
```

## 컴포넌트에서 state와 action 사용하기

React Hooks를 사용하여 `react-redux`의 `useSelector`와 `useDispatch`로 간단하게 state와 action에 접근할 수 있다.

### useSelector

```javascript
import {RootState, useAppDispatch} from '../reducers';
import {setSearchValue} from '../reducers/todoSlice';
...

const dispatch = useAppDispatch();
....
    dispatch(addTodo({id:1, text:'Redux-toolkit을 활용한 상태관리'}));
...
```
### useDispatch

```javascript
import {RootState} from '../reducers';

const todos = useSelector((state: RootState) => state.todos);
```

## 비동기 처리하기

redux store 자체적으로는 비동기 처리를 하지 못하기 때문에 [`redux-thunk`](https://github.com/reduxjs/redux-thunk), [`redux-saga`](https://github.com/redux-saga/redux-saga), [`redux-observable`](https://github.com/redux-observable/redux-observable/) 등과 같은 미들웨어를 사용해서 비동기 액션 처리를 해야 한다. 공식 문서에서는 Redux Thunk를 사용하는 것을 추천하며, 이전에 언급했듯이 `configureStore` 함수는 기본적으로 thunk 미들웨어를 포함하고 있으므로 따로 설정해 주지 않더라도 바로 사용이 가능하다.

### createAsyncThunk

`createAsyncThunk` API로 비동기 액션을 만들면 이 액션에 대해 `pending`, `fulfilled`, `rejected` 상태에 대한 액션이 자동으로 생성된다. 먼저 비동기 액션은 아래와 같이 생성한다.

```javascript
import {createSlice, PayloadAction, createAsyncThunk} from '@reduxjs/toolkit';

interface MyKnownError {
  errorMessage: string
}

interface TodosAttributes {
  id: number;
  text: string;
  completed: boolean
}

export const fetchTodos = createAsyncThunk<
  TodosAttributes[], // 성공 시 리턴 타입
  number, // input type
  { rejectValue: MyKnownError } // thunkApi 정의({dispatch?, state?, extra?, rejectValue?})
>('todos/fetchTodos', async(userId, thunkAPI) => {
  try { 
    const {data} = await axios.get(`https://localhost:3000/todos/${userId}`);
    return data;
  } catch(e){
    return rejectWithValue({ errorMessage: '알 수 없는 에러가 발생했습니다.' });
  }
})
```

만들어진 비동기 액션에 대한 리듀서는 아래와 같이 `extraReducers`로 작성할 수 있다. `extraReducers`로 지정된 reducer는 외부 작업을 참조하기 위한 것이기 때문에 `slice.actions`에 생성되지 않는다. 또한, `ActionReducerMapBuilder`를 수신하는 콜백으로 작성하는 것이 권장된다.

```javascript
interface ITodoState {
  loading: boolean;
  error: null | string;
  todos: TodosAttributes[]
}

const initialState:ITodoState = {
  loading: false,
  error: null,
  todos: []
}

const todosSlice = createSlice({
  name: 'todos',
  initialState,
  reducers: {
    ...
  },
  extraReducers: (builder) => {
    builder
      .addCase(fetchTodos.pending, (state) => {
        state.error = null;
        state.loading = true;
      })
      .addCase(fetchTodos.fulfilled, (state, { payload }) => {
        state.error = null;
        state.loading = false;
        state.todos = payload;
      })
      .addCase(fetchTodos.rejected, (state, { payload }) => {
        state.error = payload;
        state.loading = false;
      });
  },
})
```

### unwrapResult

Thunk 액션은 프로미스를 반환하는데, `unwrapResult` 이용해서 바로 컴포넌트에서 비동기 액션 결과값을 핸들링 할 수 있다. 

```javascript
import {unwrapResult} from '@reduxjs/toolkit';
...
  try{
    const resultAction = await dispatch(fetchTodos(1));
    const todos = unwrapResult(resultAction);
    setTodos(todos);
  } catch(e){
    console.log(e)
  }
...
```
