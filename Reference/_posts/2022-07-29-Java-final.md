---
layout: post
title: Spring Property
description: >
 게시판 만들기 프로젝트를 진행하며 자세히 알지 못하는 부분에 대한 공부 중 Spring property에 대한 공부 내용
sitemap: true
hide_last_modified: false
---

1. 개요
{:toc}

# 프로젝트 관리
[프로젝트 관리 문서](https://docs.google.com/spreadsheets/d/1xxuP3eXVIsYP-Pe4pwDcvYthXhtYNUvVXXgRPU3XWqw/edit?usp=sharing)

# final

> 자바 언어에서 final은 오직 한 번만 할당할 수 있는 entity를 정의할 때 사용된다. final로 선언된 변수가 할당되면
>  항상 같은 값을 가진다. 만약 final 변수가 객체를 참조하고 있다면, 그 객채의 상태가 바뀌어도 final 변수는 매번 동일한 
> 내용을 참조한다.

## 사용될 수 있는 곳

- Class
```java
public final class FinalClass{}
```

클래스에도 사용할 수 있지만 final이 적용된 클래스는 상속을 하지 못합니다.
- Method
```java
public class FinalMethodClass{
    public final void nonOverrideMethod(){}
}
public class children extends FinalMethodClass {
    public void nonOverrideMethod(){} // 오버라이드 불가능
}
```
메소드는 오버라이드를 하지 못한다고 하네요.

- Variable

```java
public class FinalVariableClass{
    private fianl int finalVariable;
}
```
final 변수는 한 번 값을 할당하면 수정할 수 없다고 하네요. 그러니 초기화 값은 필수적 입니다.
하지만 객체안의 변수라면 생성자와 static을 통한 초기화 까지는 허용합니다.

그렇다고 무조건적으로 수정이 불가능 한것은 아닙니다. 수정할 수 없는 범위는 그 변수의 값에 한정됩니다. 
다른 객체를 참조하거나 참조 할때에는 객체의 내부 값을 변경할 수 있습니다.

```java
class TestClass{
   private String a;
   private int b;
   
   //get,set,생성자 Method
}

// main
final TestClass test = new TestClass("test",0); // O
test.setA("testzz");
test.setB(5);
System.print.out(""+test.getA); // testzz 출력
test = new TestClass("test2",1); // 컴파일 에러
```

변수 타입에는 기본형과 참조형이 있습니다. 기본형은 메모리 공간에 주소와 값이 같이 할당 되어있는것이고
참조형은 객체의 주소를 가지고있는 것입니다. 

final 은 기본형에 사용하면 변수 자체에 수정이 불가능하게 막히지만 참조형에 수정이 불가능하게 걸린다면 객체를 가르켜
수정하지 못하게 막히지만 객체안의 변수들에게 적용되는 것이기 아니기 때문에 객체 내부의 변수는 수정이 가능합니다.

