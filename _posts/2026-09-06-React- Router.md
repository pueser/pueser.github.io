---
layout: single
title: "React - Router"
categories: React
tag: [React]
author_profile: true
toc: true
toc_label: 목차
toc_icon: "fas fa-list"
---
<br>
<br>

# ◆ BrowserRouter와 createBrowserRouter

React로 여러 페이지를 구성하다 보면 다음과 같은 URL이 필요해진다.

```
/
/about
/services
/notice
/contact
```

React에서는 실제로 여러 HTML 파일을 만드는 대신, React Router를 이용하여 URL에 따라 다른 컴포넌트를 화면에 보여줄 수 있다.

React Router에서 라우팅을 구성하는 방법 중 자주 사용되는 방식이 바로

- `BrowserRouter`
- `createBrowserRouter`

이다.

둘 다 React 애플리케이션에서 URL에 따라 화면을 전환하기 위해 사용하지만, 라우팅을 구성하는 방식에는 차이가 있다.

---
<br>
<br>

# ◆ 먼저 라우팅(Routing)이란?

라우팅은 쉽게 말하면

> "현재 URL에 어떤 화면을 보여줄 것인가?"

를 결정하는 것이다.

예를 들어 다음과 같은 규칙을 만들 수 있다.

| URL | 화면 |
|---|---|
| `/` | HomePage |
| `/about` | AboutPage |
| `/services` | ServicesPage |
| `/notice` | NoticePage |

사용자가 `/about`으로 이동하면 AboutPage를 보여주고, `/notice`로 이동하면 NoticePage를 보여주는 것이다.

이러한 URL과 컴포넌트의 연결을 관리하는 것이 React Router이다.

---
<br>
<br>

# ◆ BrowserRouter

먼저 BrowserRouter 방식부터 살펴보자.

가장 기본적인 형태는 다음과 같다.

```jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom'

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<HomePage />} />
        <Route path="/about" element={<AboutPage />} />
        <Route path="/services" element={<ServicesPage />} />
      </Routes>
    </BrowserRouter>
  )
}
```

이 코드를 쉽게 해석하면 다음과 같다.

```
BrowserRouter
      ↓
현재 URL 확인
      ↓
Routes
      ↓
URL과 일치하는 Route 찾기
      ↓
해당 컴포넌트 보여주기
```

예를 들어 사용자가

```
/about
```

으로 이동하면

```jsx
<Route path="/about" element={<AboutPage />} />
```

가 선택되어 AboutPage가 화면에 표시된다.

---
<br>
<br>

# ◆ BrowserRouter 방식의 특징

BrowserRouter 방식에서는 라우팅을 주로 JSX 안에서 작성한다.

```jsx
<Routes>
  <Route path="/" element={<HomePage />} />
  <Route path="/about" element={<AboutPage />} />
  <Route path="/services" element={<ServicesPage />} />
</Routes>
```

따라서 구조를 처음 배우는 입장에서는 비교적 직관적이다.

| URL | 컴포넌트 |
|---|---|
| `/` | HomePage |
| `/about` | AboutPage |
| `/services` | ServicesPage |

처럼 바로 확인할 수 있기 때문이다.

---
<br>
<br>

# ◆ createBrowserRouter

createBrowserRouter는 라우팅 설정을 별도의 Router 객체로 만드는 방식이다.

예를 들어 다음과 같이 작성할 수 있다.

```jsx
import { createBrowserRouter } from 'react-router-dom'

export const router = createBrowserRouter([
  {
    path: '/',
    element: <HomePage />,
  },
  {
    path: '/about',
    element: <AboutPage />,
  },
  {
    path: '/services',
    element: <ServicesPage />,
  },
])
```

그리고 만들어진 router를 RouterProvider에 전달한다.

```jsx
import { RouterProvider } from 'react-router-dom'
import { router } from './app/router'

createRoot(document.getElementById('root')!).render(
  <RouterProvider router={router} />
)
```

전체적인 구조는 다음과 같다.

