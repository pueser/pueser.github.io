---
layout: single
title:  "Tomcat - web application server(WAS)"
categories: WAS
tag: [Tomcat ]
author_profile: true
toc: true
toc_label: 목차
toc_icon: "fas fa-list"

---



# Tomcat

우리가 만드는 일반적인 자바 코드는 혼자서 웹 브라우저의 요청을 해석하거나응답할 능력이 없는데,, 이때 톰캣이 중간에서 복잡한 일을 대신 해주는 서버 역할을 한다.

### 1. 서블릿 컨테이너(Servlet Container) 역할

자바로 웹 페이지를 동적으로 만들기 위해서는 **Servlet**이라는 기술을 씁니다. 하지만 서블릿은 스스로 실행될 수 없습니다.

### 2. HTTP 요청과 응답의 자동화

웹 브라우저가 "이 페이지 보여줘!"라고 요청하면, 그 데이터는 복잡한 텍스트 형태(HTTP 프로토콜)로 전달됩니다.

### 3. 동적 콘텐츠 생성 (Static vs Dynamic)



## Tomcat 설치방법

### 1) 공식 사이트에서 zip 무료 다운로드

**Apache Tomcat 9 zip** 다운로드((https://tomcat.apache.org/download-90.cgi))

추천: **Core → 64-bit Windows zip**

```
D:\tomcat\apache-tomcat-9.0
```

------

### 2) 압축만 풀기

다운로드한 zip 파일 우클릭 → 압축풀기

예:

```
D:\tomcat\apache-tomcat-9.0.102
```

------

### 3) 실행

폴더 들어가서

```
bin > startup.bat
```

더블클릭하면 바로 실행됨.

브라우저에서:

```
http://localhost:8080
```

들어가서 **Tomcat 기본 페이지 나오면 성공**.





## 이클립스 연결

- **New → Server**
- Apache Tomcat 9
- Tomcat Home 다시 지정

경로 예시:

```
D:\tomcat\apache-tomcat-9.0
```

**server 창이 안보이는경우**

```
Window → Show View → Other… → Server → Servers
```





## 서블릿(Servlet)

서블릿은 일반 자바 클래스와 달리 `HttpServlet` 클래스를 상속받아 구현하며, 다음과 같은 특징이 있습니다.

- **동적 처리:** 사용자의 아이디, 입력값에 따라 매번 다른 결과물을 만듭니다.
- **멀티 쓰레드 지원:** 톰캣이 여러 사용자의 요청을 동시에 처리할 수 있도록 관리해 줍니다.
- **플랫폼 독립성:** 자바 기반이므로 어떤 OS에서든 돌아갑니다.





## 서블릿 vs JSP

- **Servlet:** 자바 코드 안에 HTML 코드가 들어 있는 형태 (로직 처리에 유리)
- **JSP:** HTML 코드 안에 자바 코드가 들어 있는 형태 (화면 구성에 유리)
