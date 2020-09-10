---
layout: post
title: Express 서버에 GraphQL API 도입하기 (feat. Typescript)
date: 2020-09-08
comments: true
categories: [Study, graphql]
tags: [Express, Typescript, GraphQL]
excerpt: 여태 토이 프로젝트 이건, 회사 프로젝트 이건 Rest API 만 사용했었는데, 지금 사용하고 있는 공유 오피스에서 알게 된 백엔드 개발자가 React와 GraphQL 사용법을 물어와 이참에 GraphQL을 공부해 보기로 했다. 
---
여태 토이 프로젝트 이건, 회사 프로젝트 이건 Rest API 만 사용했었는데, 지금 사용하고 있는 공유 오피스에서 알게 된 백엔드 개발자가 React와 GraphQL 사용법을 물어와 이참에 GraphQL을 공부해 보기로 했다.

먼저, Express 서버에 GraphQL API를 적용해 보자.


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
    "target": "es5",
    "module": "commonjs",         
    "lib": ["dom","es6"], 
    "outDir": "dist", 
    "rootDir": "src", 
    "strict": true,         
    "esModuleInterop": true,        
  },
  "exclude": [ "node_modules" ],
  "include": [ "src/**/*.ts" ]
}
```

# Express에 GraphQL API 적용하기

이제 본격적으로 서버를 생성해 볼 차례. 

## 디펜던시 설치

필요한 디펜던시들을 설치한다. [express-graphql](https://github.com/graphql/express-graphql)은 express와 graphql을 연결해 간단하게 http 서버를 만들 수 있게 도와주며, [graphql-tools](https://github.com/ardatan/graphql-tools)는 자바스크립트에서 GraphQL 스키마와 리졸버를 구축할 수 있도록 도와주는 패키지 이다. 

이외 관련된 type 패키지들도 설치해 주면 된다.

```bash
$ npm i express graphql express-graphql graphql-tools
$ npm i cors compression morgan dotenv
```

## 기본 express 서버 생성하기

루트 디렉토리에 `server.ts` 파일을 생성하고 아래와 같이 기본 서버 코드를 작성한다.

```javascript
import express from 'express';
import cors from 'cors';
import morgan from 'morgan';
import compression from 'compression';

dotenv.config();

const app = express();

const prod: boolean = process.env.NODE_ENV === 'production';
const port = prod ? process.env.PORT : 8000;

app.use(morgan('dev'));
app.use(cors());
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

## GraphQL schema와 resolver 정의하기

[express-graphql](https://github.com/graphql/express-graphql#simple-setup) 문서에 따르면 graphql http 서버를 생성하기 위해서는 schema 정의가 필요하다. schema는 [graphql-js](https://github.com/graphql/graphql-js)의 `GraphQLSchema` 인스턴스로 [graphql-tools](https://github.com/ardatan/graphql-tools)를 사용해 쉽게 정의할 수 있다.

![express-graphql-simple-setup](/images/express-graphql-simple-setup.png "express-graphql-simple-setup")

루트 디렉토리에 schema.ts 파일을 만들고 아래와 같이 schema를 정의한다. GraphQL의 쿼리가 가능한 타입 정의(typeDefs)와 쿼리를 요청했을 때 서버가 어떻게 처리할지를 정의한 리졸버(resolvers)로 구성된다. 

GraphQL의 타입 정의 방식은 [여기](https://graphql.org/learn/schema/)서 자세하게 알아볼 수 있다.

```javascript
import { makeExecutableSchema } from '@graphql-tools/schema';
import { IResolvers } from 'graphql-tools';
import { GraphQLSchema } from 'graphql';

const typeDefs = `
  type Query {
    ping: String!
}`

const resolverMap: IResolvers = {
  Query: {
    ping() {
      return `👋 pong! 👋`;
    }
  }
};

const schema: GraphQLSchema = makeExecutableSchema({
  typeDefs,
  resolvers,
});

export default schema;
```
이제 정의한 schema를 이용해 graphql 서버를 적용할 차례이다. `server.ts` 파일로 돌아가 아래와 같이 `/graphql` 엔드포인트에 `graphqlHTTP` 서버를 생성해 준다.

```javascript
...
import schema from './schema';
...
app.use(morgan('dev'));
app.use(cors());
app.use(compression());

app.use(
  '/graphql',
  graphqlHTTP({
    schema,
    graphiql: true,
  }),
);

app.listen(port, (): void =>
  console.log(
    `\n🚀      GraphQL is now running on http://localhost:${port}/graphql`,
  ),
);
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