```
createBrowserRouter()
        ↓
라우팅 설정 생성
        ↓
      router
        ↓
RouterProvider
        ↓
React 애플리케이션에 적용
```

---
<br>
<br>

# ◆ BrowserRouter와 createBrowserRouter의 가장 큰 차이

두 방식을 가장 간단하게 비교하면 다음과 같다.

| 구분 | BrowserRouter | createBrowserRouter |
|---|---|---|
| 라우팅 작성 방식 | JSX | Router 객체 |
| 사용 방법 | BrowserRouter + Routes + Route | createBrowserRouter + RouterProvider |
| 라우팅 설정 | 컴포넌트 내부 | 별도의 설정으로 분리 가능 |
| 중첩 라우팅 | 가능 | 가능 |
| 동적 URL | 가능 | 가능 |
| Data Router 기능 | 제한적 | 지원 |
| 복잡한 프로젝트 | 관리 방식에 따라 복잡해질 수 있음 | 라우팅 설정을 분리하기 편함 |
| TypeScript 전용인가? | 아니오 | 아니오 |

여기서 가장 중요한 부분은 마지막이다.

**createBrowserRouter는 TypeScript를 사용하기 때문에 사용하는 것이 아니다.**

JavaScript에서도 사용할 수 있고 TypeScript에서도 사용할 수 있다.

즉,

```
TypeScript
    ↓
프로그래밍 언어 및 타입 시스템

BrowserRouter
createBrowserRouter
    ↓
React Router의 라우팅 구성 방식
```

으로 서로 다른 개념이다.

---
<br>
<br>

# ◆ 두 방식의 구조를 비교해보자

## 1. BrowserRouter 방식

보통 다음과 같은 구조가 된다.

```
main.tsx
    ↓
App.tsx
    ↓
BrowserRouter
    ↓
Routes
    ↓
Route
    ↓
Page
```

예를 들어 App.tsx에서:

```jsx
function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<HomePage />} />
        <Route path="/about" element={<AboutPage />} />
      </Routes>
    </BrowserRouter>
  )
}
```

처럼 작성한다.

## 2. createBrowserRouter 방식

반면 createBrowserRouter를 사용하면 다음과 같이 구성할 수 있다.

```
main.tsx
    ↓
RouterProvider
    ↓
router.tsx
    ↓
createBrowserRouter()
    ↓
Page
```

예를 들어:

**router.tsx**
```jsx
export const router = createBrowserRouter([
  {
    path: '/',
    element: <HomePage />,
  },
  {
    path: '/about',
    element: <AboutPage />,
  },
])
```

**main.tsx**
```jsx
createRoot(document.getElementById('root')!).render(
  <RouterProvider router={router} />
)
```

즉 라우팅 설정을 `router.tsx`라는 별도의 파일로 분리할 수 있다.

---

<br>
<br>

# ◆ createBrowserRouter의 장점

createBrowserRouter의 장점은 단순히 라우팅 설정을 분리할 수 있다는 것만은 아니다.

React Router의 Data Router 기능을 함께 사용할 수 있다는 것이 중요한 차이점이다.

예를 들어 특정 페이지를 보여주기 전에 데이터를 가져오는 `loader`를 라우트에 연결할 수 있다.

```jsx
const router = createBrowserRouter([
  {
    path: '/notice',
    element: <NoticePage />,
    loader: async () => {
      // 게시글 데이터 가져오기
      return getNoticeList()
    },
  },
])
```

이렇게 라우트 자체에 데이터 로딩과 같은 기능을 연결할 수 있다.

또한 `action`을 이용하여 폼 제출 등의 라우트 관련 작업도 구성할 수 있다.

즉 createBrowserRouter는 단순히

```
URL → 컴포넌트
```

만 연결하는 것이 아니라,

```
URL
 ↓
라우트
 ↓
데이터 로딩
 ↓
컴포넌트
```

와 같은 구조로 애플리케이션의 라우팅과 데이터 처리를 함께 관리하기 좋은 방식이다.

---

<br>
<br>

# ◆ 중첩 라우팅도 쉽게 구성할 수 있다

