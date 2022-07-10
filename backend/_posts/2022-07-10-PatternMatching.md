---
layout: post
title: Pattern Matching for instanceof
description: >
 게시판 만들기 프로젝트를 진행하며 자세히 알지 못하는 부분에 대한 공부 중 Pattern Matching for instanceof 대한 공부 내용
sitemap: true
hide_last_modified: false
---

1. 개요
{:toc}

# 프로젝트 관리
[프로젝트 관리 문서](https://docs.google.com/spreadsheets/d/1xxuP3eXVIsYP-Pe4pwDcvYthXhtYNUvVXXgRPU3XWqw/edit?usp=sharing)

# Pattern Matching for instanceof

## 쓰여왔던 `instanceOf` 연산자
동물 개체의 간단한 계층 구조에서 유형을 확일혀는 코드입니다.
```java
if (animal instanceof Cat)
{
    Cat cat = (Cat) animal;
    cat.meow();
}else if (animal instanceof Dog) {
    Dog dog = (Dog) animal;
    dog.woof();
}
```
이 예에서 각 조건 블록에 대해 동물 매개변수를 테스트하여 유형을 결정하고 캐스트를 통해 변환하고 지역 변수를 선언합니다.

이 접근 방식은 효과가 있지만 다음과 같은 몇 가지 단점이 있습니다.
- 유형을 테스트하고 모든 조건부 블록에 대해 캐스트해야 하는 이러한 유형의 코드를 작성하는 것은 지루합니다.
- if 블록 마다 유형 이름을 세 번 반복합니다.
- 캐스트 및 변수 추출이 코드를 지배하므로 가독성이 떨어집니다.
- 유형 이름을 반복적으로 선언하면 오류가 발생할 가능성이 더 높아집니다 . 이로 인해 예기치 않은 런타임 오류가 발생할 수 있습니다.
- 새로운 동물을 추가할 때마다 문제가 확대됩니다.

## Java 14에서 향상된 instanceOf
```java
if (animal instanceof Cat cat) { 
    cat.meow();
}else if(animal instanceof Dog dog) {
    dog.woof();
}
```

동물을 Cat cat  유형 패턴과 일치시킵니다. 먼저 동물 변수를 테스트하여 그것이 Cat의 인스턴스인지 확인합니다.
그렇다면 Cat 유형으로 캐스팅되고 마지막으로 결과를 cat에 할당합니다.

변수 이름 cat은 기존 변수가 아니라 패턴 변수의 선언이라는 점에 유의하는 것이 중요합니다. 또한 변수 cat과 dog는 볌위 내에 있고 각각의 패턴 일치 표현식이 true를 반환할 때 할당된다는 점도 언급해야합니다.

결과적으로 다른 위치에서 두 변수 중 하나를 사용하려면 코드에서 컴파일러 오류가 생성됩니다. 

 
# Reference

- [Open JDK](https://openjdk.org/jeps/394)
- [baeldung](https://www.baeldung.com/java-pattern-matching-instanceof)