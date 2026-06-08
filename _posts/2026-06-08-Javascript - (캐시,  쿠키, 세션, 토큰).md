---
layout: single
title:  "캐시,  쿠키, 세션, 토큰"
categories: Javascript 
tag: [인증]
author_profile: true
toc: true
toc_label: 목차
toc_icon: "fas fa-list"

---




# 캐시 / 쿠키 / 세션 / 토큰 정리 (jQuery + Spring 기준)

---

## 1. 캐시 (Cache)

### 캐시란?

캐시는 자주 사용하는 데이터를 임시 저장하여 성능을 향상시키는 기술이다.

> 쉽게 말하면 "이미 가져온 데이터를 또 요청하지 말고 저장해 두자"

### 예시

- 이미지
- 게시글 목록
- 상품 목록

### 저장 위치

- 브라우저 캐시
- 서버 메모리

### 특징

- 데이터 재사용으로 속도 향상
- DB 요청 감소
- 성능 최적화 목적

---

## 2. 쿠키 (Cookie)

### 쿠키란?

쿠키는 브라우저에 저장되는 작은 데이터이다.  
서버가 브라우저에게 "이 데이터를 저장해 둬"라고 전달하면 브라우저가 저장한다.

### 예시

- 아이디 저장
- 자동 로그인 정보 일부

### 저장 위치

사용자 브라우저

### jQuery 쿠키 사용

**1) 쿠키 설정**

```javascript
$.cookie("username", "kim", { expires: 1, path: "/" });
```

**2) 쿠키 읽기**

```javascript
var name = $.cookie("username");
console.log(name);
```

**3) 쿠키 삭제**

```javascript
$.removeCookie("username", { path: "/" });
```

---

## 3. 세션 (Session)

### 세션이란?

세션은 서버가 사용자의 상태를 저장하는 방식이다.  
쿠키와 가장 큰 차이점은 **저장 위치**이다.

### 저장 위치

서버

### 동작 방식

1. 사용자 로그인
2. 서버가 세션 생성
3. `sessionId` 발급
4. 브라우저에 `sessionId` 쿠키 저장

### 구조

```
서버
 └─ Session 생성
      └─ sessionId = ABC123

브라우저
 └─ sessionId=ABC123
```

### 요청 흐름

브라우저는 요청마다 `sessionId=ABC123`을 서버로 전달하고,  
서버는 이를 통해 사용자 정보를 조회한다.

### Spring Boot 예제

**로그인 (세션 저장)**

```java
@PostMapping("/login")
public ResponseEntity<String> login(HttpSession session) {
    session.setAttribute("userId", "kim123");
    return ResponseEntity.ok("로그인 성공");
}
```

**세션 조회**

```java
@GetMapping("/me")
public ResponseEntity<String> me(HttpSession session) {
    String userId = (String) session.getAttribute("userId");
    return ResponseEntity.ok("현재 로그인 유저 = " + userId);
}
```

**세션 삭제 (로그아웃)**

```java
@PostMapping("/logout")
public ResponseEntity<String> logout(HttpSession session) {
    session.invalidate();
    return ResponseEntity.ok("로그아웃 완료");
}
```

---

## 4. 토큰 (Token)

### 토큰이란?

토큰은 사용자 인증 정보를 문자열 형태로 만든 것이다.  
최근에는 **JWT(JSON Web Token)** 를 많이 사용한다.

### 저장 위치

클라이언트

### 사용 예시

- React + Spring Boot
- 모바일 앱
- REST API 서버

### 특징

- 서버는 상태를 저장하지 않음 (**Stateless**)
- 클라이언트가 토큰을 들고 다님

### jQuery 토큰 사용

**1) 토큰 포함 요청**

```javascript
$.ajax({
    url: "/me",
    type: "GET",
    beforeSend: function(xhr) {
        xhr.setRequestHeader(
            "Authorization",
            "Bearer " + localStorage.getItem("token")
        );
    },
    success: function(res) {
        console.log(res);
    }
});
```

**2) 로그아웃**

```javascript
localStorage.removeItem("token");
```

### JWT 방식 흐름

1. 로그인 성공
2. 서버 → JWT 생성
3. 클라이언트 저장
4. 요청 시 JWT 전달
5. 서버는 검증만 수행 (저장하지 않음)

> **핵심: 서버는 상태를 기억하지 않는다**

---

## 4_1. 클라이언트 저장 위치

> 클라이언트 = 브라우저 / 앱

### 1) localStorage

```javascript
localStorage.setItem("token", jwt);
```

- 브라우저 종료 후에도 유지
- 가장 많이 사용
- XSS 공격에 취약

### 2) sessionStorage

```javascript
sessionStorage.setItem("token", jwt);
```

- 탭 종료 시 삭제
- 임시 로그인 용도

### 3) Cookie (HttpOnly)

```
Set-Cookie: token=JWT; HttpOnly
```

- JavaScript 접근 불가
- 보안적으로 더 안전

---

## 4_2. 세션 vs 토큰 비교

| 구분 | 세션 | 토큰 |
|------|------|------|
| 저장 위치 | 서버 | 클라이언트 |
| 서버 메모리 | 사용 | 거의 사용 안 함 |
| 확장성 | 낮음 | 높음 |
| 인증 방식 | Session ID 조회 | Token 검증 |
| 대표 사용처 | 전통 웹 | REST API, 모바일 |

---

## 핵심 요약

| 개념 | 설명 |
|------|------|
| 캐시 | 성능 최적화 |
| 쿠키 | 브라우저 저장 데이터 |
| 세션 | 서버 기반 로그인 관리 |
| 토큰 | 클라이언트 기반 인증 (JWT) |
