---

layout: single
title: "Vite + React SPA - Vite"
categories: Web
tag: [Vite, React, SPA]
author_profile: true
toc: true
toc_label: 목차
toc_icon: "fas fa-list"
---

<br>
<br>

# ◆ Vite란?

Vite는 React와 같은 프론트엔드 프로젝트를 **개발하고 빌드하기 위한 도구(Build Tool)**이다.

쉽게 말하면 개발할 때는 빠르게 프로젝트를 실행하고, 배포할 때는 배포 가능한 파일로 만들어주는 도구라고 생각하면 된다.

Vite는 다음과 같은 기능을 제공한다.

| 기능                         | 설명                       |
| -------------------------- | ------------------------ |
| 개발 서버                      | 로컬에서 프로젝트를 실행            |
| JavaScript / TypeScript 처리 | JS, TS 파일을 처리            |
| JSX / TSX 처리               | React의 JSX, TSX 코드 처리    |
| CSS 처리                     | CSS 파일 처리                |
| React 지원                   | React 프로젝트 개발 환경 구성      |
| 환경변수 처리                    | `.env` 등의 환경변수 사용        |
| 정적 파일 처리                   | 이미지, favicon 등의 정적 파일 처리 |
| Production 빌드              | 배포 가능한 결과물 생성            |
| HMR                        | 코드 수정 내용을 브라우저에 빠르게 반영   |

예를 들어 다음 명령어를 실행한다고 해보자.

```bash
npm run dev
```

그러면 Vite가 **개발 서버(Development Server)**를 실행한다.

일반적으로 다음과 같은 주소에서 프로젝트를 확인할 수 있다.

```text
http://localhost:5173
```

즉,

```text
npm run dev
      ↓
Vite 개발 서버 실행
      ↓
브라우저에서 프로젝트 확인
```

과 같은 구조로 동작한다.

---

<br>
<br>


# ◆ React와 Vite는 같은 것이 아니다

React 프로젝트를 처음 접하면 React와 Vite가 같은 역할을 하는 것처럼 느껴질 수 있다.

하지만 둘은 역할이 다르다.

**React는 UI를 만들기 위한 라이브러리**이고,

**Vite는 프로젝트를 개발하고 빌드하기 위한 도구**이다.

쉽게 표현하면 다음과 같다.

```text
React
  ↓
UI를 만드는 역할

Vite
  ↓
React 프로젝트를 개발하고 빌드하는 역할
```

예를 들어 React에서는 다음과 같이 컴포넌트를 작성한다.

```tsx
function App() {
  return <h1>Hello React</h1>
}
```

위 코드는 React 코드이다.

반면 다음과 같은 코드는 Vite의 설정이다.

```ts
export default defineConfig({
  plugins: [react()],
  server: {
    port: 5173,
  },
})
```

따라서 다음과 같이 이해하면 된다.

| 기술    | 역할                       |
| ----- | ------------------------ |
| React | 애플리케이션의 UI와 컴포넌트를 만드는 기술 |
| Vite  | React 프로젝트를 개발하고 빌드하는 도구 |

---

<br>
<br>


# ◆ Vite 프로젝트의 기본 구조

Vite를 사용하여 React + TypeScript 프로젝트를 구성하면 대략 다음과 같은 구조를 볼 수 있다.

```text
my-project/
├── public/
├── src/
│   ├── App.tsx
│   ├── main.tsx
│   └── ...
├── .env
├── index.html
├── package.json
├── tsconfig.json
├── tsconfig.json
└── vite.config.ts
```

각 파일의 역할을 간단하게 정리하면 다음과 같다.

| 파일               | 역할                         |
| ---------------- | -------------------------- |
| `.env`           | 환경변수 저장                    |
| `vite.config.ts` | Vite 설정                    |
| `index.html`     | 브라우저가 처음 로드하는 HTML         |
| `main.tsx`       | React 애플리케이션을 시작하는 코드      |
| `App.tsx`        | 실제 React UI를 구성하는 최상위 컴포넌트 |

