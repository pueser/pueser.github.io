---
layout: single
title:  "Thymeleaf- grammar"
categories: SpringBoot
tag: [SpringBoot]
author_profile: true
toc: true
toc_label: 목차
toc_icon: "fas fa-list"

---

<br>





# ◆Thymeleaf

타임리프는 컨트롤러가 전달하는 데이터를 이용해 동적으로 화면을 만들어주는 역할을 하는 뷰 템플릿 엔진이다. 타임리프가 갖는 대표적인  특징은 다음과 같다.<br>



- 서버상에서 동작하지 않아도 HTML 파일의 내용을 바로 확인이 가능하다.
- 순수 HTML 구조를 유지한다.

<br/>







---

# ◆Thymeleaf 환경설정



**build.gradle 파일**

```java
implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
implementation 'nz.net.ultraq.thymeleaf:thymeleaf-layout-dialect'
```

<br/>

**maven 파일**

```java
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

<br/>







---

# ◆Thymeleaf 문법



## 1.1 th:each

반복에서 사용할 수 있는 여러 상태 값을 지원한다.<br>`th:each="변수 : 컬렉션"`

```java
<th:each="question : ${questionList}">
<th:text="${question.objcect}">
```

<br/>



## 1.2 th:each ~ loop:

추가한 loop 객체를 이용하여 루프 내에서 다음과 같은 속성을 사용할수 있다.

```java
<th:each="question, loop : ${questionList}">
<th:text="${loop.count}>
```

- **loop.index - 반복 순서, 0부터 1씩 증가**
- **loop.count - 반복 순서, 1부터 1씩 증가**
- **loop.size - 반복 객체의 요소 갯수 (예: questionList의 요소 갯수)**
- **loop.first - 루프의 첫번째 순서인 경우 true**
- **loop.last - 루프의 마지막 순서인 경우 true**
- **loop.odd - 루프의 홀수번째 순서인 경우 true**
- **loop.even - 루프의 짝수번째 순서인 경우 true**
- **loop.current - 현재 대입된 객체 (예: 위의 경우 question과 동일)**

<br/>



## 2. th:text

일반 텍스트를 반환할 때 사용한다.

```java
<th:text="${question.createDate}"></div>
```

<br/>



## 3. th:if

해당 조건이 맞지 않으면 태그 자체를 렌더링하지 않는다.

```java
<th:if="${question != null}">
```

<br/>



## 4. th:href

특정 url로 직접 이동이 가능하다.

```java
<a th:href="@{/question/detail}"></a>
```

자바 객체의 값을 더할 때에는 반드시 `|`로 감싸주어야 한다.

```java
<a th:href="@{|/question/detail/${question.id}|}"></a>
```

<br/>



## 5. \#temporals.format

날짜값을 원하는 형식으로 세팅이 가능하다.

`#temporals.format(날짜, 원하는 형식)`

```java
<th:text="${#temporals.format(question.createDate,'yyyy-MMdd HH:mm')}">
```

<br/>



## 6.1 layout:fragment

th:fragment는 해당 부분을 fragment로 선언한다는 의미.

```java
<!doctype html>
<html lang="ko">

<head>
	<!-- Required meta tags -->
	<meta charset="utf-8">
	<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
	<!-- Bootstrap CSS -->
	<link rel="stylesheet" type="text/css" th:href="@{/bootstrap.min.css}">
	<!-- sbb CSS -->
	<link rel="stylesheet" type="text/css" th:href="@{/style.css}">
	<title>Hello, sbb!</title>
</head>

<body>
	<!-- 기본 템플릿 안에 삽입될 내용 Start -->
	<th:block layout:fragment="content"></th:block>
	<!-- 기본 템플릿 안에 삽입될 내용 End -->
</body>

</html>
```



## 6.2 layout:decorate

fragment의 경로를 적어 주입시켜 반복되는  HTML 코드를 줄일 수 있다.

`layout:decorate="~{fragment 경로}"`

```java
<html layout:decorate="~{layout}">
	<div layout:fragment="content">
</html>
```

프로젝트 폴더/src/main/resources/templates/layout.html에 있어서 layout 작성함

<br/>



## 7. th:replace

현재 태그를 템플릿 조각에 있는 태그로 대체하는 방법

`th:replace="~{ [ 템플릿 조각 경로 ] :: 조각명}"`

**form_errors.html**

```java
<div th:fragment="formErrorsFragment" class="alert alert-danger" role="alert" th:if="${#fields.hasAnyErrors()}">
	<div th:each="err : ${#fields.allErrors()}" th:text="${err}"/>
</div>	
```



```java
<div th:replace="~{form_errors :: formErrorsFragment}"></div>
```

<br/>



## 8. th:object

form submit을 할 때, form의 데이터가 th:object에 설정해준 객체로 받아진다.

```java
<form th:action="@{/question/create}" method="post"	th:object="${questionForm}">
...
</form>
```

QuestionForm 객체가 생성 되어있다.

<br/>



## 9. th:field

HTML 태그의 id, name, value 속성을 자동으로 만들어준다.

```java
<form th:action="@{/question/create}" method="post"	th:object="${questionForm}">
	<input type="text" name="subject" id="subject" th:field="*{subject}" >	
</form>
```

<br/>



## 10. List 유틸리티

```java
<th:text="|${#lists.size(question.answerList)>|">
```

- **#lists.size - 목록의 길이를 반환.** 
- **#lists.isEmpty - 주어진리스트에 요소가없는 경우에 true를 반환.**
- **#lists.contains(myList, 'red') - 주어진리스트의 멤버인지 여부를 확인.**
- **#lists.containsAll(myList, {"red", "green"}) - 여러 요소의 멤버 자격을 확인**
- **#lists.sort - 리스트 정렬**





---







출처 : <a href="https://www.baeldung.com/thymeleaf-lists-utility">https://www.baeldung.com/thymeleaf-lists-utility [List 유틸리티]</a>

       <a href="https://jddng.tistory.com/223">https://jddng.tistory.com/223 [IT 개발자들의 울타리:티스토리] </a>

       <a href="https://memo-the-day.tistory.com/133"> https://memo-the-day.tistory.com/133 [Thymeleaf 문법]</a>
