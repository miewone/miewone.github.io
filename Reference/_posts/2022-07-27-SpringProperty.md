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
# properties, yaml
크게 차이는 없지만 표현식의 차이가 있습니다.
```text
spring.datasource.url= jdbc:mysql://localhost:3306/board
```
```yaml
spring:
 datasource:
  url: jdbc:mysql://localhost:3306/board
```
보이는것 처럼 구분이 힘들게 한줄로 늘어져 있습니다. yaml은 계층 구조임으로 한눈에 파악 하기가 쉽습니다.

# Value 값 주입 받기
```yaml
test : 123
```
위 파일 처럼 name= test, value= 123을 가진 property가 있다면 이 값을 spring에서 불러와 사용할 수 있습니다.
아래는 값을 불러와 주입하기 위한 예시입니다.
```java
class test{
    @Value("${test}")
    private Integer testValue;
}
```
# 후술...

## reference
- [[Spring Boot] 외부설정1 - Application.properties - max9106](https://velog.io/@max9106/Spring-Boot-%EC%99%B8%EB%B6%80%EC%84%A4%EC%A0%95-uik69crax3)
- [Spring 공식 property 참조 데이터](https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#appendix.application-properties.data)
- [  Spring Boot에서 properties 값 주입받기 - Tecoble](https://tecoble.techcourse.co.kr/post/2020-09-29-spring-properties-binding/)


