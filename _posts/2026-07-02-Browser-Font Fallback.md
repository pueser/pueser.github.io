---
layout: single
title: "Browser - Font Fallback"
categories: Browser
tag: [Browser, Font]
author_profile: true
toc: true
toc_label: 목차
toc_icon: "fas fa-list"
---

#  ◆브라우저 Font Fallback이란?

**Font Fallback**이란 브라우저가 지정된 폰트로 문자를 출력할 수 없을 때, 다른 폰트를 찾아 대신 렌더링하는 과정을 의미한다.

예를 들어 다음과 같이 폰트를 지정했다고 가정해보자.

```css
font-family: "Custom Font", "Noto Sans", sans-serif;
```

브라우저는 단순히 첫 번째 폰트가 설치되어 있는지만 확인하는 것이 아니다.

**현재 출력하려는 문자를 표현할 수 있는 Glyph(글리프)가 존재하는지까지 확인한 후** 렌더링할 폰트를 결정한다.

```text
Custom Font
    │
    ├─ 폰트 없음
    ├─ 현재 문자의 Glyph 없음
    ▼
Noto Sans
    │
    ├─ Glyph 존재
    │      ▼
    │   렌더링
    │
    └─ Glyph 없음
           ▼
브라우저 Font Fallback
           ▼
운영체제 기본 글꼴
```

즉, **폰트 파일이 존재하더라도 필요한 Glyph가 없다면 다음 폰트를 탐색한다.**

---

<br/>
<br/>

#  ◆브라우저는 웹폰트를 어떻게 사용할까?

웹폰트는 `@font-face`를 이용하여 등록한다.

```css
@font-face {
    font-family: "Custom Font";
    src: url("CustomFont.woff2") format("woff2"),
         url("CustomFont.ttf") format("truetype");
    font-weight: 400;
    font-style: normal;
    font-display: swap;
}
```

브라우저는 대략 다음과 같은 순서로 웹폰트를 사용한다.

```text
CSS 파싱
    │
    ▼
@font-face 검색
    │
    ▼
지원 가능한 폰트 포맷 선택
    │
    ▼
폰트 다운로드
    │
    ▼
폰트 등록
    │
    ▼
Glyph 확인
    │
    ▼
렌더링
```

---

<br/>
<br/>

## 웹폰트가 준비되기 전에는 무엇을 사용할까?

웹폰트는 다운로드가 완료되어야 사용할 수 있다.

하지만 사용자가 웹폰트 다운로드가 끝날 때까지 빈 화면만 보고 있을 수는 없다.

따라서 브라우저는 **운영체제(OS)에 기본 설치된 시스템 폰트(System Font)** 를 먼저 사용하여 화면을 렌더링한다.

대표적인 시스템 폰트는 다음과 같다.

| 운영체제 | 대표 시스템 폰트 |
|----------|------------------|
| Windows | Segoe UI |
| macOS | San Francisco |
| Android | Roboto |

웹폰트 다운로드가 완료되면 시스템 폰트 대신 웹폰트로 다시 렌더링한다.

```text
HTML/CSS 파싱
      │
      ▼
웹폰트 필요
      │
      ▼
웹폰트 준비 여부 확인
      │
      ├── 준비됨
      │      ▼
      │   웹폰트 렌더링
      │
      └── 아직 준비되지 않음
             │
             ▼
     시스템 폰트 렌더링
             │
             ▼
      웹폰트 다운로드 완료
             │
             ▼
      웹폰트로 다시 렌더링
```

이 과정에서 다음과 같은 현상이 발생할 수 있다.

- **FOUT (Flash of Unstyled Text)** : 시스템 폰트가 먼저 보였다가 웹폰트로 변경되는 현상
- **FOIT (Flash of Invisible Text)** : 웹폰트가 로드될 때까지 텍스트가 보이지 않는 현상

---

<br/>
<br/>

#  ◆@font-face 주요 속성

## font-family

CSS에서 사용할 폰트 이름이다.

```css
font-family: "Custom Font";
```

실제 폰트 내부 이름과 동일할 필요는 없다.

<br/>

## src

브라우저가 다운로드할 폰트 파일의 위치이다.

```css
src: url("CustomFont.woff2") format("woff2"),
     url("CustomFont.ttf") format("truetype");
```

