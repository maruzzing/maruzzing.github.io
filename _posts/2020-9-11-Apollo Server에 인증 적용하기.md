---
layout: post
title: Apollo Server에 인증 적용하기
date: 2020-09-11
comments: true
categories: [Study, graphql]
tags: [Typescript, Apollo Server, GraphQL, Authentication]
excerpt: 이제 서버를 구현할 때 기본이 되면서 보안상으로 아주 중요한 역할을 하는 인증/인가를 구현해 보려고 한다. 기본적으로 Bearer에 jwt 토큰을 적용한 인증 방법을 사용할 것이다.

---

이제 서버를 구현할 때 기본이 되면서 보안상으로 아주 중요한 역할을 하는 인증/인가를 구현해 보려고 한다. 기본적으로 Bearer에 jwt 토큰을 적용한 인증 방법을 사용할 것이다.
<br>
[공식 문서](https://www.apollographql.com/docs/apollo-server/security/authentication/#gatsby-focus-wrapper)에 나와있듯이, context 레벨에서 사용자를 인증하는 방법과, resolver 레벨에서 인증하는 방법이 있는데, 쿼리나 뮤테이션에 따라 인증을 필요로 하지 않는 부분이 있기 때문에 resolver 레벨에서 미들웨어를 만들어 구현해 보려고 한다.

## 라이브러리 설치

jwt 토큰 발행을 위해 `jsonwebtoken`를, 비밀번호 암호화를 위해 `bcrypt`를 설치해 준다.

```bash
$ npm i jsonwebtoken bcrypt
```

## Context 정의

먼저 `src/server.ts` 파일의 `apolloServer`의 생성자에 context 옵션을 추가해 준다. context 내에 정의된 함수는 모든 GraphQL API 콜에서 실행되기 때문에 공통적으로 공유해야 하는 상태 등을 정의해 줄 수 있다.

```typescript
const apolloServer = new ApolloServer({
  schema,
  context: ({ req, res }) => ({ req, res }),
});
```

`src/types/context.ts` 파일을 만들고 우리가 사용할 Context의 타입을 정의해 준다.

```typescript
import { Request, Response } from 'express';

export interface Context {
  req: Request;
  res: Response;
}
```

## 로그인 시 accessToken 발급

로그인 시 accessToken을 발급해 주기 위해 `src/lib/jwt.ts` 파일을 생성하고, jwt 생성, 확인 등의 메서드를 작성한다.<br>
payload로 해당 user의 id를 userId로 넘겨줄 것이다. 서비스의 특성과 활용 방법에 따라 `admin: boolean` 등 다양한 payload를 넣어줄 수 있다. `.env` 파일에 `JWT_ACCESS_TOKEN_SECRET`도 정의해 주고, 유효기간을 설정해 준다.

```typescript
import { sign, verify } from 'jsonwebtoken';

export function generateAccessToken(id: number) {
  return sign({ userId: id }, process.env.JWT_ACCESS_TOKEN_SECRET!, { expiresIn: '15m' });
}

export function verifyAccessToken(token: string) {
  return verify(token, process.env.JWT_ACCESS_TOKEN_SECRET!);
}
```

이제 `src/type/user.ts`에 input/response 타입을 정의해 준다.

```typescript
import { InputType, Field, ObjectType } from 'type-graphql';
import { IsEmail, Length } from 'class-validator';
import User from '../entity/User';

@ObjectType()
export class LoginResponse {
  @Field()
  accessToken: string;
}

@InputType()
export class LoginInput implements Partial<User> {
  @Field()
  @IsEmail()
  email: string;

  @Field()
  @Length(6, 12)
  password: string;
}
```

`src/resolvers/UserResolver.ts`에 `register`과 `login` mutation을 작성해 준다. 

```typescript
@Mutation(() => Boolean)
async register(@Arg('data') data: LoginInput): Promise<Boolean> {
    try{
        const { email, password } = data;
        // 비밀번호 암호화
        const hashedPassword = await bcrypt.hash(password, 12);
        await User.insert({ email, password: hashedPassword });
        return true
    } catch(err) {
        return err;
    }
}

@Mutation(() => LoginResponse)
async login( @Arg('data') data: LoginInput ): Promise<LoginResponse> {
    try {
        const { email, password } = data;
        const user = await User.findOne({ where: { email } });
        if (!user) {
            throw new Error(`The user with email: ${email} does not exist!`);
        }
        // 비밀번호 확인
        const valid = await compare(password, user.password);
        if (!valid) {
            throw new Error(`Password not mached!`);
        }
        // accessToken 발급
        return { accessToken: generateAccessToken(user.id) };
    } catch (err) {
      return err;
    }
}
```

## isAuth 미들웨어 작성

이제 login 시 user는 accessToken을 가지게 되고, API 요청 시 헤더에 Beaer 토큰을 담아서 올 것이다. 이 token을 decode 해 해당 userId의 user 정보가 없다면 에러를, 있다면 user 정보를 context에 담아 다음 처리과정으로 넘겨주는 역할을 하는 isAuth 미들웨어를 작성해 보자.
<br>

`src/middlewares/isAuthMiddleware.ts`파일을 생성하고 아래와 같이 작성한다.

```typescript
import { MiddlewareFn } from 'type-graphql/dist/interfaces/Middleware';
import { Context } from '../types/context';
import { verifyAccessToken } from '../lib/jwt';

/* eslint-disable @typescript-eslint/no-explicit-any */
export const isAuth: MiddlewareFn<Context> = ({ context }, next) => {
  const { authorization } = context.req.headers;
  if (!authorization) {
    throw new Error('not authenticated');
  }
  try {
    const token = authorization.split(' ')[1];
    const payload = verifyAccessToken(token);
    context.payload = payload as any;
  } catch (err) {
    console.log(err);
    throw new Error('not authenticated');
  }
  return next();
};
```

추가적으로 context 타입을 수정해 준다. 

```typescript
// src/types/context.ts
import { Request, Response } from 'express';

export interface Context {
  req: Request;
  res: Response;
  payload?: { userId: number };
}
```

## isAuth 미들웨어 적용

resolver level에서의 미들웨어는 `type-graphql`의 `UseMiddleware`를 사용해 아래의 형식으로 적용할 수 있다. isAuth 미들웨어에서 확인 된 user정보가 context의 payload로 전달되는 것이다. [이전 포스팅](/study/graphql/Apollo-Server에-TypeORM으로-MySQL-DB-연결하고-GraphQL-API-만들기)에서 `getUser`에 argument로 id를 넘겨주는 것으로 구현했었지만 이제 argument 없이 accessToken을 사용할 수 있다.

```typescript
@Query(() => User, { nullable: true })
@UseMiddleware(isAuth)
async getUser(@Ctx() { payload }: Context) {
    try {
        return await User.findOne({ where: { id: payload!.userId } });
    } catch (err) {
        return err;
    }
}
```

## refreshToken 발급

위에서 만들어 준 accessToken의 유효기간을 15분으로 정의해 줬었는데, 유저는 15분이 지나면 다시 로그인을 해야하는 불편함이 생긴다. 이 때 사용할 수 있는 것이 refreshToken인데, accessToken이 만료되었을 때 유효한 refreshToken이 있으면 accessToken을 새로 발급해 주는 것이다. 이 경우 유저는 refreshToken이 유효한 기간동안은 끊김 없이 서비스를 이용할 수 있다.
<br>

refreshToken은 쿠키에 담아 client에 전달해 줄 것이며, refreshToken 또한 만료기간이 다가오면 재발급 해 주어 계속 로그인되어 있을 수 있게 구현해 볼 것이다.

먼저 이전에 작성했던 login 에 refreshToken 발급 로직을 추가해 준다. 이를 위해 `src/lib/jwt.ts` 파일에 `generateRefreshToken`와 `verifyRefreshToken`를 추가해 준다. secret은 accessToken의 secret를 함께 사용해도 되지만 여기선 `JWT_SECRET_REFRESH`를 따로 정의해 주었다. 또한, refreshToken이 유효한 버전인지 확인하기 위해 User entity에 `refreshToken` 컬럼을 추가해 줄 수도 있지만 여기서는 `tokenVersion`을 추가해 사용한다.

```typescript
// src/entity/User.ts
import {
  Entity,
  PrimaryGeneratedColumn,
  Column,
  BaseEntity,
  CreateDateColumn,
  UpdateDateColumn,
  OneToMany,
} from 'typeorm';
import { Field, Int, ObjectType } from 'type-graphql';
import Project from './Project';

@ObjectType()
@Entity()
export default class User extends BaseEntity {
  @Field(() => Int)
  @PrimaryGeneratedColumn()
  id: number;

  @Field()
  @Column({ unique: true })
  email: string;

  @Field()
  @Column()
  password: string;

  @OneToMany(() => Project, (project) => project.user, { eager: true })
  @Field(() => [Project])
  projects: Project[];

  @Column('int', { default: 0 })
  tokenVersion: number;

  @Column('timestampz')
  @CreateDateColumn()
  createdAt: string;

  @Column('timestampz')
  @UpdateDateColumn()
  updatedAt: string;
}

// src/lib/jwt.ts
...
export function generateRefreshToken(id: number, tokenVersion: number) {
  return sign({ userId: id, tokenVersion }, process.env.JWT_SECRET_REFRESH!, {
    expiresIn: '7d',
  });
}

export function verifyRefreshToken(token: string) {
  return verify(token, process.env.JWT_SECRET_REFRESH!);
}
```

이제 위에서 만들어 준 `login`에 refreshToken 발급 로직을 추가해 주면 로그인 시 response body로 accessToken이, cookie 내에 refreshToken이 발급되어 client에 전달된다.

```typescript
...
@Mutation(() => LoginResponse)
async login( 
@Arg('data') data: LoginInput,
@Ctx() { res }: Context,
): Promise<LoginResponse> {
    try {
        const { email, password } = data;
        const user = await User.findOne({ where: { email } });
        if (!user) {
            throw new Error(`The user with email: ${email} does not exist!`);
        }
        // 비밀번호 확인
        const valid = await compare(password, user.password);
        if (!valid) {
            throw new Error(`Password not mached!`);
        }
        // refreshToken 발급
        res.cookie(
          'jid',
          generateRefreshToken(user.id, user.tokenVersion),
          {
            httpOnly: true,
          },
        );
        // accessToken 발급
        return { accessToken: generateAccessToken(user.id) };
    } catch (err) {
      return err;
    }
}
```

## token 재발급

이제 token을 재발급 하는 로직을 추가할 차례이다. refreshToken을 쿠키로 발급했으므로, cookie-parser를 설치한다. 그러면 `req.cookies`로 쿠키를 확인할 수 있다.

```bash
$ npm i cookie-parser
```

`src/server.ts` 파일에 적용해 준다.

```typescript
...
app.use(cookieParser());
...
```

토큰을 리프레시 하는 요청은 GraphQL API로 구현하지 않고 `/refresh-token` 엔드포인트로 분기한다.

```typescript
/// src/server.ts
import { refreshToken } from './refreshToken';
...
app.post('/refresh-token', refreshToken);
```

이제 `/refresh-token`로 들어온 요청을 처리해 줄 차례이다. `refreshToken.ts` 파일을 생성하고, 아래와 같이 accessToken 재발급 로직을 추가한다. 만약 refreshToken의 유효기간이 1day 이내로 남았다면 refreshToken도 재발급 해주는 로직을 추가했다. 

```typescript
import { Request, Response } from 'express';
import {
  verifyRefreshToken,
  generateRefreshToken,
  generateAccessToken,
} from './lib/jwt';
import User from './entity/User';

export async function refreshToken(req: Request, res: Response) {
  const token = req.cookies.jid;
  if (!token) {
    return res.send({ ok: false, accessToken: '' });
  }
  let payload: any = null;
  try {
    payload = verifyRefreshToken(token);
  } catch (err) {
    return res.send({ ok: false, accessToken: '' });
  }
  const { userId, exp, tokenVersion } = payload;

  // user와 tokenVersion 유효성 검사
  const user = await User.findOne({ where: { id: userId } });
  if (!user || user.tokenVersion !== tokenVersion) {
    return res.send({ ok: false, accessToken: '' });
  }

  // refreshToken 재발급
  const afterOneDay = Math.floor(Date.now() / 1000) + 60 * 60 * 24;
  if (exp < afterOneDay) {
    const newTokenVersion = user.tokenVersion + 1;
    res.cookie('jid', generateRefreshToken(user.id, newTokenVersion), {
      httpOnly: true,
    });
    Object.assign(user, { tokenVersion: newTokenVersion });
    await user.save();
  }

  // accessToken 재발급
  return res.send({
    ok: true,
    accessToken: generateAccessToken(userId),
  });
}
```

<br>
<br>
<span class="reference">관련 post</span>

- [Apollo Express GraphQL API 서버 도입하기 (feat.Typescript)](/study/graphql/Apollo-Express-GraphQL-API-서버-도입하기-(feat.Typescript))
- [Apollo Server에 TypeORM으로 MySQL DB 연결하고 GraphQL API 만들기](/study/graphql/Apollo-Server에-TypeORM으로-MySQL-DB-연결하고-GraphQL-API-만들기)
