---
layout: single
title:  "CSS - $(window).resize()"
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







# ◆setTimeout()

setTimeout()은 특정시간이 지난 다음에 코드를 실행하는 함수 이다.

```javascript
//문법
setTimeout()
```



```javascript
$(window).on
setTimeout(function(){
    console.log("화면 변화")
})
```

