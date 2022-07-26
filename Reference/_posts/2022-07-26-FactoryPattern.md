---
layout: post
title: Factory 패턴 
description: >
 게시판 만들기 프로젝트를 진행하며 자세히 알지 못하는 부분에 대한 공부 중 팩토리 패턴 대한 공부 내용
sitemap: true
hide_last_modified: false
---

1. 개요
{:toc}

# 프로젝트 관리
[프로젝트 관리 문서](https://docs.google.com/spreadsheets/d/1xxuP3eXVIsYP-Pe4pwDcvYthXhtYNUvVXXgRPU3XWqw/edit?usp=sharing)



# Factory Pattern

메서드와 클래스를 찍어내는 패턴.

복잡한 오브젝트의 생성 과정을 클라이언트가 직접 다룰 필요가 없음.

오브젝트의 복잡한 과정을 팩토리에 숨겨놓고 클라이언트는 팩토리에 호출만 하면 된다.

```java
abstract class Animal {
    abstract void bark();
}

public class AnimalFactory {

    public Animal createAnimal(String animal){
        if(animal.equals("Cat")){
            return new Cat();
        } else if (animal.equals("Dog")) {
            return new Dog();
        }else {
            return new Cat();
        }

    }
}

class Dog extends Animal{
    @Override
    void bark() {
        System.out.println("bark");
    }
}

public class Cat extends Animal{
    @Override
    void bark() {
        System.out.println("meow");
    }
}

// main 
factory = AnimalFactory();
Cat cat = factory.createAnimal("Cat");
Dog dog = factory.createAnimal("Dog");
```