createBrowserRouter에서는 `children`을 이용하여 부모와 자식 라우트를 구성할 수 있다.

예를 들어:

```jsx
const router = createBrowserRouter([
  {
    path: '/',
    element: <PublicLayout />,
    children: [
      {
        index: true,
        element: <HomePage />,
      },
      {
        path: 'about',
        element: <AboutPage />,
      },
      {
        path: 'services',
        element: <ServicesPage />,
      },
    ],
  },
])
```

이 구조는 다음과 같다.

```
/
└── PublicLayout
    ├── /
    │   └── HomePage
    │
    ├── /about
    │   └── AboutPage
    │
    └── /services
        └── ServicesPage
```

여기서 PublicLayout은 공통 레이아웃 역할을 한다.

예를 들어:

```
┌─────────────────────────────┐
│           Header            │
├─────────────────────────────┤
│                              │
│          <Outlet />         │
│                              │
├─────────────────────────────┤
│           Footer            │
└─────────────────────────────┘
```

`<Outlet />` 부분에 현재 URL에 해당하는 자식 페이지가 들어간다.

`/about`이라면:

```
PublicLayout
 ├── Header
 ├── AboutPage
 └── Footer
```

가 되고, `/services`라면:

```
PublicLayout
 ├── Header
 ├── ServicesPage
 └── Footer
```

가 된다.

이것을 **Nested Routing(중첩 라우팅)**이라고 한다.

---
<br>
<br>

# ◆ 실제 프로젝트에서는 어떻게 사용할까?

예를 들어 일반 사용자 페이지와 관리자 페이지가 함께 있는 프로젝트라고 생각해보자.

```
/
├── /about
├── /services
├── /notice
└── /contact

/portal/admin/login
/portal/admin
/portal/admin/hotels
/portal/admin/posts
```

이런 구조라면 createBrowserRouter를 이용하여 라우팅 설정을 한 곳에서 관리할 수 있다.

```jsx
export const router = createBrowserRouter([
  {
    path: '/',
    element: <PublicLayout />,
    children: [
      { index: true, element: <HomePage /> },
      { path: 'about', element: <AboutPage /> },
      { path: 'services', element: <ServicesPage /> },
      { path: 'notice', element: <NoticePage /> },
    ],
  },

  {
    path: '/portal/admin/login',
    element: <LoginPage />,
  },

  {
    path: '/portal/admin',
    element: (
      <ProtectedRoute>
        <DashboardPage />
      </ProtectedRoute>
    ),
  },

  {
    path: '/portal/admin/posts',
    element: (
      <ProtectedRoute>
        <PostManagePage />
      </ProtectedRoute>
    ),
  },
])
```

이렇게 하면 라우팅 구조를 한눈에 확인할 수 있다.

```
Router
│
├── Public
│   └── PublicLayout
│       ├── Home
│       ├── About
│       ├── Services
│       └── Notice
│
├── Admin Login
│   └── LoginPage
│
└── Admin
    └── ProtectedRoute
        ├── Dashboard
        └── Posts
```

특히 관리자 페이지처럼 인증 여부를 확인해야 하는 화면에서는 ProtectedRoute와 조합하여 구조를 명확하게 만들 수 있다.

---
<br>
<br>

# ◆ 그렇다면 어떤 것을 사용해야 할까?

둘 중 하나가 무조건 더 좋은 것은 아니다.

간단한 프로젝트라면 다음처럼 BrowserRouter를 사용하는 것도 충분하다.

```jsx
<BrowserRouter>
  <Routes>
    <Route path="/" element={<HomePage />} />
    <Route path="/about" element={<AboutPage />} />
  </Routes>
</BrowserRouter>
```

반면 다음과 같은 기능이 많아진다면 createBrowserRouter 방식이 편리할 수 있다.

- ✓ 많은 페이지
- ✓ 중첩 라우팅
- ✓ 공통 Layout
- ✓ 관리자 페이지
- ✓ 인증 처리
- ✓ loader
- ✓ action
- ✓ 라우팅 설정을 별도 파일로 관리
