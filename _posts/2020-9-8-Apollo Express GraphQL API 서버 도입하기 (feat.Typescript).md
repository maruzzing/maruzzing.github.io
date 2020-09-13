---
layout: post
title: Apollo Express GraphQL API 서버 도입하기 (feat.Typescript)
date: 2020-09-08
comments: true
categories: [Study, graphql]
tags: [Express, Typescript, GraphQL, Apollo Server]
excerpt: 여태 토이 프로젝트 이건, 회사 프로젝트 이건 Rest API 만 사용했었는데, 지금 사용하고 있는 공유 오피스에서 알게 된 백엔드 개발자가 React와 GraphQL 사용법을 물어와 관련내용을 좀 찾아보니 너무 신세계라👀 이참에 GraphQL을 공부해 보기로 했다.

---

여태 토이 프로젝트 이건, 회사 프로젝트 이건 Rest API 만 사용했었는데, 지금 사용하고 있는 공유 오피스에서 알게 된 백엔드 개발자가 React와 GraphQL 사용법을 물어와 관련내용을 좀 찾아보니 너무 신세계라👀 이참에 GraphQL을 공부해 보기로 했다.

사용할 기술 스택은 아래와 같다.
- Node.js + Express.js
- Typescript
- GraphQL
- Apollo Server
- MySQL
- Sequelize

<br>
먼저 이번 포스팅에서는 간단한 서버 구축을 해보려 한다.


# 프로젝트 생성 및 타입스크립트 설정하기

## 프로젝트 생성

먼저, 원하는 위치에 디렉토리를 만들고 새로운 프로젝트를 생성한다. 

```bash
$ mkdir [프로젝트 이름]
$ cd [프로젝트 이름]
$ npm init --yes
```

## 타입스크립트 설정

타입스크립트와 nodemon, 그리고 ts-node를 설치해 주고, 루트 디렉토리에 `tsconfig.json` 파일을 생성한다. 

```bash
$ npm i typescript nodemon ts-node --save-dev
```

생성된 `tsconfig.json` 파일을 아래와 같이 작성해 준다.

```json
{
    "compilerOptions": {
      "target": "es6",
      "module": "commonjs",
      "lib": [
        "dom",
        "es6"
      ],
      "outDir": "dist",         
      "rootDir": "src",
      "moduleResolution": "node",
      "strict": true,
      "esModuleInterop": true,                     
    },
    "exclude": [
      "node_modules"
    ],
    "include": [
      "./src/**/*.tsx",
      "./src/**/*.ts"
    ]
  }
```

# Express에 GraphQL API 적용하기

이제 본격적으로 서버를 생성해 볼 차례. 

## 디펜던시 설치

필요한 디펜던시와 이외 관련된 type 패키지들도 설치해 준다.

```bash
$ npm i apollo-server-express express graphql
$ npm i compression morgan dotenv
```

## 기본 express 서버 생성하기

루트 디렉토리에 src 폴더를 생성하고 `server.ts` 파일을 아래와 같이 작성해 준다.

```javascript
import express from 'express';
import morgan from 'morgan';
import compression from 'compression';

dotenv.config();

const app = express();

const prod: boolean = process.env.NODE_ENV === 'production';
const port = prod ? process.env.PORT : 8000;

app.use(morgan('dev'));
app.use(compression());

app.listen(port, (): void =>
  console.log(
    `\n🚀      GraphQL is now running on http://localhost:${port}/graphql`,
  ),
);
```

`package.json`의 스크립트를 아래와 같이 수정하고 `npm run dev`를 실행하면 정상적으로 서버가 구동됨을 확인할 수 있다.

```json
...
  "scripts": {
    "dev": "nodemon 'src/server.ts' --exec 'ts-node' src/server.ts -e ts"
  },
...
```

## Apollo Server 적용하기

GraphQL의 쿼리가 가능한 타입 정의(typeDefs)와 쿼리를 요청했을 때 서버가 어떻게 처리할지를 정의한 리졸버(resolvers)를 정의하여 `ApolloServer` 생성자에 넘겨준다. 이후 포스팅에서 typeDefs와 resolvers를 정의하는 폴더를 분기할 예정이다.

생성한 `ApolloServer` 인스턴스에 `applyMiddleware` 메서드를 이용해 이전에 생성한 express 서버를 넘겨 준다. 이 때 cors 설정도 추가해 줄 수 있다.

```javascript
...
import { ApolloServer, gql } from 'apollo-server-express';
...

const typeDefs = gql`
  type Query {
    ping: String
  }
`;

// Provide resolver functions for your schema fields
const resolvers = {
  Query: {
    ping: () => '👋 pong! 👋',
  },
};

const apolloServer = new ApolloServer({
  typeDefs,
  resolvers,
});

apolloServer.applyMiddleware({ app, cors: false });
...
```

## 테스트 해보기

이제 제대로 구축이 되었는지 쿼리를 보내보자. 콘솔에 아래오 같이 curl 명령어를 전송하면 응답이 오는 것을 확인할 수 있으며, 

```bash
$ curl -X POST "http://localhost:8000/graphql" -H "content-type: application/json" -d '{"query":"{ping}"}' 

{"data":{"ping":"👋 pong! 👋"}}
```
<br>


아래 그림과 같이 postman을 활용해서도 확인할 수 있다.
![express-graphql-simple-setup-test](/images/express-graphql-simple-setup-test.png "express-graphql-simple-setup-test")