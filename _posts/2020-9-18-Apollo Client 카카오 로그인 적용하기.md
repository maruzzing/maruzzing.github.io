---
layout: post
title: Apollo Client 카카오 로그인 적용하기
date: 2020-09-18
comments: true
categories: [Study, nextjs, graphql]
tags: [Next.js, Typescript, Apollo, GraphQL]
excerpt: 이번엔 Apollo Client에 카카오 로그인을 적용해 보겠다. 카카오 로그인 REST API를 사용할 것이며, 전체적인 플로우는 아래와 같다.
---

이번엔 Apollo Client에 카카오 로그인을 적용해 보겠다. 카카오 로그인 REST API를 사용할 것이며, 전체적인 플로우는 아래와 같다.


|  1  | **client** | 카카오로 로그인 버튼을 누르면 카카오 인증 페이지로 이동해 로그인을 진행한다.|
|  2  | **server** | 앱 인증 시 서버의 redirect_uri로 auth code가 전달된다. 이를 client로 전달한다(redirect).|
|  3  | **client** | 전달된 auth code로 사용자 인증을 하고 accessToken을 받아 이를 qraphql mutation으로 서버에 전달한다(로그인/회원가입).|
|  4  | **server** | accessToken을 가지고 카카오로 부터 사용자 정보를 요청해 회원가입/로그인을 진행하고 우리 서버 인증을 위한 accessToken과 refreshToken을 client에 전달한다.|


# 사전 설정

