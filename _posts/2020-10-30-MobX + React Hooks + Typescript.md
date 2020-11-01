---
layout: post
title: MobX + React Hooks + Typescript
date: 2020-10-30
comments: true
categories: [Study, react]
tags: [MobX, React Hooks, TypeScript]
excerpt: MobX는 Redux 및 Context API 외에 React 앱에서 사용할 수있는 상태 관리 라이브러리로 객체 지향 프로그래밍 및 반응형 프로그래밍 원칙의 영향을 받는다. MobX에서는 특정 상태(데이터)를 관찰하여 상태가 변경되었을 때 자동으로 업데이트 한다. 
---

<div style='display: flex; justify-content: center;'>
  <img src="/images/mobx.svg" alt="mobx" width="200em">
</div>

MobX는 Redux 및 Context API 외에 React 앱에서 사용할 수있는 상태 관리 라이브러리로 객체 지향 프로그래밍 및 반응형 프로그래밍 원칙의 영향을 받는다. MobX에서는 특정 상태(데이터)를 관찰하여 상태가 변경되었을 때 자동으로 업데이트 한다. 
<br>

리덕스는 단일 store를 가지면서 상태 처리의 책임은 다수의 리듀서에서 담당하는 반면, MobX는 store의 개수에 제한이 없어 기능별, 로직별로 store를 분리하여 관리할 수 있으며, 데코레이터를 이용해 간락하게 코드를 작성할 수 있다. 또한, Redux에서 상태값는 불변 객체이며 순수 함수에 의해서만 변경되어야 하지만 MobX에서는 state 변경이 가능하기 때문에 간단하게 업데이트가 가능하다.
<br>

하지만 MobX의 높은 자유도는 테스트와 디버그를 어렵게 하는 단점이 있다.
<br>

# 핵심 개념

![MobX Flow](/images/mobx_flow.png "MobX Flow")

### Observable state

관찰 가능한 상태를 말하며, 이 관찰 대상의 변화는 reaction과 computations을 일으킨다. 

### Computed values

observable state의 변화에 따른 연산 값으로 반환하는 값이 변경 되었을 때 reaction을 일으키므로 불필요한 rerender를 줄여주기 때문에 성능 개선에 도움을 준다.

### Actions

observable state상태에 변화를 일으키는 것이다.

### Reactions

UI 업데이트 등 observable state상태에 변화에 따른 반응을 말한다.
<br>
<br>

# MobX + React Hooks + Typescript

