---
layout: post
title: React Native FlatList 최적화 하기
date: 2020-10-16
comments: true
categories: [Study, rnative]
tags: [React Native, FlatList, Performance]
excerpt: FlatList는 많은 양의 데이터를 출력해야 할 때 유용하게 쓰이는 API 이다. 하지만 FlatList를 제대로 최적화 하지 않으면 스크롤이 느려지고 성능이 낮아지는 문제가 발생한다. 
featured-image: 
---

FlatList는 많은 양의 데이터를 출력해야 할 때 유용하게 쓰이는 API 이다. 하지만 FlatList를 제대로 최적화 하지 않으면 스크롤이 느려지고 성능이 낮아지는 문제가 발생한다. 
<br>

FlatList를 최적화하는 방법을 알아보자.

## 인라인 화살표 함수 사용하지 않기

화살표 함수를 속성 값으로 입력하게 되면 DOM 컴포넌트가 렌더링 될 때마다 새로운 함수가 생성되기 때문에 불필요한 렌더링이 발생한다. 따라서 `renderItem`,  `keyExtractor`, 그리고 `ListHeaderComponent` 등의 속성을 구성할 때 `useCallback`을 사용하여 꼭 필요할 때만 업데이트 되도록 하는 것이 좋다.

```javascript
const renderItem = useCallback(({item}) => <ListItem item={item} />, [])
const keyExtractor = useCallback((item) => item.id.toString(), [])

<FlatList 
    data={DATA}
    renderItem={renderItem}
    keyExtractor={keyExtractor}
/>
```

## getItemLayout

`getItemLayout` 속성을 정의해 주면 item의 레이아웃 크기가 매번 계산되지 않아도 되므로 성능 개선에 효과적이다.

```javascript
const ITEM_HEIGHT = 200;

const getItemLayout = useCallback(
    (data, index) => ({
        length: ITEM_HEIGHT,
        offset: ITEM_HEIGHT * index,
        index,    
    }), 
    []
);

<FlatList 
    data={DATA}
    renderItem={renderItem}
    keyExtractor={keyExtractor}
    getItemLayout={getItemLayout}
/>
```

## initialNumToRender

| TYPE | DEFAULT | 
| :----- | :----- | 
| Number | 10 |

최초 몇 개의 item을 렌더할지 명시해 주는 옵션으로, 초기 렌더링 선능을 향상할 수 있다. 단, 아이템 높이와 화면에서 FlatList 영역의 높이를 고려해 빈 곳이 발생하지 않도록 해야 한다.

## maxToRenderPerBatch

| TYPE | DEFAULT | 
| :----- | :----- | 
| Number | 10 |

스크롤 시 렌더링 할 항목을 결정한다. 기본 값이 10으로 설정되어 있기 때문에 item 크기와 FlatList 영역의 높이에 따라 실제로 필요한 최적의 숫자로 설정해 주는 것이 좋다.

## windowSize

| TYPE | DEFAULT | 
| :----- | :----- | 
| Number | 21 |

FlatList 영역의 높이 외부에서 렌더링 되는 최대 항목수를 결정해 주는 옵션이다. 이 속성에 전달되는 숫자 1은 FlatList 영역의 높이와 같으며, 기본값이 21인데 현재 스크린을 기준으로 앞으로 10개, 뒤로 10개를 추가로 렌더링 한다는 뜻이다. 보통의 경우 동시에 21개 까지 렌더링 할 필요가 없으므로 item 크기를 기반으로 최적의 숫자로 설정해 주는 것이 좋다.

## removeClippedSubviews

| TYPE | DEFAULT | 
| :----- | :----- | 
| Boolean | False |

`true`로 설정하는 경우 화면에서 벗어난 아이템을 unmount하여 메모리를 아껴준다.


