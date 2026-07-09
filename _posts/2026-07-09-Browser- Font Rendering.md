---
layout: single
title:  "Browser - Font Rendering"
categories: Browser
tag: [Font, FOUT & FOIT]
author_profile: true
toc: true
toc_label: 목차
toc_icon: "fas fa-list"

---

<br>










# ◆웹 브라우저는 폰트를 어떻게 렌더링할까?

웹 페이지를 처음 접속했을 때 **기본 폰트가 잠깐 보였다가 몇 초 후 웹폰트(Web Font)로 변경되는 현상**을 본 적이 있을 것이다.

왜 이런 현상이 발생하는 걸까?

이를 이해하기 위해서는 브라우저가 웹폰트를 로드하고 렌더링하는 과정을 알아야 한다.

---
<br/>
<br/>

# ◆전체 렌더링 과정

```text
사용자 접속
    │
    ▼
HTML 다운로드
    │
    ▼
CSS 다운로드
    │
    ▼
CSS 파싱
    │
    ▼
@font-face 발견
    │
    ▼
브라우저 캐시(Cache) 확인
    │
    ├── 캐시에 있음
    │       │
    │       ▼
    │   폰트 즉시 사용
    │
    └── 캐시에 없음
            │
            ▼
      폰트 다운로드
            │
            ▼
      폰트 메모리 로드
            │
            ▼
 Unicode → Glyph 매핑
            │
            ▼
 Text Shaping(문자 조합)
            │
            ▼
 Layout 계산
            │
            ▼
 화면(Rendering)
```

---
<br/>
<br/>

## 1. HTML 다운로드

사용자가 웹 사이트에 접속하면 브라우저는 가장 먼저 HTML 문서를 요청한다.

```http
GET /index.html
```

예를 들어 다음과 같은 HTML이 있다고 가정해 보자.

```html
<head>
    <link rel="stylesheet" href="style.css">
</head>

<body>
    <p>Hello World</p>
</body>
```

브라우저는 HTML을 위에서 아래로 읽다가 CSS 파일이 필요하다는 것을 인식한다.

---
<br/>

## 2. CSS 다운로드 및 파싱

브라우저는 CSS 파일을 요청한다.

```http
GET /style.css
```

CSS를 읽는 도중 다음과 같은 코드를 발견한다.

```css
@font-face {
    font-family: "Noto Sans";
    src: url("./fonts/NotoSans-Regular.woff2") format("woff2");
}

body {
    font-family: "Noto Sans";
}
```

이 순간 브라우저는

> "이 페이지를 그리기 위해서는 NotoSans-Regular.woff2 파일이 필요하구나."

라고 판단한다.

---
<br/>

## 3. 브라우저 캐시(Cache) 확인

브라우저는 폰트를 바로 다운로드하지 않는다.

먼저 자신의 캐시에 이미 해당 폰트가 저장되어 있는지 확인한다.

```text
NotoSans-Regular.woff2

        │
        ▼

브라우저 캐시에 존재하는가?

    ├── YES → 바로 사용
    └── NO  → 서버에서 다운로드
```

### 캐시(Cache)란?

캐시는 브라우저가 이전에 다운로드한 리소스를 임시로 저장해 두는 공간이다.

대표적으로 다음과 같은 파일들이 저장된다.

- HTML
- CSS
- JavaScript
- 이미지
- 폰트(Web Font)

즉, 한번 다운로드한 웹폰트는 다음 방문 시 다시 다운로드하지 않고 캐시에 저장된 파일을 바로 사용할 수 있다.

<br/>

**[캐시를 사용한 경우]**
from memory cache

<img alt="%" src="https://pueser.github.io/images/2026-07-09-Browser- Font Rendering/폰트캐시1.png" />

**[캐시를 사용하지 않은 경우]**
from disk cache

<img alt="%" src="https://pueser.github.io/images/2026-07-09-Browser- Font Rendering/폰트캐시2.png" />

---
<br/>

## 4. 폰트 다운로드

캐시에 폰트가 없다면 서버에 요청한다.

```http
GET /fonts/NotoSans-Regular.woff2
```

다운로드가 완료되면 브라우저는 해당 폰트를 메모리에 로드한다.

---
<br/>

## 5. Unicode와 Glyph 매핑

브라우저는 화면에 출력할 문자열을 Unicode 코드 포인트 형태로 가지고 있다.

예를 들어

```text
안녕하세요
```

는 내부적으로

