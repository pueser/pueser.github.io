---
layout: single
title: "Supabase Auth - Session, Token, RLS의 관계"
categories: Supabase
tag: [Supabase, Auth, Session, JWT, RLS]
author_profile: true
toc: true
toc_label: 목차
toc_icon: "fas fa-list"
---

<br>
<br>
<br>


| 구분 | Session 방식 | Token 방식 |
|---|---|---|
| **클라이언트가 보통 가지고 있는 것** | Session ID | Token |
| **사용자 정보** | 서버 Session 저장소에 있음 | JWT라면 Token 내부에 있을 수 있음 |
| **인증 확인** | Session ID로 Session 조회 | Token의 서명/내용 검증 |
| **DB 조회** | Session 저장소 조회가 필요 | JWT 인증 자체에는 반드시 필요하지 않음 |
| **사용자 식별** | Session에 연결된 user ID | Token의 `sub` 등 |
| **권한 확인** | Session/DB 정보 활용 | Token 정보 또는 DB 정보 활용 |

---

<br>
<br>
<br>

# ◆ Supabase Auth란?

Supabase에서는 **Supabase Auth**를 이용하여 회원가입, 로그인, 로그아웃 등의 인증 기능을 구현할 수 있다.

로그인 과정에서 가장 중요한 개념은 다음과 같다.

* `auth.users`
* `Session`
* `access_token`
* `refresh_token`
* `auth.uid()`
* RLS(Row Level Security)
* `public.users`

이들은 서로 연결되어 있지만 각각 담당하는 역할은 다르다.

---

<br>
<br>


# ◆ 1. Supabase Auth의 사용자

Supabase에서 사용자가 이메일과 비밀번호로 로그인하면 먼저 **Supabase Auth**가 사용자의 인증 정보를 확인한다.

Supabase Auth에서는 사용자를 `auth.users`에 관리한다.

예를 들어:

```text
auth.users
┌──────────────────────────────────────┐
│ id                                   │
│ dc381699-8ade-4752-8905-7e752a1a2cbc │
│                                      │
│ email                                │
│ admin@test.com                       │
└──────────────────────────────────────┘
```

여기서 `id`는 해당 사용자를 식별하는 **UUID**이다.

이 UUID는 이후 Session, JWT, RLS 등에서 사용된다.

---

<br>
<br>


# ◆ 2. 로그인하면 Session이 발급된다

React에서 Supabase 로그인을 수행할 때는 다음과 같이 사용할 수 있다.

```tsx
const { data, error } =
  await supabase.auth.signInWithPassword({
    email,
    password,
  })
```

로그인이 성공하면 Supabase는 `Session`을 반환한다.

Session은 단순히 하나의 토큰이 아니라 **현재 로그인 상태를 유지하기 위한 인증 정보의 묶음**이다.

```text
Session
├── access_token
├── refresh_token
├── expires_at
├── expires_in
├── token_type
└── user
    └── id
```

---

<br>
<br>


# ◆ 3. Session과 UUID의 관계

Session 자체가 `auth.users`의 UUID인 것은 아니다.

Session 안에 있는:

```tsx
session.user.id
```

가 `auth.users.id`와 동일한 UUID를 가진다.

예를 들어:

```text
auth.users.id
        │
        │ 동일한 UUID
        ▼
session.user.id
```

따라서 로그인한 사용자가:

```text
auth.users.id
= dc381699-8ade-4752-8905-7e752a1a2cbc
```

라면 Session의 `user.id`도 동일하다.

---

<br>
<br>


# ◆ 4. Access Token

Session에서 가장 중요한 값 중 하나가 `access_token`이다.

```text
Session
└── access_token
```

`access_token`은 실제로 Supabase에 요청을 보낼 때 **현재 사용자가 인증된 사용자임을 증명하는 데 사용되는 토큰**이다.

Supabase의 Access Token은 JWT(JSON Web Token) 형식이다.

JWT 내부에는 사용자 식별에 필요한 정보가 포함되어 있으며, 대표적으로 `sub` 값이 있다.

```text
JWT Payload
┌──────────────────────────────────────┐
│ sub: "dc381699-8ade-4752-8905-..."   │
│ aud: "authenticated"                 │
│ role: "authenticated"                │
│ exp: ...                             │
└──────────────────────────────────────┘
```

여기서 `sub`는 해당 Auth 사용자의 UUID를 나타낸다.

즉:

```text
access_token
    ↓
JWT
    ↓
sub
    ↓
auth.users.id
```

로 연결된다.

---

<br>
<br>


# ◆ 5. Access Token의 만료 시간

