---
layout: single
title:  "Javascript- 이벤트 버블링"
categories: Javascript
tag: [Javascript]
author_profile: true
toc: true
toc_label: 목차
toc_icon: "fas fa-list"

---

<br>











# ◆이벤트 버블링이란?
이벤트 버블링(Event Bubbling)이란 특정 요소에서 발생한 이벤트가 부모 요소를 거쳐 최상위 요소까지 전달되는 현상을 의미한다.

예를 들어 버튼을 클릭했을 때 해당 버튼의 클릭 이벤트뿐만 아니라 부모 요소, 최종적으로는 `document`, `window`까지 이벤트가 전파될 수 있다.

```html
<div class="container">
    <button class="action-button">클릭</button>
</div>
```

위 구조에서 버튼을 클릭하면 아래와 같은 순서로 이벤트가 전달된다.

```
button
  ↓
div.container
  ↓
document
  ↓
window
```

이러한 동작은 기본적인 브라우저 이벤트 흐름이며, 상황에 따라 유용하게 사용할 수 있지만 의도하지 않은 이벤트 실행의 원인이 되기도 한다.

---



## 문제 상황
검색 페이지에서 검색 결과를 클릭하면 다른 페이지로 이동하는 구조가 있었다.

페이지 이동 시 검색어 데이터를 유지하기 위해 클릭 이벤트에서 쿠키를 저장한다.

```javascript
$("검색클릭").click(function () {
    $.cookie("value", 검색어 데이터, { path: '/' });
});
```

그런데 페이지 이동 직후 쿠키가 삭제되는 문제가 발생했다.

처음에는 쿠키 저장 로직이나 페이지 이동 과정의 문제라고 생각했지만 실제 원인은 다른 곳에 있었다.

---

## 원인 분석

기존 코드에는 화면 외부 영역 클릭 시 검색 상태를 초기화하기 위해 `document.onclick` 이벤트가 등록되어 있었다.

```javascript
document.onclick = function () {
    document.cookie = 
        'value=; Path=/; Expires=Thu, 01 Jan 1970 00:00:01 GMT;';
};
```

문제는 다른 코드에서 아래와 같이 강제로 클릭 이벤트를 발생시키고 있었다.

```javascript
$(".layer").trigger("click");
```

처음에는 단순히 해당 요소의 클릭 이벤트만 실행될 것이라고 생각했지만, jQuery의 `.trigger()`는 실제 사용자 클릭과 유사한 이벤트 흐름을 생성한다.

즉,

```
trigger("click")
        ↓
.layer click 이벤트 실행
        ↓
이벤트 버블링 발생
        ↓
document.onclick 실행
        ↓
검색 쿠키 삭제
```

와 같은 흐름이 만들어졌다.

결과적으로 페이지 이동 과정에서 실행된 `.trigger("click")`이 전역 클릭 이벤트까지 전달되면서 저장된 검색 쿠키가 삭제되는 문제가 발생했다.

---

## 해결 방법

### 1. 단순히 해당 요소의 이벤트만 실행해야 한다면 `.triggerHandler()` 사용

`.trigger()`는 이벤트 전파 과정까지 재현한다.

반면 `.triggerHandler()`는 선택된 요소의 이벤트 핸들러만 실행하며 이벤트 버블링을 발생시키지 않는다.

변경 전:

```javascript
$(".layer").trigger("click");
```

변경 후:

```javascript
$(".layer").triggerHandler("click");
```

이렇게 하면 상위 요소의 클릭 이벤트까지 전달되지 않는다.

```text
.layer click 실행
        ↓
종료
```

`document.onclick` 같은 전역 이벤트가 존재하는 환경에서는 `.triggerHandler()`가 더 안전한 선택이 될 수 있다.

---

### 2. 이벤트가 아닌 기능 자체를 재사용해야 한다면 공통 함수로 분리

만약 클릭 이벤트를 발생시키려는 목적이 아니라 "클릭했을 때 실행되는 동작"만 필요하다면 `.trigger()` 사용보다 함수 분리가 적절하다.

변경 전:

```javascript
$(".layer").trigger("click");

$(".layer").click(function () {
    closeLayer();
});
```

변경 후:

```javascript
function closeLayer() {
    console.log("기능 수행하기");
}


$(".layer").click(function () {
    closeLayer();
});
```

필요한 곳에서는 이벤트를 재실행하는 것이 아니라 기능 자체를 호출한다.

```javascript
closeLayer();
```

이 방식은 이벤트 흐름에 의존하지 않기 때문에 유지보수 측면에서도 더 안정적이다.

---

## `.trigger()`와 함수 분리의 차이

### `.trigger("click")`

실제 클릭 이벤트와 유사한 흐름을 만든다.

따라서 다음 동작들이 함께 발생할 수 있다.

- 이벤트 버블링
- 상위 요소 이벤트 실행
- 기본 이벤트 동작
- 다른 이벤트 핸들러 실행

즉, "사용자가 실제로 클릭한 것처럼 동작시키고 싶을 때" 사용한다.

---

### 함수 호출 방식

필요한 로직만 실행한다.

```javascript
closeLayer();
```

이 방식은 이벤트 전파와 관계없이 원하는 기능만 수행한다.

즉,

- UI 상태 변경
- 모달 닫기
- 레이어 제거
- 데이터 초기화

같은 동작을 재사용할 때 적합하다.

---

### 추가로 사용할 수 있는 방법

#### event.stopPropagation()

이벤트 객체를 받아 버블링을 중단할 수도 있다.

```javascript
$(".button").on("click", function(event) {
    event.stopPropagation();

    console.log("버튼 클릭");
});
```

위 코드에서는 버튼 클릭 이벤트가 부모 요소로 전달되지 않는다.

---

#### return false (jQuery)

jQuery에서는 `return false`를 사용해 아래 두 가지 동작을 동시에 수행할 수 있다.

- `event.stopPropagation()`
- `event.preventDefault()`

```javascript
$(".button").click(function() {

    console.log("버튼 클릭");

    return false;
});
```

다만 두 동작이 모두 필요한 상황인지 확인하고 사용하는 것이 좋다.

---

# 정리

`trigger("click")`은 단순히 이벤트 핸들러를 호출하는 것이 아니라 실제 클릭 이벤트 흐름을 생성한다.

따라서 `document.onclick`, `window.onclick` 같은 전역 이벤트가 존재하는 프로젝트에서는 예상하지 못한 사이드 이펙트를 만들 수 있다.

사용 목적에 따라 선택해야 한다.

| 상황 | 사용 방법 |
| --- | --- |
| 실제 클릭과 동일한 이벤트 흐름이 필요하다 | `.trigger("click")` |
| 해당 요소의 이벤트만 실행하고 싶다 | `.triggerHandler("click")` |
| 클릭으로 실행되는 기능만 재사용하고 싶다 | 공통 함수 분리 |

앞으로 이벤트를 강제로 발생시키기 전에 "정말 클릭 이벤트가 필요한 것인지, 아니면 기능 실행만 필요한 것인지"를 먼저 판단하는 것이 중요하다.
