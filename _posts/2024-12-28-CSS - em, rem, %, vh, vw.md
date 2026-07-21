---
layout: single
title:  "CSS - em, rem, %, vh, vw"
categories: CSS
tag: [CSS]
author_profile: true
toc: true
toc_label: 목차
toc_icon: "fas fa-list"


---

<br/>

## CSS 단위 정리

| 단위 | 기준이 되는 대상 | 특징 및 팁 |
|------|------------------|-----------|
| `em` | 부모 요소의 폰트 크기 | 부모-자식 구조가 깊어지면 크기가 중첩되어 커지거나 작아질 수 있음 |
| `rem` | **`<html>` (Root)**의 폰트 크기 | 최상위 기준이라 예측하기 쉽고, 브라우저 확대/축소에도 일관성 있게 동작 |
| `%` | 부모 요소의 크기(너비/높이) | 부모 박스 레이아웃 안에서 상대적인 비율을 지정할 때 사용 |
| `vh` | Viewport Height (화면 높이) | `100vh` = 브라우저 화면 높이의 100% |
| `vw` | Viewport Width (화면 너비) | `100vw` = 브라우저 화면 너비의 100% |

<br/>
<br/>


# ◆ em

**em**은 ``부모요소의 폰트 크기``를 기준으로 비례하여 크기가 조정 된다.

<br/>





## 1) 계산방식

```
0.5em = 16px * 0.5 = 8px
1em = 16px * 1 = 16px
2em = 16px * 2 = 32px
```

<br/>





## 2) 사용 방법

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<link rel="stylesheet" href="change.css" />
</head>
<body>
    <div class="container">
        <div class="title">title</div>
        <div class="subtitle">subtitle
            <p class="subsubtitle">subsubtitle</p>
        </div>
    </div>
</body>
</html>
```

```css
.container{
    font-size: 16px;
}

.container .title{
    font-size: 0.5em;
}

.container .subtitle{
    font-size: 2em;
}

.container .subtitle .subsubtitle{
    font-size: 0.5em;
}
```

title의 글꼴 크기는 **0.5 * 16px = 8px**<br/>subtitle의 글꼴 크기는  **2 * 16px = 32px**<br/>subsubtitile의 글꼴 크기는 **2 * 0.5 * 16px = 16px**

<br/>







---

# ◆ rem

**rem**은 ``<html> (Root)의 폰트 크기``를 기준으로 비례하여 크기가 조정 된다.<br/>따라서 중첩이 이러나더라도 em과 달리 font-size가 달라지지 않는다.

<br/>





## 1) 계산 방식

```
0.5rem = 16px * 0.5 = 8px
1rem = 16px * 1 = 16px
2rem = 16px * 2 = 32px
```

<br/>







## 2) 사용 방법

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<link rel="stylesheet" href="change.css" />
</head>
<body>
    <div class="container">
        <div class="title">title</div>
        <div class="subtitle">subtitle
            <p class="subsubtitle">subsubtitle</p>
        </div>
    </div>
</body>
</html>
```

```css
.container{
    font-size: 16px;
}

.container .title{
    font-size: 0.5rem;
}

.container .subtitle{
    font-size: 2rem;
}

.container .subtitle .subsubtitle{
    font-size: 0.5rem;
}
```

title의 글꼴 크기는 **0.5 * 16px = 8px**<br/>subtitle의 글꼴 크기는  **2 * 16px = 32px**<br/>subsubtitile의 글꼴 크기는 **0.5 * 16px = 8px**

<br/>







---

# ◆ %

%은 ``부모요소의 크기``를 기준으로 비례하여 크기가 조정 된다.

<br/>





## 1) 사용방법

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<link rel="stylesheet" href="change.css" />
</head>
<body>
    <div class="container">
        <div class="box">box</div>
    </div>
</body>
</html>
```

```css
.container{
    background-color: aqua;
    width: 200px;
    height: 500px;
}

.box{
    text-align: center;
    line-height: 15;
    background-color: blueviolet;
    width: 100px;
    height: 50%;
}
```
<img width="158" alt="%" src="https://github.com/user-attachments/assets/77ef2482-64e2-4f67-9eeb-c7d5e2605434" />

<br/>








---

# ◆ vh

**vh**는 Viewport height의 축약이며  ``브라우저 화면 창(Viewport)`` 기준으로 비례하여 크기가 조정 된다.<br>

``1vh = 1%``

<br/>







## 1) 사용방법

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<link rel="stylesheet" href="change.css" />
</head>
<body>
    <div class="container">
        <div class="box">box</div>
    </div>
</body>
</html>
```

```css
body{
    background-color: blanchedalmond;
}

.container{
    background-color: aqua;
    width: 200px;
    height: 500px;
}

.box{
    text-align: center;
    line-height: 15;
    background-color: blueviolet;
    width: 100px;
    height: 50vh;
}
```
<img width="158" height= 300 src="https://github.com/user-attachments/assets/ccc95fa6-01e1-429e-9275-83940b69966b" />

container영역이 아닌 브라우저 화면 크기에 세로영역50%를 차지하게 된다.

<br/>









# ◆ vw

**vw**는 Viewport width의 축약이며  ``브라우저 화면 창(Viewport)`` 기준으로 비례하여 크기가 조정 된다.<br>

``1vw = 1%``

<br/>





## 1) 사용방법

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<link rel="stylesheet" href="change.css" />
</head>
<body>
    <div class="container">
        <div class="box">box</div>
    </div>
</body>
</html>
```

```css
body{
    background-color: blanchedalmond;
}

.box{
    text-align: center;
    line-height: 15;
    background-color: blueviolet;
    height: 300px;
    width: 50vw;
}
```
<img width="200" height= 300 src="https://github.com/user-attachments/assets/8db405f6-d64a-47dc-afc2-428d705c04c6" />

container영역이 아닌 브라우저 화면 크기에 가로영역 50%를 차지하게 된다.
<br/>





참고 : <a href="https://brunch.co.kr/@bommade/25">brunch.co.kr</a>
