---
layout: single
title:  "javascript - Cookie vs Session"
categories: javascript 
tag: [javascript ]
author_profile: true
toc: true
toc_label: 목차
toc_icon: "fas fa-list"

---

<br/>











# ◆Cookie

- 쿠키는 **클라이언트 로컬에 저장**되는 키와 값이 들어있는 작은 데이터 파일이다.<br/>
- 사용자 인증 유효시간을 명시할 수 있으면, 유효시간이 정해지면 브라우저가 종료되어도 인증이 유지된다.<br/>
- 사용 예시 : 이전에 본 상품 목록, 쇼핑몰의 장바구니

<br/>

**쿠키 옵션**

```
path : URL path설정하면 해당 경로에 있는 페이지만 쿠키에 접근할 수 있습니다.
domain : 쿠키에 접근 가능한 domain을 지정합니다.
expires : 유효일자를 설정하면 브라우저를 닫아도 쿠키가 삭제되지 않습니다.
max-age : 만료기간을 설정하면 브라우저를 닫아도 쿠키가 삭제되지 않습니다.
secure : HTTPS로 통신하는 경우에만 쿠키가 전송됩니다.
samesite : 크로스 사이트 요청 위조(XSRF) 공격을 막기위해 만들어진 옵션입니다.
```

<br/>





## 1) 쿠키 생성

<a href="https://plugins.jquery.com/cookie/">plugins.jquery.com/cookie/</a> 에서 다운받아 플러그인 해준다.

```javascript
<script type="text/javascript" src="./jquery.cookie.js"></script>
```

**javascript**

```javascript
document.cookie = "name=혜진" // 쿠키 생성
document.cookie = "name=혜진; max-age=2" // 2초 후 만료되는 쿠키 생성
document.cookie = "name=혜진; domain=localhost" // 모든 도메인에서 쿠키 접근 허용
document.cookie = "name=혜진; path=/" // 전체 경로에서 쿠키 사용 가능
```

**jquery**

```javascript
$.cookie('name','혜진') // 쿠키 생성
$.cookie('name','혜진',{expires: 7}) //7일 뒤에 만료되는 쿠키 생성
$.cookie('love','찬호',{expires: 7, path: '/'}) // 전체 사이트에 대해 7일 뒤에 만료되는 쿠키 생성
```

<br/>





## 2) 쿠키 읽기

**javascript**

```javascript
document.cookie; // 모든 쿠키 읽기 => ['name = 혜진']

// 읽기 방법
$(function(){
    var test = getCookie('name');
    console.log("test",test) // 혜진
});
var getCookie = function(name){
    var value = document.cookie.match('(^|;) ?' + name + '=([^;]*)(;|$)');
    return value? value[2] : null;
}
```

**jquery**

```javascript
$.cookie('name') // 혜진
$.cookie('names') // undefined
$.cookie() // 모든 쿠키 읽기 => {'name':'혜진'}
```

<br/>





## 3) 쿠키 삭제하기

**javascript**

```javascript
document.cookie = "name=혜진; max-age=0"; // 생성되자마자 삭제된다.
```

**jquery**

```javascript
$.removeCookie('name') // 삭제되면서 true 반환
$.removeCookie('names') // 삭제 안되면서 false 반환
$.removeCookie('love') // false 반환
$.removeCookie('love','찬호',{expires: 7, path: '/'}) 
// true 반환 => 쿠키 생성 시 path와 expires을 사용 했다면 삭제할 때도 같이 작성 해주어야 한다.
```

<br/>





참고 

- <a href="https://dullyshin.github.io/2019/09/10/WEB-CookiesMakeDel/">dullyshin.github.io</a>

- <a href="https://beop07.tistory.com/56">beop07.tistory.com</a>

  

<br/>







---

# ◆Session

- 세션은 **서버측에서 관리**하기 때문에 보안에 좋지만, 사용자가 많아질수록 서버 메모리를 많이 차지하게 된다.<br/>
- 클라이언트가 request를 보내면, 해당 서버의 엔진이 클라이언트에게 유일한 ID를 부여하는데 이것이 세션ID이다.<br/>
- 사용 예시 : 보안에 중요한 로그인



## 1) 세션 사용

**import javax.servlet.http.HttpSession;** 라이브러리 사용

```java
HttpSession session = request.getSession(); // 생성된 세션이 있으면 true 반환 없으면, false 반환
```

<br/>



## 2) 세션 저장

```java
HttpSession session = request.getSession();

session.setAttribute(이름, 값)
```

<br/>



## 3) 세션 조회

```java
String name = (String)session.getAttribute("name");
```

<br/>



## 4) 세션 삭제

```java
session.removeAttribute("name"); // 세션에서 일부 값 삭제하기
session.invalidate(); // 모든세션 정보 삭제하기
```



<br/>







참고 : <a href="https://hong42.tistory.com/133">hong42.tistory.com</a>