이 중에서 특히 `vite.config.ts`, `index.html`, `main.tsx`의 관계를 이해하면 Vite + React 프로젝트의 전체적인 구조를 이해하기 쉬워진다.

---
<br>
<br>

# ◆ vercel.json이란?

`vercel.json`은 **Vercel에서 프로젝트를 배포할 때 사용할 설정을 정의하는 파일**이다.

`vite.config.ts`가 **Vite의 개발 및 빌드 동작을 설정하는 파일**이라면,

`vercel.json`은 **Vercel에서 배포된 애플리케이션을 어떻게 서비스할지 설정하는 파일**이라고 이해할 수 있다.

예를 들어 Vite + React에서 React Router를 사용하는 SPA를 Vercel에 배포할 경우, 다음과 같이 `rewrite`를 설정할 수 있다.

```json
{
  "$schema": "https://openapi.vercel.sh/vercel.json",
  "rewrites": [
    {
      "source": "/(.*)",
      "destination": "/index.html"
    }
  ]
}
```

이 설정은 사용자가 다음과 같이 직접 URL에 접근했을 때,

```text
/portal/admin/posts
```

Vercel이 해당 경로의 실제 파일을 찾는 대신 `index.html`을 반환하도록 한다.

```text
/portal/admin/posts
        ↓
      Vercel
        ↓
    /index.html
        ↓
      React 실행
        ↓
  React Router가
  /portal/admin/posts 처리
```

Vite로 빌드한 SPA에는 일반적으로 다음과 같이 `index.html`이 존재한다.

```text
dist/
├── index.html
└── assets/
```

하지만 `/portal/admin/posts`와 같은 React Router의 경로가 실제 파일로 존재하는 것은 아니다.

따라서 서버에서 해당 경로를 `index.html`로 연결해주지 않으면, **페이지 내부에서 React Router를 사용하고 있더라도 직접 URL에 접근하거나 새로고침할 때 404가 발생할 수 있다.**

반면 React 애플리케이션이 이미 실행된 상태에서 `NavLink`나 `navigate()`를 이용해 이동하는 경우에는 React Router가 클라이언트에서 라우팅을 처리하기 때문에 이러한 문제가 발생하지 않는다.

즉, `vercel.json`의 `rewrite`는 **React Router 자체를 설정하는 것이 아니라, React Router가 실행되기 전에 서버에서 `index.html`을 전달해주는 역할**을 한다.

> **vite.config.ts → Vite의 개발 및 빌드 설정**
> **vercel.json → Vercel 배포 및 서비스 설정**

---
<br>
<br>


# ◆ vite.config.ts란?

`vite.config.ts`는 Vite의 동작 방식을 설정하는 파일이다.

예를 들어 현재 프로젝트의 `vite.config.ts`가 다음과 같다고 하자.

```ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
  plugins: [react(), tailwindcss()],

  base: '/',

  server: {
    port: 5173,
  },
})
```

## defineConfig()란?

다음 코드를 보자.

```ts
export default defineConfig({
  ...
})
```

`defineConfig()`는 Vite의 설정을 작성하는 부분이다.

쉽게 말하면 다음과 같다.

> Vite야.
> 이 프로젝트에서는 이런 설정으로 동작해.

라는 내용을 작성하는 것이다.

예를 들어 개발 서버의 포트를 설정하거나 사용할 플러그인을 등록할 수 있다.

---

## plugins란?

다음 설정을 보자.

```ts
plugins: [react(), tailwindcss()],
```

`plugins`에는 Vite에서 사용할 플러그인을 등록한다.

현재 프로젝트에서는 다음 두 개의 플러그인을 사용하고 있다.

```text
react()
tailwindcss()
```

구조로 표현하면 다음과 같다.

```text
Vite
 ├── React Plugin
 └── Tailwind CSS Plugin
```

---

## React Plugin이란?

다음 코드를 보자.

```ts
import react from '@vitejs/plugin-react'
```

그리고 `plugins`에 등록한다.

```ts
plugins: [react()]
```

`@vitejs/plugin-react`는 Vite에서 React 프로젝트를 처리하기 위한 플러그인이다.

React에서는 다음과 같은 JSX / TSX 코드를 작성한다.

