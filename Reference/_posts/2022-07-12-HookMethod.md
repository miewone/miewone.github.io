---
layout: post
title: Template Method
description: >
 게시판 만들기 프로젝트를 진행하며 자세히 알지 못하는 부분에 대한 공부 중 Template Method 대한 공부 내용
sitemap: true
hide_last_modified: false
---

1. 개요
{:toc}

# 프로젝트 관리
[프로젝트 관리 문서](https://docs.google.com/spreadsheets/d/1xxuP3eXVIsYP-Pe4pwDcvYthXhtYNUvVXXgRPU3XWqw/edit?usp=sharing)



# Hook Method

abstract 키워드를 붙이면 상속 받은 클래스는 반드시 해당 메소드를 구현해야 하지만 abstract 키워드를
붙이지 않고 Hook Method로 만들면 반드시 구현할 필요가 없습니다. 상속 받은 클래스에서는 선택적으로 오버라이드 할 수 있습니다.

```java
abstract class Cat{
    abstract public void meow();

    public void sleep()
    {
        System.out.println("Zzz");
    }
}

class BlackCate extends Cat {

    @Override
    public void meow()
    {
        // Me...ow?
    }
}
```

sleep() 메서드는 Cat 클래스에서 구현이 되어있기 때문에 추상클래스를 상속받은 클래스에서 오버라이드할 필요없이 바로 사용하여도 됩니다.

