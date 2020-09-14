---
layout: post
title: sequelize associate와 include 사용하기
date: 2019-09-11
comments: true
categories: [Study, nodejs]
tags: [Express.js, Sequelize]
excerpt: 관계형 DB를 사용하면서 서로 관계가 있는 테이블의 데이터를 함께 호출하고 싶은 경우가 있다. 이 때 손쉽게 사용할 수 있는 것이 associate와 include 이다.
---

관계형 DB를 사용하면서 서로 관계가 있는 테이블의 데이터를 함께 호출하고 싶은 경우가 있다. 이 때 손쉽게 사용할 수 있는 것이 `associate`와 `include` 이다.

`associate`는 테이블 간 관계를 정의해 주는 것이고,
`include`는 db 쿼리 요청 시, 관계가 있는 테이블의 data도 함께 호출하는 것이다.

### associate

BelongsTo, HasOne, HasMany, BelongsToMany의 네 가지 associate 타입을 가지고 있는데, models의 해당 파일에 아래와 같이 정의해 주면 된다.

```javascript
    ...
    {
      tableName: "reviews",
      timestamps: true
    }
  );
  reviews.associate = function(models) {
    reviews.hasMany(models.Todos, { foreignKey: "reviewId" });
  };
  return reviews;
```

위의 예제에서, 1개의 review는 많은 todo 리스트를 가지고 있는 1:N 관계이며, todo 테이블은 `foreignKey`로 `reviewId`를 가지고 있다.

특정 review를 호출하면서, 관계된 todo도 호출하고 싶은 경우이다.

### include

이를 이용한 `findOne` 쿼리는 아래와 같이 작성할 수 있다. Reviews 테이블에서 특정 `reviewId`를 가진 리뷰 내용을 호출함과 동시에, Todos 테이블에 있는 todo 중, 그 `reviewId`를 가진 데이터를 'todos'라는 키의 값으로 같이 호출하는 것이다.

```javascript
Reviews.findOne({
  include: [
    {
      model: Todos,
      as: "todos",
      where: { reviewId }
    }
  ],
  where: {
    reviewId:
  },
  attributes: ["date", "review"]
});
```

그럼 결과 값은 아래와 같이 반환된다.

```javascript
{
    date: date,
    review: review,
    todos: [todos...]
}
```