먼저 카카오 [개발자 지원 사이트](https://developers.kakao.com/)에 어플리케이션을 등록하고 앱 키를 발급받는다. 카카오 로그인을 위해서는 REST API 키를 사용할 것이다.

![kakao_login_app_key](/images/kakao_login_app_key.png "kakao_login_app_key")

추가적으로 웹 플랫폼을 등록하고,

![kakao_login_web_platform](/images/kakao_login_web_platform.png "kakao_login_web_platform")

카카오 로그인 기능을 활성화 시키고, redirect uri를 등록한다. 앱 인증에 성공하면 여기에 입력한 redirect uri로 auth code가 전달된다. 추가적으로 동의항목도 설정할 수 있다.

![kakao_login_redirect_url](/images/kakao_login_redirect_url.png "kakao_login_redirect_url")

또한, 보안을 위해 보안 탭에서 Client Secret을 등록해 줄 수 있다.

![kakao_login_client_secret](/images/kakao_login_client_secret.png "kakao_login_client_secret")


# 카카오 로그인 구현

이제 본격적으로 카카오 로그인을 구현해 보자. 먼저 환경변수로 `KAKAO_REST_API_KEY`와 `OAUTH_CALLBAK_URI`를 추가해 준다.

## 인증 코드 받기 (앱 인증)

인증 코드를 받기 위해서는 아래의 형태로 GET 요청을 보내면 된다. [공식문서](https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api#request-code)

```bash
GET /oauth/authorize?client_id={REST_API_KEY}&redirect_uri={REDIRECT_URI}&response_type=code HTTP/1.1
Host: kauth.kakao.com
```
<br>
이는 아래와 같이 적용할 수 있다. `client_id`는 앱 생성 시 발급 받은 REST API 키, `redirect_uri`은 서버의 redirect_uri	, 그리고 `state`는 optional한 파라미터로, redirect될 때 그대로 전달 되기 때문에 프로젝트 구조에 맞춰서 활용하면 된다. 문서를 제대로 안읽어 보고 `state` 존재를 뒤늦게 알아 삽질 한 기억...😭

```typescript
// client
<SutTitle>SNS로 로그인</SutTitle>
<a href={`https://kauth.kakao.com/oauth/authorize?response_type=code&client_id=${process.env.KAKAO_REST_API_KEY}&redirect_uri=${process.env.OAUTH_CALLBAK_URI}&state=login`}>
  <KakaoLogin src="/kakao_login_large_wide.png" />
</a>
```

이제 로그인 버튼을 클릭하면 정보 동의창이 뜨고, 앱 인증이 진행된다. 인증에 성공하면 인증 코드가 아래의 형태로 서버의 redirect_uri로 전달된다. 

```bash
{redirect_uri}?code={authorize_code}&state={state}
```

이 코드를 사용해 유저 인증을 하게 되므로, 이 코드를 query에 담아 client의 원하는 주소로 redirect 해준다.

```typescript
// server
// src/server.ts

async function getSocialAuthCode(req: Request, res: Response) {
  const { code, state } = req.query;
  return res.redirect(`http://localhost:3000/user/${state}?code=${code}`);
}

app.get('/auth/oauth', getSocialAuthCode);
```


## 인증 코드로 accessToken 받기 (사용자 인증)

이제 클라이언트에서도 code를 받았으니 이를 이용해 카카오 accessToken을 받을 차례이다. 공식문서의 [사용자 토큰 받기](https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api#request-token) 부분을 참고한다.

```bash
POST /oauth/token HTTP/1.1
Host: kauth.kakao.com
Content-type: application/x-www-form-urlencoded;charset=utf-8
```

`client_id`와 `redirect_uri`은 이전에 보낸 것과 같고, `code`가 서버로 부터 받은 auth code 이다. `client_secret` 설정을 했다면 이 부분도 추가하면 된다.

<br>

Next.js에서는 `useRouter`를 이용해 `router` 객체에 접근할 수 있다. 서버로부터 리다이렉션 된 페이지에 아래와 같이 작성하여 accessToken을 받아올 수 있다.

```typescript
// client
...
import {useRouter} from 'next/router';
import queryString from 'query-string';
import axios from 'axios';
...

export default function LoginPage() {
  const router = useRouter();
  const code = router.query.code as string;

  useEffect(() => {
    const getKakaoAccessToken = async (code: string) => {
    const formData = {
      grant_type: 'authorization_code',
      client_id: process.env.KAKAO_API_KEY,
      redirect_uri: process.env.OAUTH_CALLBAK_URI,
      code,
    };
    return await await axios.post(
    `https://kauth.kakao.com/oauth/token?${queryString.stringify(formData)}`,
    );
    };
    const getSocialAccessToken = async (code: string) => {
      try {
        const {data} = await getKakaoAccessToken(code);
        const {access_token} = data;
      } catch (e) {
        console.log(e)
      }
    };
    if (code) {
      getSocialAccessToken(code);
    }
  }, [code]);
  ...
```

## 카카오 accessToken으로 로그인 하기 (서비스 서버 사용자 인증)

이제 카카오 accessToken으로 서비스 서버에 사용자 인증을 진행할 차례이다.

먼저, 소셜로그인 mutation을 작성해 주어야 한다. input data로는 `socialAccessToken`과, 'KAKAO', 'NAVER' 등과 같은 `provider`를, response data로 `accessToken`을 가지는 graphql muation이다.

```typescript
// client
// src/graphql/user.ts

...
import {gql} from '@apollo/client';
...

export const LOGIN_WITH_SOCIAL = gql`
  mutation($data: SocialLoginInput!) {
    loginWithSocial(data: $data) {
      accessToken
    }
  }
`;
```

작성한 mutation을 이전에 작성해 둔 LoginPage에 적용한다. response로 받은 `accessToken`은 `localStorage`에 저장해 준다.

```typescript
...
  const [loginWithSocial] = useMutation(LOGIN_WITH_SOCIAL);
...

    const getSocialAccessToken = async (code: string) => {
      try {
        const {data} = await api.getKakaoAccessToken(code);
        const {access_token} = data;
        // 로그인 로직 추가
        const response = await loginWithSocial({
          variables: {
            data: {socialAccessToken: access_token, provider: 'kakao'},
          },
        });
        if (response.data?.loginWithSocial?.accessToken) {
          localStorage.setItem(
            'accessToken',
            response.data.loginWithSocial.accessToken,
          );
          router.push('/');
        }
        //
      } catch (e) {
        console.log(e)
      }
    };
...
```

서버 쪽도 마찬가지로 mutation을 정의한다. 먼저, input과 output 타입을 정의해 주고,

```typescript
// server
// src/types/user.ts
...

@ObjectType()
export class LoginResponse {
  @Field()
  accessToken: string;
}

@InputType()
export class SocialLoginInput implements Partial<User> {
  @Field()
  socialAccessToken: string;

  @Field()
  provider: 'kakao';
}
...
```

resolver에 mutation을 정의해 주는데, `accessToken`으로 [사용자 정보를 조회](https://developers.kakao.com/docs/latest/ko/user-mgmt/rest-api)한 뒤, `id`와 필요 `properties`를 db에 저장하는 식으로 구현했다. 로그인 시에는 `id`가 db에 있는지 확인하고 accessToken을 발급하는데, 비슷하게 register mutation도 추가하면 된다. 

```typescript
// server
// src/resolvers/UserResolver.ts
...
async function getSocialId(socialAccessToken: string): Promise<any> {
  const { data } = await axios.get(`https://kapi.kakao.com/v2/user/me`, {
    headers: { Authorization: `Bearer ${socialAccessToken}` },
  });
  return data;
}
...

  @Mutation(() => LoginResponse)
  async loginWithSocial(
    @Arg('data') data: SocialLoginInput,
    @Ctx() { res }: Context,
  ): Promise<LoginResponse> {
    try {
      const { socialAccessToken } = data;
      // 사용자 정보 가져오기
      const socialData = await getSocialId(socialAccessToken);
      const user = await User.findOne({ where: { socialId: socialData!.id } });
      // user가 없으면 에러 반환
      if (!user) {
        if (!user) {
          throw new Error(
            `The user with socialId: ${socialData!.id} does not exist!`,
          );
        }
      }
      // sending refreshToken
      const newTokenVersion = user.tokenVersion + 1;
      Object.assign(user, { tokenVersion: user.tokenVersion + 1 });
      await user.save();
      res.cookie('jid', generateRefreshToken(user.id, newTokenVersion), {
        httpOnly: true,
      });
      // sending accessToken
      return {
        accessToken: generateAccessToken(user.id),
      };
    } catch (err) {
      return err;
    }
  }
  ...
```