```text
U+C548
U+B155
U+D558
...
```

와 같이 저장되어 있다.

폰트 파일 안에는 각각의 Unicode에 대응하는 Glyph(글리프)가 저장되어 있다.

```text
Unicode
   │
   ▼
U+C548
   │
   ▼
'안' 글자 모양(Glyph)
```

브라우저는 Unicode를 이용하여 해당 글자의 Glyph를 찾아낸다.

---
<br/>

## 6. Text Shaping (문자 조합)

영문은 대부분 문자 하나가 글자 하나를 의미한다.

예를 들어

```text
CAT
```

은

```text
C
A
T
```

각 문자를 그대로 그리면 된다.
<br/>
<br/>
하지만 힌디어, 벵골어, 판자브어, 태국어와 같은 언어는 그렇지 않다.

예를 들어 판자브어

```text
ਕੀ
```

는 실제로는

```text
ਕ
ੀ
```

두 개의 Unicode 문자로 이루어져 있다.

하지만 브라우저는 이를 따로 그리지 않는다.

```text
ਕ + ੀ
      │
      ▼
하나의 글자처럼 조합
```

이 과정을 **Text Shaping**이라고 한다.

Text Shaping 과정에서는 다음과 같은 작업이 함께 수행된다.

- Combining Mark(결합 문자)
- Ligature(합자)
- 문자 위치 조정(Positioning)
- 언어별 OpenType 규칙 적용

이 과정을 거쳐 다양한 언어가 자연스럽게 표시된다.

---
<br/>

## 7. Layout 계산

이제 브라우저는 글자를 어디에 배치할지 계산한다.

예를 들어

```css
font-size:16px;
line-height:24px;
font-weight:700;
letter-spacing:1px;
```

와 같은 스타일을 기반으로

- 글자의 위치
- 줄 간격(Line Height)
- 자간(Letter Spacing)
- 굵기(Font Weight)

등을 계산한다.

---
<br/>

## 8. 화면(Rendering)

마지막으로 브라우저는 계산된 결과를 GPU 또는 CPU를 이용하여 화면에 그린다.

최종적으로 사용자는

```text
Hello

안녕하세요

ਸਤ ਸ੍ਰੀ ਅਕਾਲ
```

과 같은 결과를 보게 된다.

---
<br/>
<br/>

# ◆왜 처음에는 기본 폰트가 보일까?

웹폰트는 다운로드가 완료되어야 사용할 수 있다.

예를 들어

```text
0ms    HTML 다운로드 완료
50ms   CSS 다운로드 완료
80ms   웹폰트 요청
500ms  웹폰트 다운로드 완료
550ms  웹폰트 적용
```

웹폰트가 다운로드되는 동안 브라우저는 임시로 시스템 기본 폰트를 사용한다.

```text
기본 폰트 표시
       │
       ▼
웹폰트 다운로드 완료
       │
       ▼
웹폰트 적용
```

그래서 사용자는

> 처음에는 기본 폰트가 보였다가 잠시 후 웹폰트로 변경되는 현상

을 보게 된다.
<br/>

이 현상을 **FOUT(Flash of Unstyled Text)** 라고 한다.

반대로 웹폰트가 모두 다운로드될 때까지 텍스트 자체를 숨기는 방식을 **FOIT(Flash of Invisible Text)** 라고 한다.

---
<br/>
<br/>

# ◆브라우저 캐시가 중요한 이유

브라우저는 한번 다운로드한 웹폰트를 캐시에 저장한다.

다음 방문 시에는

```text
캐시 확인
    │
    ▼
폰트 존재
    │
    ▼
즉시 사용
```

하므로 다시 다운로드할 필요가 없다.

그 결과

- 페이지 로딩 속도가 빨라진다.
- 네트워크 사용량이 줄어든다.
- 웹폰트가 거의 즉시 적용된다.

---
<br/>
<br/>

# ◆정리

웹폰트는 단순히 글자 모양만 저장한 파일이 아니다.

브라우저는 웹폰트를 다운로드한 뒤 다음 과정을 거쳐 화면을 완성한다.

1. HTML 다운로드
2. CSS 다운로드 및 파싱
3. `@font-face` 발견
4. 브라우저 캐시 확인
5. 필요 시 웹폰트 다운로드
6. Unicode와 Glyph 매핑
7. Text Shaping을 통한 문자 조합
8. Layout 계산
9. 화면 렌더링(Rendering)
