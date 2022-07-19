---
layout: post
title: Factory Method 
description: >
 게시판 만들기 프로젝트를 진행하며 자세히 알지 못하는 부분에 대한 공부 중 팩토리 메서드 대한 공부 내용
sitemap: true
hide_last_modified: false
---

1. 개요
{:toc}

# 프로젝트 관리
[프로젝트 관리 문서](https://docs.google.com/spreadsheets/d/1xxuP3eXVIsYP-Pe4pwDcvYthXhtYNUvVXXgRPU3XWqw/edit?usp=sharing)



# Factory Method Pattern 시작 전

객체 생성 처리를 서브 클래스로 분리 해 처리하도록 캡슐화 하는 패턴입니다.

우선 팩토리 메서드를 알기전 템플릿 메서드를 알고 시작하는게 이해하기 편했으므로 템플릿 메서드 먼저 설명하도록 하겠습니다.

## Template Method Pattern

[TemplateMethodPattern](/reference/2022-07-10-TemplateMethod)

# Factory Method Pattern

객체 생성 처리를 서브 클래스로 분리 해 처리하도록 캡슐화 하는 패턴
- 객체의 생성 코드를 별도의 클래스/메서드로 분리함으로써 **객체 생성의 변화**에 대비하는데 유용합니다.
- 특정 기능의 구현은 개별 클래스를 통해 제공되는 것이 바람직한 설계입니다. 기능의 변경이나 사오항에 따른 기능의 선택은 해당 객체를 생성하는
코드의 변경을 초래합니다.
  - 상황에 따라 적절한 객체를 생성하는 코드는 자주 중복될 수 있습니다.


이번 강의에서 나온 팩토리 메서드를 이용하는 코드입니다.

```java
private Article(String title,String content,String hashtag){
    this.title=title;
    this.content=content;
    this.hashtag=hashtag;
}

public static Article of(String title,String content,String hashtag){
    return new Article(title,content,hashtag);
}
```

# References
- [heejeong Kwon- [Design Pattern] 팩토리 메서드 패턴이란](https://gmlwjd9405.github.io/2018/08/07/factory-method-pattern.html)