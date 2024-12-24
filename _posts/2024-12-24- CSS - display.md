---
layout: single
title:  "CSS - display"
categories: CSS
tag: [CSS]
author_profile: true
toc: true
toc_label: 목차
toc_icon: "fas fa-list"

---

<br>








# ◆display
display 속성은 웹 페이지 내의 요소가 어떻게 보여지는지 요소의 레이아웃 박스 유형을 결정하는 중요한 속성이다.

# 1) display: none
display의 속성이 none인 경우 요소가 화면에 보이지 않는다.
해당 요소와 그 자식 요소들 까지 렌더링 되지 않는다.

# 2) display: block
display의 속성이 block인 경우 전후 줄바꿈이 들어가 혼자 한줄을 차지한다
대표적인 block element는 <div> <p> <h1> 태그들이다.

이때 width, height, margin, padding 속성이 모두 적용된다.

# 3) display: inline
display의 속성이 inline인 경우 한줄에 다른 엘리먼트와 나란히 배친된다.
대표적인 inline element는 <span>, <a>, <em> 태그들이다.

이때, width와 height 속성은 무시되고 margin과 padding속성의 상하간격은 반영되지 않는다.

# 4) display: inline-block
display의 속성이 inline-block 경우 