```tsx
function App() {
  return <h1>Hello React</h1>
}
```

Vite가 이러한 React 코드를 개발 환경에서 정상적으로 처리할 수 있도록 React 플러그인을 사용한다.

---

## Tailwind CSS Plugin이란?

현재 프로젝트에는 다음 코드도 있다.

```ts
import tailwindcss from '@tailwindcss/vite'
```

그리고 다음과 같이 등록한다.

```ts
plugins: [react(), tailwindcss()]
```

이 설정은 Vite에서 Tailwind CSS를 사용할 수 있도록 연결해주는 역할을 한다.

따라서 현재 프로젝트는 다음과 같은 구조로 볼 수 있다.

```text
Vite
   │
   ├── React Plugin
   │
   └── Tailwind CSS Plugin
```

---

## base: '/'란?

다음 설정을 보자.

```ts
base: '/',
```

`base`는 애플리케이션의 **기본 URL 경로(Base Path)**를 설정한다.

예를 들어 사이트가 다음과 같이 도메인의 루트에서 서비스된다면,

```text
https://example.com/
```

다음과 같이 설정할 수 있다.

```ts
base: '/',
```

반대로 애플리케이션이 다음과 같이 `/admin/` 경로 아래에서 서비스된다면,

```text
https://example.com/admin/
```

다음과 같이 설정할 수 있다.

```ts
base: '/admin/',
```

따라서 일반적인 Vercel 배포처럼 도메인의 루트에서 서비스를 제공하는 경우에는 다음과 같이 설정하는 것이 일반적이다.

```ts
base: '/',
```

---

## server.port란?

다음 설정을 보자.

```ts
server: {
  port: 5173,
},
```

이것은 Vite 개발 서버가 사용할 포트 번호를 설정한다.

따라서 다음 명령어를 실행하면,

```bash
npm run dev
```

다음 주소에서 개발 서버에 접속할 수 있다.

```text
http://localhost:5173
```

여기서 중요한 점은 `server.port`가 **개발 환경에서 사용하는 포트**라는 것이다.

즉, Production 서버의 포트를 설정하는 것이 아니다.

또한 Vite의 기본 개발 서버 포트가 일반적으로 `5173`이기 때문에 다음 설정은 생략해도 기본값과 동일하게 동작한다.

```ts
server: {
  port: 5173,
},
```

따라서 현재 프로젝트에서는 **개발 서버의 포트를 명시적으로 5173으로 지정한 것**이라고 볼 수 있다.

---

<br>
<br>


# ◆ 그렇다면 왜 Vite React 프로젝트에는 index.html이 있는가?

현재 프로젝트에서는 Vite가 애플리케이션을 실행하기 위한 **HTML 엔트리 문서(HTML Entry Document)**로 `index.html`을 사용하기 때문이다.

구조를 보면 이해하기 쉽다.

```text
브라우저
   ↓
index.html
   ↓
main.tsx
   ↓
createRoot()
   ↓
App.tsx
   ↓
React 컴포넌트
   ↓
화면 출력
```

따라서 `index.html`은 브라우저가 처음 로드하는 HTML이고,

`main.tsx`는 그 HTML의 `root` 요소에 React 애플리케이션을 연결하여 React 애플리케이션을 실제로 시작시키는 코드라고 보는 것이 정확하다.

---

<br>
<br>


# ◆ index.html과 main.tsx의 차이

두 파일을 비교하면 다음과 같다.

| 파일           | 역할                                |
| ------------ | --------------------------------- |
| `index.html` | 브라우저가 처음 로드하는 HTML 문서             |
| `main.tsx`   | React를 시작하고 root에 렌더링하는 코드        |
| `App.tsx`    | 실제 애플리케이션 UI를 구성하는 최상위 React 컴포넌트 |

쉽게 비유하면 다음과 같다.

```text
index.html
→ 무대

main.tsx
→ 배우를 무대에 올리는 역할

App.tsx
→ 실제 공연
```

---

<br>
<br>


# ◆ React의 실제 시작점은 어디인가?

이 부분은 표현을 정확하게 하는 것이 중요하다.

