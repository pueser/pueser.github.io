---
layout: single
title:  "Browser - Font Fallback"
categories: Browser
tag: [Browser, Font]
author_profile: true
toc: true
toc_label: 목차
toc_icon: "fas fa-list"


---

<br/>





# ◆브라우저 Font Fallback이란?

Font Fallback이란 **브라우저가 지정된 폰트로 문자를 출력할 수 없을 때, 다른 폰트를 찾아 대신 렌더링하는 과정**을 말한다.

예를 들어 다음과 같이 폰트를 지정했다고 가정해보자.

```css
font-family: "Custom Font", "Noto Sans", sans-serif;
```

브라우저는 단순히 첫 번째 폰트가 있는지만 확인하지 않는다.

다음과 같은 순서로 폰트를 탐색한다.

```
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

즉, **폰트 파일이 존재하더라도 출력하려는 문자의 Glyph가 없으면 다음 폰트를 찾게 된다.**

---
<br/>
<br/>

# ◆브라우저는 웹폰트를 어떻게 사용할까?

웹폰트는 `@font-face`를 통해 등록한다.

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

브라우저는 다음과 같은 순서로 동작한다.

```
CSS 파싱
    ↓
@font-face 검색
    ↓
지원 가능한 폰트 포맷 선택
    ↓
폰트 다운로드
    ↓
폰트 등록
    ↓
Glyph 확인
    ↓
렌더링
```

---
<br/>
<br/>

>## @font-face 주요 속성
>
>### 1. font-family
>
>CSS에서 사용할 폰트 이름이다.
>
>```css
>font-family: "Custom Font";
>```
>
>실제 폰트 내부 이름과 같을 필요는 없으며, 원하는 이름을 사용할 수 있다.
>
>### 2. src
>
>브라우저가 다운로드할 폰트 파일의 위치이다.
>
>```css
>src: url("CustomFont.woff2") format("woff2"),
>     url("CustomFont.ttf") format("truetype");
>```
>
>브라우저는 일반적으로 다음 순서로 지원 여부를 확인한다.
>
>```
>woff2
> ↓
>woff
> ↓
>ttf
>```
>
>### 3. font-weight
>
>해당 폰트가 담당하는 굵기를 의미한다.
>
>```css
>font-weight: 400;
>```
>
>### 4. font-style
>
>폰트의 스타일을 의미한다.
>
>- normal
>- italic
>- oblique
>
>### 5. font-display
>
>웹폰트가 다운로드되는 동안 텍스트를 어떻게 표시할지 결정한다.
>
>```css
>font-display: swap;
>```
>
>`swap`은 먼저 시스템 폰트로 표시한 뒤, 다운로드가 완료되면 웹폰트로 교체하는 방식이다.

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
    font-family: "Custom Font", "Noto Sans", sans-serif;
}
```

처음에는 **`Custom Font`를 사용할 수 없으면 `Noto Sans`가 적용될 것**이라고 생각했다.

하지만 실제로는 같은 환경에서도 서로 다른 폰트가 렌더링되었다.

[기존 페이지]

<img alt="%" src="./images/2026-07-02- Browser - Font Fallback/fallback1.png" />

[현재 페이지]

<img alt="%" src="./images/2026-07-02- Browser - Font Fallback/fallback2.png" />

같은 브라우저, 같은 PC였지만 서로 다른 폰트가 선택되었다.

<br/>

> ## 글꼴 출처(Local file / Network resource)
>
> Chrome DevTools의 **Rendered Fonts**에는 실제 렌더링에 사용된 폰트와 함께 **글꼴 출처(Font origin)** 가 표시된다.
>
> ### Network resource
>
> 웹페이지에서 `@font-face`를 통해 다운로드한 웹폰트가 실제 렌더링에 사용된 경우이다.
>
> ```
> Custom Font
> 글꼴 출처: Network resource
> ```
>
> 즉, 브라우저가 네트워크에서 받은 웹폰트를 사용하고 있다는 의미이다.
>
> ### Local file
>
> 웹폰트가 아닌 운영체제의 폰트를 사용한 경우이다.
>
> 예를 들어 Windows에서는 다음과 같은 시스템 폰트가 선택될 수 있다.
>
> - Yu Gothic
> - Malgun Gothic
> - Meiryo
>
> ```
> Yu Gothic Medium
> 글꼴 출처: Local file
> ```
>
> 이는 브라우저가 운영체제의 시스템 폰트를 사용하여 렌더링했다는 의미이다.

---
<br/>
<br/>

# ◆왜 서로 다른 Font Fallback이 발생했을까?

브라우저는 `font-family`만 보고 폰트를 선택하지 않는다.

다음 요소를 모두 고려하여 최종 폰트를 결정한다.

- `font-family`
- `font-weight`
- `font-style`
- Glyph 존재 여부
- `lang` 속성
- 운영체제의 Font Fallback 정책

특히 `lang` 속성도 폰트 선택에 영향을 줄 수 있다.

예를 들어

```html
<html lang="ja">
```

와 같이 일본어 문서로 인식되면, 현재 폰트가 일본어 Glyph를 지원하지 않을 경우 Windows의 **Yu Gothic**과 같은 일본어 시스템 폰트가 선택될 수 있다.

---
<br/>
<br/>

# 정리

- `font-family`는 우선순위일 뿐, 반드시 해당 폰트로 렌더링되는 것은 아니다.
- 폰트에 현재 문자의 Glyph가 없으면 다음 폰트를 탐색한다.
- 모든 웹폰트가 실패하면 브라우저와 운영체제가 적절한 시스템 폰트를 선택한다.
- `lang` 속성과 운영체제의 Font Fallback 정책에 따라 같은 환경에서도 서로 다른 폰트가 선택될 수 있다.
- Chrome DevTools의 **Rendered Fonts**를 확인하면 실제 어떤 폰트가 렌더링되었는지 확인할 수 있다.
