---
layout: single
title:  "CSS - position"
categories: CSS
tag: [CSS]
author_profile: true
toc: true
toc_label: 목차
toc_icon: "fas fa-list"

---

<br/>







# ◆position 

HTML 문서 상에서 **요소가 배치되는 방식**을 결정한다. 정확한 위치 지정을 위해 top, left, bottom, right 속성과 함께 사용된다.

<br/>








## 1) Position: static

 position 속성이 **static**인 요소는 HTML 문서 상에서 원래 있어야하는 위치에 배치된다.<br/> 따라서 ``top, left, bottom, right 속성값은 position속성이 static일때 무시``된다.

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <link rel="stylesheet" href="study.css">
    </head>
    <body>
        <div class="box1">box1</div>
        <div class="box2">box2</div>
      </body>
</html>
```

```css
.box1 {
  width: 200px;
  height: 100px;
  border: 1px solid;
  background: yellow;
  line-height: 100px;
  text-align: center;

}

.box2 {
  width: 200px;
  height: 100px;
  border: 1px solid;
  background: red;
  line-height: 100px;
  text-align: center;

  position: static; 
  right: 100px;
  left : 200px;
}
```

![Position_static](https://github.com/user-attachments/assets/8216e9ec-25ff-4cd0-a035-28b1e8a820b2)
<br/>








## 2) Position: relative

 position 속성이 **realtive**인 경우 요소를 원래 위치에서 벗어나게 배치할 수 있게 된다. <br/> 원래위치를 기준으로 상대적으로 배치 해준다.<br/>요소의 ``위치지정은 top, botton, left, right 속성을 이용``해서 지정한다.

```css
.box1 {
  width: 200px;
  height: 100px;
  border: 1px solid;
  background: yellow;
  line-height: 100px;
  text-align: center;

}

.box2 {
  width: 200px;
  height: 100px;
  border: 1px solid;
  background: red;
  line-height: 100px;
  text-align: center;

  position: relative; 
  left : 100px;
  bottom: 50px;
}
```
![Position_relative](https://github.com/user-attachments/assets/99868ce7-949d-4e6b-ba03-5edede05ecff)
<br/>








## 3) Position: absolute

position 속성이 **absolute**인 경우 요소는 배치기준을 자신이 아닌 **상위요소**에서 찾는다.<br/>DOM 트리를 따라 올라가서 position 속성이 static이 아닌 첫 번째 상위요소가 해당 요소의 배치 기준으로 설정된다. <br/>



관례적으로 ``부모요소는 position속성을 relative로 지정``해준다.<br/>만약 상위에 position 속성이 static이 아닌 요소가 없다면 ``<body>`` 요소가 배치 기준이 된다.

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <link rel="stylesheet" href="study.css">
    </head>
    <body>
      <div class="box3">box3</div>
      <main>
        <div class="box2">box2</div>
      </main>
      </body>
</html>
```

```css
body{
  margin: 0;
}

main{
  position: relative;
  width: 300px;
  height: 400px;
  background: tomato;

  position: relative;
  top: 25px;
  left : 10px;
}

.box3 {
  width: 200px;
  height: 100px;
  border: 1px solid;
  background: purple;
  line-height: 100px;
  text-align: center;
  z-index: 1;

  position: absolute; 
  top: 50px;
  left : 10px;
}

.box2 {
  width: 200px;
  height: 100px;
  border: 1px solid;
  background: yellow;
  line-height: 100px;
  text-align: center;

  position: absolute; 
  top: 25px;
  left : 10px;
}
```
![Position_absolute](https://github.com/user-attachments/assets/574ed1aa-8a30-4c87-bf08-425f3b87ff92)
<br/>

box3의 사이즈는 body를 기준으로 배치되고 box2의 사이즈는 main을 기준으로 배치 되기 때문에<br/>box2는 body 기준으로 보면 ``25px(top) + 25px(main의 위치) = 50px`` 위치에 나타나게 된다.<br/>
한편, box3는 body 기준으로 직접 `top: 50px;`로 지정되었기 때문에 두 요소가 같은 높이 위치에 겹쳐 보이게 된다.

<br/>








## 4) Position: fixed

position 속성이 **fixed**인 경우 요소의 배치가 자신이나 부모 요소가 아닌 **브라우저 전체화면** 이어서 <br/>top, left, bottom, right 속성은 브라우저의 부터 요소가 얼마나 떨어져 있는지 결정한다.

```css
body{
  margin: 0;
}

main{
  position: relative;
  width: 300px;
  height: 400px;
  background: tomato;
  
  position: relative;
  top: 25px;
  left : 10px;
}

.box3 {
  width: 200px;
  height: 100px;
  border: 1px solid;
  background: purple;
  line-height: 100px;
  text-align: center;
  z-index: 1;

  position: absolute; 
  top: 50px;
  left : 10px;
}

.box2 {
  width: 200px;
  height: 100px;
  border: 1px solid;
  background: yellow;
  line-height: 100px;
  text-align: center;

  position: fixed; 
  top: 25px;
  left : 10px;
}

```
![Position_fixed](https://github.com/user-attachments/assets/3c54fdf3-94dd-4708-9654-b9428a62c9a8)
<br/>








## 5)Position: sticky

position 속성이 **fixed**인 경우 sticky인 요소가 스크롤바를 아래로 내리면 붙어서 움직이지 않는다.

```css
main{
  position: relative;
  width: 300px;
  height: 400px;
  background: tomato;
  height: 150px;
  overflow: auto;
  position: relative;
  top: 25px;
  left : 10px;
}

.box2 {
  width: 200px;
  height: 200px;
  border: 1px solid;
  line-height: 100px;
  background: purple;
  opacity: 0.5;
  text-align: center;

  position: sticky;
  top: 0px;
}

.box1 {
  width: 200px;
  height: 200px;
  border: 1px solid;
  background: yellow;
  line-height: 100px;
  text-align: center;

}

```
<br/>


![Position_sticky](https://github.com/user-attachments/assets/9a36eabd-5a38-4853-ac10-1d1c1865fc4a)





참고 : <a href="https://www.daleseo.com/css-position/">www.daleseo.com</a>