`index.html`이 브라우저가 처음 로드하는 문서이기는 하지만,

**React 애플리케이션을 실제로 시작시키는 핵심 코드는 `main.tsx`에 있다.**

예를 들어 다음과 같은 코드가 있을 수 있다.

```tsx
createRoot(document.getElementById('root')!).render(
  <App />
)
```

이 코드가 실행되면서 React 애플리케이션이 `index.html`의 `root` 요소에 연결되고 화면을 렌더링하기 시작한다.

따라서 다음과 같이 구분하면 가장 정확하다.

| 구분               | 파일           |
| ---------------- | ------------ |
| HTML 엔트리         | `index.html` |
| React 애플리케이션 엔트리 | `main.tsx`   |
| 최상위 React 컴포넌트   | `App.tsx`    |

즉,

```text
index.html
    ↓
main.tsx
    ↓
<App />
    ↓
React 컴포넌트
    ↓
화면 렌더링
```

과 같은 흐름으로 이해하면 된다.

---

<br>
<br>


# ◆ 다른 React 프로젝트에서는 index.html이 안 보일 수도 있다

React라고 해서 모든 프로젝트가 똑같은 구조를 사용하는 것은 아니다.

예를 들어 **Next.js**와 같은 React 기반 프레임워크에서는 개발자가 직접 `index.html`을 관리하지 않는 경우가 많다.

프레임워크가 HTML 생성 및 렌더링 과정을 추상화하기 때문이다.

따라서 다음과 같이 이해하면 안 된다.

```text
React 프로젝트
→ 반드시 index.html
```

더 정확하게는 다음과 같이 이해해야 한다.

```text
Vite + React SPA
→ index.html을 HTML 엔트리로 사용

React 자체
→ index.html을 반드시 요구하지 않음
```

즉, `index.html`이 존재하는 이유는 **React 자체의 요구사항이라기보다는 Vite가 사용하는 애플리케이션 구조와 관련이 있다.**

---

<br>
<br>


# ◆ 전체 구조 정리

지금까지의 내용을 하나의 흐름으로 정리하면 다음과 같다.

```text
                    Vite
                     │
          ┌──────────┴──────────┐
          │                     │
      개발 환경              Production
          │                     │
      npm run dev           npm run build
          │                     │
          ↓                     ↓
   Vite 개발 서버          빌드 결과물 생성
          │                     │
          ↓                     ↓
   index.html              dist/
          │
          ↓
      main.tsx
          │
          ↓
     createRoot()
          │
          ↓
       App.tsx
          │
          ↓
   React 컴포넌트
          │
          ↓
      화면 출력
```

결국 **Vite + React SPA**에서는 각각의 역할을 다음과 같이 이해하면 된다.

| 구성 요소            | 역할                            |
| ---------------- | ----------------------------- |
| React            | UI와 컴포넌트를 만드는 라이브러리           |
| Vite             | 개발 서버와 Production 빌드를 담당하는 도구 |
| `vite.config.ts` | Vite의 동작 방식 설정                |
| `index.html`     | 브라우저가 처음 로드하는 HTML 엔트리        |
| `main.tsx`       | React 애플리케이션을 시작하는 엔트리        |
| `App.tsx`        | React 애플리케이션의 최상위 컴포넌트        |
| `npm run dev`    | Vite 개발 서버 실행                 |
| `npm run build`  | Production용 파일 생성             |
| `dist/`          | Production 빌드 결과물             |

따라서 Vite + React SPA의 기본적인 실행 흐름은 다음과 같이 정리할 수 있다.

```text
npm run dev
      ↓
Vite 개발 서버
      ↓
index.html
      ↓
main.tsx
      ↓
createRoot()
      ↓
App.tsx
      ↓
React 컴포넌트
      ↓
브라우저 화면
```

그리고 배포할 때는 다음과 같은 흐름으로 동작한다.

```text
npm run build
      ↓
Vite가 프로젝트 빌드
      ↓
dist/
      ↓
HTML / CSS / JavaScript / 정적 파일
      ↓
Vercel 등의 웹 서버에서 서비스
```
