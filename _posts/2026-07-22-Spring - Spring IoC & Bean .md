---
layout: single
title:  "Spring - Spring IoC & Bean"
categories: CSS
tag: [Spring, Bean]
author_profile: true
toc: true
toc_label: 목차
toc_icon: "fas fa-list"

---

<br>



# ◆ Spring IoC(제어의 역전)와 Bean

Spring을 처음 공부하면 가장 많이 듣는 용어가 있다.

- IoC(Inversion of Control)
- DI(Dependency Injection)
- Bean
- Spring Container
- `@Autowired`

---

<br>
<br>

## 객체를 직접 생성하는 일반 Java

일반 Java에서는 `new`를 사용하여 필요한 객체를 개발자가 직접 생성한다.

예를 들어 A 클래스와 B 클래스에서 각각 `new MemberService()`를 호출하면, 서로 다른 객체가 생성된다.


즉, 객체의 생성과 관리를 모두 개발자가 직접 담당한다.

<br>

## Spring에서의 객체 생성

Spring에서는 개발자가 `new`를 사용하여 객체를 직접 생성하지 않는다.

대신 `@Component`, `@Service`, `@Repository` 등의 어노테이션이 붙은 클래스를 Spring이 실행될 때 찾아 객체를 자동으로 생성한다.


즉, 객체의 생성과 관리의 제어권이 개발자가 아닌 **Spring**으로 넘어간다.

이러한 개념을 **IoC(Inversion of Control, 제어의 역전)** 이라고 한다.

---

<br>
<br>

# ◆ Spring Container란?
Spring은 생성한 객체를 **Spring Container**라는 공간에 보관하고 관리한다.
쉽게 말하면 **객체를 관리하는 창고**라고 생각하면 된다.


```text
Spring Container

┌────────────────────┐
│ MemberService 객체 │
└────────────────────┘
```
Spring은 실행되면서 필요한 객체를 생성한 뒤 이 창고에 보관한다.

---
<br>
<br>

# ◆ Bean이란?

Spring Container에 등록되어 관리되는 객체를 **Bean**이라고 한다.


즉,

- Spring이 객체를 생성하고
- Spring Container에 저장하여
- 필요할 때마다 사용할 수 있도록 관리하는 객체


를 Bean이라고 한다.

따라서 **Bean 등록**이란 Spring이 객체를 생성하여 Spring Container에 저장하고 관리하는 것을 의미한다.

---

<br>
<br>

# ◆ @Autowired
Bean으로 등록된 객체는 필요한 곳에서 사용할 수 있다.

Spring에서는 `@Autowired`를 사용하여 Spring Container에 등록된 Bean을 자동으로 가져와 사용할 수 있다.

예를 들어

```java
@RestController
public class MemberController {

    @Autowired
    private MemberService memberService;

}
```

를 작성하면

Spring은

```text
MemberService가 필요하네.

↓

Spring Container에서 찾아본다.

↓

Bean이 있네.

↓

MemberController에 넣어준다.
```

즉,

```text
Spring Container

┌────────────────────┐
│ MemberService Bean │
└────────────────────┘
        ▲
        │
        │
MemberController
```

처럼 자동으로 연결해 준다.

이 과정을

> **DI(Dependency Injection, 의존성 주입)**

이라고 한다.

---

<br>
<br>

# ◆ @Autowired를 사용하면 같은 객체를 사용할까?

Spring Bean은 기본적으로 **Singleton**으로 생성된다.
즉, 하나의 객체를 생성한 뒤 필요한 클래스들이 함께 사용한다.

예를 들어

```java
@Service
public class MemberService {

}
```

가 있다면

A와 B가

```java
@Autowired
MemberService memberService;
```

를 사용해도

```text
A
  \
   \
    MemberService
   /
  /
B
```

처럼 **하나의 객체를 함께 사용된다.**


<br>

## 그렇다면 하나의 객체를 여러 클래스에서 사용하면 데이터가 서로 섞이지 않을까?

만약 Service가 다음과 같이 멤버 변수에 데이터를 저장한다면 문제가 발생할 수 있다.

예를 들어

```java
@Service
public class Calculator {

    private int result = 0;

    public void add(int n) {
        result += n;
    }

}
```

A가

```java
calculator.add(10);
```

을 호출하면

```text
result = 10
```

이 됩니다.

그런데

B가

```java
calculator.add(20);
```

을 호출하면

```text
result = 30
```

이 된다.

왜냐하면

**같은 객체를 공유하기 때문이다.**

<br>

그래서 Spring에서는 Service를 **Stateless(상태가 없는 객체)** 로 작성하는 것을 권장한다.

즉, 객체 내부에 데이터를 저장하지 않고 필요한 데이터는 메서드의 매개변수로 전달하여 처리한다.

```java
@Service
public class Calculator {

    public int add(int a, int b) {

        return a + b;

    }

}
```

객체 안에는

```text
result
price
member
```

같은 데이터를 저장하지 않는다.

필요한 데이터는

```java
calculator.add(10, 20);
```

처럼 **메서드의 매개변수(Parameter)** 로 전달한다.

메서드가 종료되면 전달받은 데이터도 함께 사라진다.

이러한 객체를

> **Stateless(상태가 없는 객체)**

라고 한다.

---

<br>
<br>

# ◆ Stateless가 중요한 이유

Spring에서는 하나의 Service 객체를 여러 사용자가 함께 사용한다.

하지만 Service 내부에 데이터를 저장하지 않기 때문에

```text
사용자 A
      │
      ▼
MemberService
      ▲
      │
사용자 B
```

처럼 하나의 객체를 공유해도

각 요청의 데이터는 서로 영향을 주지 않는다.


