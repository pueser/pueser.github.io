---
layout: single
title:  "SpringBoot- Validation"
categories: SpringBoot
tag: [SpringBoot]
author_profile: true
toc: true
toc_label: 목차
toc_icon: "fas fa-list"

---

<br>





# ◆Validation

유효성 검사가 필요한 Request 객체에 Validation 어노테이션을 사용해 유효성 검사를 적용할 수 있다.

<br/>





---

# ◆Validation 환경설정

spring boot 2.3 version 이상부터는 아예 모듈로 빠져 validation 의존성을 따로 추가해줘야 사용할 수 있다.

<br/>

**build.gradle**

```java
implementation 'org.springframework.boot:spring-boot-starter-validation'
```

<br/>





---

# ◆Validation  활용 예시



## 1. **question_form.html**

```java
<html layout:decorate="~{layout}">
<div layout:fragment="content">
	<h5 >질문등록</h5>
	<form th:action="@{/question/create}" method="post"	th:object="${questionForm}">
		<div role="alert" th:if="${#fields.hasAnyErrors()}">    
        	<div th:each="err : ${#fields.allErrors()}" th:text="${err}"/>
        </div>  
		<div class="mb-3">
			<label for="subject">제목</label>
			<input type="text" name="subject" id="subject" th:field="*{subject}" >
		</div>
		<div class="mb-3">
			<label for="content">내용</label>
			<textarea name="content" id="content" th:field="*{content}" rows="10"></textarea>
		</div>
		<input type="submit" value="저장하기" >
	</form>
</div>
</html>
```

- **#fields.hasAnyErrors()**가 true라면  검증실패이기 때문에 **#fields.allErrors()**에 오류가 담겨있다.

<br/>







## 2. **유효성 검사 form 예시**

```java
public class QuestionForm {
	@NotEmpty(message="제목은 필수 항목입니다.")
	@Size(max = 200)
	private String subject;
	
	@NotEmpty(message="내용은 필수 항목 입니다.")
	private String content;
}
```

- question_form.html의 입력값과 QuestionForm 클래스의 subject,content가 일치하여 **자동으로 바인딩**이 된다.<br>



**유효성 검사 어노테이션**

|      어노테이션      | 설명                                                         |
| :------------------: | ------------------------------------------------------------ |
|        @Null         | null만 허용한다.                                             |
|       @NotNull       | 빈 문자열(""), 공백(" ")은 허용하되, Null은 허용하지 않음    |
|      @NotEmpty       | 공백(" ")은 허용하되, Null과 빈 문자열("")은 허용하지 않음   |
|      @NotBlank       | null, 빈 문자열(""), 공백(" ") 모두 허용하지 않는다.         |
|        @Email        | 이메일 형식을 검사한다. 단, 빈 문자열("")의 경우엔 통과 시킨다. ( @Pattern을 통한 정규식 검사를 더 많이 사용 |
| @Pattern(regexp = )  | 정규식 검사할 때 사용한다.                                   |
|  @Size(min=, max=)   | 길이를 제한할 때 사용한다.                                   |
|    @Max(value = )    | value 이하의 값만 허용한다.                                  |
|    @Min(value = )    | value 이상의 값만 허용한다.                                  |
|      @Positive       | 값을 양수로 제한한다.                                        |
|   @PositiveOrZero    | 값을 양수와 0만 가능하도록 제한한다.                         |
|      @Negative       | 값을 음수로 제한한다.                                        |
|   @NegativeOrZero    | 값을 음수와 0만 가능하도록 제한한다.                         |
|       @Future        | Now 보다 미래의 날짜, 시간이어야 한다.                       |
|   @FutureOrPresent   | Now 거나 미래의 날짜, 시간이어야 한다.                       |
|        @Past         | Now 보다 과거의 날짜, 시간이어야 한다.                       |
| @PastFutureOrPresent | Now 거나 과거의 날짜, 시간이어야 한다.                       |

<br/>







## 3. **questionController**

```java
@PostMapping("/create")
	public String questionCreate(@Valid QuestionForm questionForm, BindingResult bindingResult) {
		System.out.println("questionCreate post...");
	
		if(bindingResult.hasErrors()) {
			return "question_form";
		}
			
		this.questionService.create(questionForm.getSubject(), questionForm.getContent());
		return "redirect:/question/list";
    }
```

- @Valid를 사용하여 QuestionForm에서 설정한 유효성 검증이 실시된다.

- BindingResult 는 유효성검증이 수행된 결과가 의미하는 객체이므로 **반드시 @Valid 매개변수 뒤에 위치해야한다.**

<br/>





---



출처<br>
<a href="https://dev-coco.tistory.com/123">https://dev-coco.tistory.com/123 [슬기로운 개발생활:티스토리]</a>