브라우저는 일반적으로 src url 순서대로 지원 여부를 확인한다.

**참고**
[웹폰트 파일형식](https://pueser.github.io/browser/Browser-%EC%9B%B9-%ED%8F%B0%ED%8A%B8-%ED%8C%8C%EC%9D%BC-%ED%98%95%EC%8B%9D/)
<br/>

## font-weight

해당 폰트가 담당하는 굵기이다.

```css
font-weight: 400;
```

<br/>

## font-style

폰트 스타일을 지정한다.

- normal
- italic
- oblique

<br/>-

## font-display

웹폰트가 다운로드되는 동안 텍스트를 어떻게 표시할지 결정한다.

```css
font-display: swap;
```

`swap`은 시스템 폰트를 먼저 보여준 뒤, 웹폰트 다운로드가 완료되면 교체하는 방식이다.

---

<br/>
<br/>

# ◆문제 발생

다음과 같이 웹폰트를 적용하였다.

```css
@font-face {
    font-family: "Custom Font";
    src: url("CustomFont.woff2") format("woff2");
}

.title {
    font-family: "Custom Font", sans-serif;
}
```

처음에는 **`Custom Font`를 사용할 수 없으면 동일한 Font Fallback이 적용될 것**이라고 생각했다.

하지만 실제로는 같은 PC와 같은 브라우저에서도 서로 다른 폰트가 렌더링되었다.
<br/>
### 기존 페이지

<img alt="" src="https://pueser.github.io/images/2026-07-02-browser-font-fallback/fallback1.png"/>

### 현재 페이지

<img alt="" src="https://pueser.github.io/images/2026-07-02-browser-font-fallback/fallback2.png"/>

---

<br/>
<br/>

# ◆Rendered Fonts의 Font Origin

Chrome DevTools의 **Rendered Fonts**에서는 실제 렌더링된 폰트와 함께 **Font Origin**도 확인할 수 있다.

## Network resource

`@font-face`를 통해 다운로드한 웹폰트가 사용된 경우이다.

```text
Custom Font
Font Origin : Network resource
```

즉, 브라우저가 다운로드한 웹폰트를 사용하고 있다는 의미이다.

<br/>

## Local file

운영체제에 설치된 폰트를 사용한 경우이다.

예를 들어 Windows에서는 다음과 같은 폰트가 선택될 수 있다.

- Yu Gothic
- Malgun Gothic
- Meiryo

```text
Yu Gothic Medium
Font Origin : Local file
```

즉, 브라우저가 시스템 폰트를 선택하여 렌더링했다는 의미이다.

---

<br/>
<br/>

# ◆왜 서로 다른 Font Fallback이 발생했을까?

브라우저는 단순히 `font-family`만 보고 폰트를 선택하지 않는다.

다음 요소들을 모두 고려하여 최종 폰트를 결정한다.

- font-family
- font-weight
- font-style
- Glyph 존재 여부
- `lang` 속성
- 운영체제의 Font Fallback 정책

특히 **`lang` 속성**은 매우 중요한 요소이다.

예를 들어

```html
<html lang="ja">
```

처럼 일본어 문서로 인식되면, 현재 폰트가 일본어 Glyph를 지원하지 않을 경우 Windows에서는 **Yu Gothic**과 같은 일본어 시스템 폰트가 선택될 수 있다.

즉,

- 같은 브라우저
- 같은 PC
- 같은 CSS

라도

- 문서의 `lang`
- Glyph 지원 여부
- 운영체제의 Font Fallback 정책

에 따라 서로 다른 폰트가 선택될 수 있다.

---

<br/>
<br/>

# ◆정리

- `font-family`는 폰트의 우선순위를 지정할 뿐이다.
- 폰트 파일이 존재해도 필요한 Glyph가 없으면 다음 폰트를 탐색한다.
- 모든 웹폰트가 실패하면 브라우저와 운영체제가 시스템 폰트를 선택한다.
- `lang` 속성과 운영체제의 Font Fallback 정책에 따라 같은 환경에서도 서로 다른 폰트가 선택될 수 있다.
- Chrome DevTools의 **Rendered Fonts**를 통해 실제 렌더링된 폰트와 Font Origin을 확인할 수 있다.