Session에는 다음과 같은 값이 있다.

```text
expires_in: 3600
```

`3600초`이므로:

```text
3600초
= 60분
= 1시간
```

이다.

즉 현재 Access Token은 **1시간 동안 유효**하다.

JWT 내부에도:

```text
iat = 발급 시간
exp = 만료 시간
```

이 들어있으며,

```text
exp - iat = 3600초
```

가 된다.

---

<br>
<br>


# ◆ 6. Access Token이 만료되면 어떻게 될까?

Access Token이 1시간 후 만료된다고 해서 사용자가 바로 로그아웃되는 것은 아니다.

Session에는 `refresh_token`도 존재한다.

```text
Session
├── access_token
└── refresh_token
```

Access Token이 만료되거나 만료되기 전에 Supabase가 Refresh Token을 사용하여 새로운 Access Token을 발급할 수 있다.

```text
Access Token
     │
     │ 1시간 후 만료
     ▼
Refresh Token 사용
     │
     ▼
새로운 Access Token 발급
```

따라서 사용자는 별도로 로그아웃하지 않는 한 계속 로그인 상태를 유지할 수 있다.

---

<br>
<br>


# ◆ 7. Token이 갱신되면 User도 새로 만들어질까?

그렇지 않다.

Access Token이 갱신되는 것은 **같은 사용자의 새로운 인증 토큰이 발급되는 것**이다.

예를 들어 처음 Session이:

```text
Session
├── access_token = Token A
└── user.id = AAA
```

였다면 Access Token 갱신 후에는:

```text
Session
├── access_token = Token B
└── user.id = AAA
```

가 된다.

즉:

```text
Token A
   ↓ 만료
Token B
   ↓
같은 사용자 AAA
```

이다.

사용자의 UUID가 새로운 값으로 변경되는 것이 아니다.

---

<br>
<br>


# ◆ 8. Session과 User는 다르다

애플리케이션에서는 Session과 사용자 프로필 데이터를 구분하는 것이 중요하다.

## Session

Supabase Auth가 관리하는 인증 상태이다.

```text
Session
├── access_token
├── refresh_token
├── expires_at
└── user
```

## User

Supabase Auth에서 관리하는 사용자 정보이다.

```text
auth.users
├── id
├── email
├── role
├── user_metadata
└── ...
```

그리고 실제 애플리케이션에서 필요한 추가 정보는 별도의 `public.users` 테이블에 저장할 수 있다.

```text
public.users
├── id
├── auth_id
├── name
├── role
└── ...
```

---

<br>
<br>


# ◆ 9. `auth.users`와 `public.users` 연결

일반적으로 애플리케이션에서 사용하는 사용자 프로필 정보를 `public.users`에 저장하고, Supabase Auth 사용자의 UUID와 연결할 수 있다.

예를 들어:

```text
auth.users
┌──────────────────────────────────────┐
│ id                                   │
│ dc381699-8ade-4752-8905-7e752a1a2cbc │
│ email = admin@test.com               │
└──────────────────────────────────────┘
                 │
                 │ auth_id
                 ▼
public.users
┌──────────────────────────────────────┐
│ id                                   │
│ auth_id = dc381699-...               │
│ name = 관리자                        │
│ role = admin                         │
└──────────────────────────────────────┘
```

로그인 성공 후:

```tsx
const profile = await fetchUserProfile(data.user.id)
```

와 같이 Auth 사용자의 UUID를 이용하여 `public.users`의 프로필 정보를 조회할 수 있다.

예를 들어:

```tsx
.eq('auth_id', authId)
```

와 같이 조회한다.

여기서 중요한 점은 `public.users`가 **로그인 여부를 판단하는 테이블은 아니라는 것**이다.

로그인 인증은 Supabase Auth가 담당한다.

---

<br>
<br>


# ◆ 10. RLS(Row Level Security)

RLS는 **사용자가 로그인할 수 있는지를 결정하는 기능이 아니다.**

역할을 정확하게 구분하면 다음과 같다.

```text
Supabase Auth
    ↓
"이메일과 비밀번호가 올바른가?"
    ↓
인증(Authentication)
```

로그인에 성공한 후:

```text
RLS
    ↓
"인증된 사용자가 어떤 데이터를 접근할 수 있는가?"
    ↓
인가(Authorization)
```

즉:

> **Auth는 누구인지 확인하고, RLS는 무엇을 할 수 있는지 결정한다.**

---

<br>
<br>


# ◆ 11. `auth.uid()`란?

RLS 정책에서는 다음과 같은 함수를 사용할 수 있다.

```sql
auth.uid()
```