리액트에서 MobX를 사용하기 위해서는 [`mobx-react`](https://github.com/mobxjs/mobx-react)라는 라이브러리가 필요한데, hooks 문법을 사용한다면 `mobx-react`는 v6이상이 필요하며,, `mobx-react`의 함수형 컴포넌트에서 사용할 수 있는 API만 제공하는 [`mobx-react-lite`](https://github.com/mobxjs/mobx-react-lite)도 사용할 수 있다.

```
npm i mobx mobx-react-lite
```

## 슈퍼마켓 구현하기

**velopert**님의 MobX 포스팅 중, [슈퍼마켓 구현하기](https://velog.io/@velopert/MobX-3-%EC%8B%AC%ED%99%94%EC%A0%81%EC%9D%B8-%EC%82%AC%EC%9A%A9-%EB%B0%8F-%EC%B5%9C%EC%A0%81%ED%99%94-%EB%B0%A9%EB%B2%95-tnjltay61n)를 React Hooks + Typescript로 적용해 보겠다.

### 스토어 만들기

**./stores/market.ts**

`MarketStore`를 아래와 같이 정의해 준다. 

```typescript
import { observable, action, computed } from 'mobx';

export interface ISelectedItem {
    name:string;
    price:number; 
    count:number;
}

export class MarketStore{
    readonly selectedItems = observable<ISelectedItem>([]);

    @action
    put = (name:string, price:number) => {
        const exist = this.selectedItems.find(item => item.name === name);
        if(!exist){
            this.selectedItems.push({
                name,
                price,
                count: 1
            });
        } else {
            exist.count++;
        }
    };

    @action
    take = (name:string) => {
        const itemToTake = this.selectedItems.find(item => item.name === name)!
        itemToTake.count--;
        if(itemToTake.count === 0){
            this.selectedItems.remove(itemToTake);
        }
    };

    @computed
    get total() {
      console.log('총합 계산...');
      return this.selectedItems.reduce((previous, current) => {
        return previous + current.price * current.count;
      }, 0);
    };
}
```

**./stores/index.ts**

스토어를 프로젝트 내 함수 컴포넌트에서 사용할 수 있도록 `useStore` 훅을 정의해 준다.

```typescript
import { createContext, useContext } from 'react';
import { MarketStore } from './market';

export interface IStore {
  market: MarketStore;
}

export const store: IStore = {
  market: new MarketStore(),
};

export const StoreContext = createContext(store);

export const StoreProvider = StoreContext.Provider;

export const useStore = () => {
  return useContext(StoreContext);
};
```

### 스토어 적용하기

**index.ts**

```typescript
...
import { store, StoreProvider } from './stores'

ReactDOM.render(
    <StoreProvider value={store}>
      <App />
    </StoreProvider >
  document.getElementById('root')
);
```

**App.tsx**

```typescript
import React from 'react';
import SuperMarket from './components/SuperMarket';

export default function App(){
  return (
    <div>
      <SuperMarket />
    </div>
  );
}
```

**SuperMarket.tsx**

```typescript
import React from 'react';
import SuperMarketTemplate from './SuperMarketTemplate';
import ShopItemList from './ShopItemList';
import BasketItemList from './BasketItemList';
import TotalPrice from './TotalPrice';

export default function SuperMarket(){
  return <SuperMarketTemplate items={<ShopItemList />} basket={<BasketItemList />} total={<TotalPrice />}/>;
};
```

**SuperMarketTemplate.tsx**

```typescript
import React from 'react';
import styled from 'styled-components';

interface ISuperMarketTemplate{
    items:any,
    basket:any
    total:any
}

const Container = styled.div`
    width: 768px;
    display: flex;
    border: 1px solid black;
    margin-left: auto;
    margin-right: auto;
    margin-top: 3rem;
` 

const ItemsWrapper = styled.div`
    background: #f8f9fa;
    padding: 1rem;
    flex: 1;
` 

const BasketWrapper = styled.div`
    background: #f8f9fa;
    padding: 1rem;
    flex: 1;
` 

const Title = styled.h2`
  margin-top: 0;
`

export default function SuperMarketTemplate({ items, basket, total }:ISuperMarketTemplate) {
  return (
    <Container>
      <ItemsWrapper>
        <Title>상품</Title>
        {items}
      </ItemsWrapper>
      <BasketWrapper>
        <Title>장바구니</Title>
        {basket}
        {total}
      </BasketWrapper>
    </Container>
  );
};
```

**ShopItemList.tsx**

`useStore` 훅을 이용해 store에 접근한다.

```typescript
import React from 'react';
import ShopItem from './ShopItem';
import { useStore } from '../stores';

const ITEMS = [
    {
      name: '생수',
      price: 850,
    },
    {
      name: '신라면',
      price: 900,
    },
    {
      name: '포카칩',
      price: 1500,
    },
    {
      name: '새우깡',
      price: 1000,
    },
  ];

  export default function ShopItemList(){
    const { market } = useStore();

    const onPut = (name:string, price:number) => {
      market.put(name, price)
    }

    const itemList = ITEMS.map(item => <ShopItem {...item} key={item.name} onPut={onPut} />);

    return (
        <div>{itemList}</div>
    )
}
```

**ShopItem.tsx**

```typescript
import React from 'react';
import styled from 'styled-components';

interface IShopItem {
    name: string;
    price: number;
    onPut: (name:string, price:number) => void;
}

const Item = styled.div`
    margin-top: 1rem;
    background: white;
    border: 1px solid #495057;
    padding: 0.5rem;
    border-radius: 2px;
    cursor: pointer;
    &:hover {
        background: #495057;
        color: white;
    }
`

const Name = styled.h4`
    margin-top:0;
    margin-bottom: 1rem;
`

export default function ShopItem({ name, price, onPut }:IShopItem){
  return (
    <Item className="ShopItem" onClick={() => onPut(name, price)}>
      <Name>{name}</Name>
      <div>{price}원</div>
    </Item>
  );
};
```

**BasketItemList.tsx**

`useObserver`를 이용하여 `observable` 값의 변화를 감지한다.

```typescript
import React from 'react';
import BasketItem from './BasketItem';
import { useObserver } from 'mobx-react-lite';
import { useStore } from '../stores';

export default function  BasketItemList() {
    const { market } = useStore();

    const onTake = (name:string) => {
        market.take(name);
    };

    return useObserver(()=> {
        const itemList = market.selectedItems.map((item) => (
            <BasketItem item={item} key={item.name} onTake={onTake} />
        ));
        return (
            <div>
                {itemList}
            </div>
        );
    })
};
```

**BasketItemList.tsx**

```typescript
import React from 'react';
import styled from 'styled-components';
import { ISelectedItem } from '../stores/market';
import { useObserver } from 'mobx-react-lite';

interface IBasketItem  {
    onTake:(name:string)=>void
    item: ISelectedItem
}

const Item = styled.div`
    display: flex;
    width: 100%;
`
const Name = styled.div`
    flex: 2;
`

const Price = styled.div`
    flex: 1;
`

const Count = styled.div`
    flex: 1;
`

const Return = styled.div`
    margin-left: auto;
    color: #f06595;
    cursor: pointer;
    &:hover {
        text-decoration: underline;
    }
`

export default function BasketItem({ item, onTake }:IBasketItem){
    return useObserver(()=>{
        const {name, price, count} = item;
        return (
          <Item className="ShopItem" onClick={()=>onTake(name)}>
            <Name>{name}</Name>
            <Price>{price}원</Price>
            <Count>{count}</Count>
            <Return>갖다놓기</Return>
          </Item>
        );
    })
};
```

**BasketItemList.tsx**

```typescript
import React from 'react';
import { useObserver } from 'mobx-react-lite';
import { useStore } from '../stores';

export default function TotalPrice(){
    const { market } = useStore();
    return useObserver(()=> {
        return (
            <div>
                <hr />
                <p>
                    <b>총합: </b> {market.total}원
                </p>
            </div>
        );
    });
}
```

![mobx-market](/images/mobx-market.png "mobx-market")
