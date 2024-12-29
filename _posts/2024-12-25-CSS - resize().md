---
layout: single
title:  "CSS - resize, setTimeout"
categories: CSS
tag: [CSS]
author_profile: true
toc: true
toc_label: 목차
toc_icon: "fas fa-list"


---

<br/>





# ◆$(window).resize()

resize() : 브라우저 사이즈가 변경됨에 따라 반응한다.

```javascript
// javascript
window.addEventListener('resize',function(){
    console.log("화면 변화")
})


// jquery
$(window).on('resize', function(){
    console.log("화면 변화")
})
```

<br/>







---

# ◆setTimeout()

setTimeout()은 특정시간이 지난 다음에 코드를 실행하는 함수이다.

```javascript
//문법
setTimeout(함수, 지연시간, 파라미터1, 파라미터2..)
```

<br/>







## 1) 함수 실행





### 1_1) 기명함수 사용

```javascript
// 방법 1.
setTimeout(function(){
    console.log("화면 변화")
})

// 방법 2.
function  delay(){
    console.log("화면 변화")
}
setTimeout(delay, 1000)
```

<br/>







### 1_2) 매개변수 전달

```javascript
function delay(num){
  var sum = 2 + num
  console.log(sum)
}

setTimeout(delay, 1000, 3)
```

<br/>







---

# ◆clearTimeout()

```javascript
var id = setTimeout(function(){
    console.log("화면 변화")
})

clearTimeout(id)
```

함수를 clearTimeout에 부르면 실행취소가 된다.

<br/>