`auth.uid()`는 **현재 요청을 보낸 인증된 사용자의 UUID**를 반환한다.

예를 들어 로그인한 사용자의 UUID가:

```text
dc381699-8ade-4752-8905-7e752a1a2cbc
```

라면:

```sql
auth.uid()
```

도 해당 UUID를 기준으로 동작한다.

이를 이용하여 자신의 데이터만 조회하도록 RLS 정책을 만들 수 있다.

예:

```sql
auth.uid() = auth_id
```

의 의미는:

> 현재 인증된 사용자의 UUID와 해당 행의 `auth_id`가 같은 경우에만 접근을 허용한다.

---

<br>
<br>


# ◆ 12. 전체 인증 흐름

지금까지의 내용을 하나의 흐름으로 정리하면 다음과 같다.

```text
사용자가 이메일/비밀번호 입력
              ↓
    signInWithPassword()
              ↓
       Supabase Auth
              ↓
      auth.users 확인
              ↓
        인증 성공
              ↓
          Session 발급
              ↓
     ┌────────┴────────┐
     ↓                 ↓
access_token      refresh_token
     ↓
JWT
     ↓
사용자 UUID 확인
     ↓
Supabase DB 요청
     ↓
    auth.uid()
     ↓
      RLS
     ↓
접근 권한 확인
     ↓
허용된 데이터 반환
```

그리고 애플리케이션 프로필 데이터가 필요하다면:

```text
auth.users.id
      │
      │ 동일한 UUID
      ▼
public.users.auth_id
      │
      ▼
프로필 정보 조회
```

가 추가된다.

---

<br>
<br>


# ◆ 13. Session은 브라우저에 저장되어도 괜찮을까?

React SPA에서 Supabase JS Client를 사용하는 경우 Session 정보가 브라우저의 저장소에 보이는 것은 정상적인 동작이다.

개발자 도구의 Local Storage에서 다음과 같은 정보를 볼 수 있다.

```text
Session
├── access_token
├── refresh_token
├── expires_at
├── expires_in
└── user
```

하지만 여기서 중요한 보안 문제가 있다.

`access_token`이나 `refresh_token`이 공격자에게 탈취되면 인증된 사용자처럼 요청을 수행할 수 있기 때문이다.

따라서:

* XSS 방지
* 안전한 프론트엔드 코드 작성
* 적절한 Session 설정
* 반드시 RLS 적용
* 클라이언트에서 전달한 사용자 ID만 믿지 않기

등이 중요하다.

특히 DB 접근 권한은 클라이언트 코드가 아니라 **RLS를 통해 데이터베이스에서 최종적으로 검증해야 한다.**

---

<br>
<br>


# ◆ 14. 핵심 정리

Supabase 인증을 이해할 때 다음 관계를 기억하면 된다.

```text
┌─────────────────────────────┐
│        Supabase Auth        │
│                             │
│       auth.users            │
│            │                │
│            │ id             │
│            ▼                │
│         Session             │
│       ┌───────────┐         │
│       │access_token│        │
│       │refresh_token│       │
│       │user.id     │       │
│       └───────────┘         │
└─────────────┬───────────────┘
              │
              ▼
          auth.uid()
              │
              ▼
             RLS
              │
              ▼
      DB 접근 권한 결정
              │
              ▼
       public.users 등
       애플리케이션 데이터
```

핵심적으로 기억할 것은 다음과 같다.

1. **`auth.users.id`는 Supabase Auth 사용자의 고유 UUID이다.**
2. **Session 자체가 UUID는 아니다.**
3. **Session 안의 `user.id`가 `auth.users.id`와 연결된다.**
4. **`access_token`은 실제 인증에 사용되는 JWT이다.**
5. **현재 Access Token은 `expires_in: 3600`이므로 1시간 동안 유효하다.**
6. **Access Token이 만료되면 Refresh Token을 이용해 새로운 Access Token을 받을 수 있다.**
7. **Token이 갱신되어도 같은 사용자의 UUID는 유지된다.**
8. **Supabase Auth는 인증(Authentication)을 담당한다.**
9. **RLS는 인증된 사용자의 DB 접근 권한(Authorization)을 담당한다.**
10. **`auth.uid()`를 이용하면 현재 인증된 사용자의 UUID를 기준으로 RLS 정책을 만들 수 있다.**
11. **`public.users`는 애플리케이션의 사용자 프로필 정보를 저장하는 용도로 사용할 수 있다.**
12. **브라우저에서 Session 정보가 보일 수 있으므로 XSS와 RLS 등 보안 대책이 중요하다.**

---
