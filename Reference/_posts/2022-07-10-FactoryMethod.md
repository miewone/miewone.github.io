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

우선 팩토리 메서드를 알기전 아래 디자인 패턴을 알고 시작하는게 이해하기 편했으므로 먼저 설명하도록 하겠습니다.

## Template Method Pattern

[TemplateMethodPattern](/reference/2022-07-10-TemplateMethod)

## Strategy Pattern

[StrategyPattern](/reference/2022-07-19-StrategyPattern)

## Factory Pattern 

[FactoryPattern](/reference/2022-07-26-FactoryPattern)

# Factory Method Pattern

팩토리 패턴의 확장판입니다. 기존의 팩토리 패턴에서 원하는 기능을 추가할 수 있습니다.   
오브젝트를 만들어내는 메서드에 상속이 되어 있는가, 클래스 안에 팩토리 메서드가 있는가가 중요합니다.

팩토리 패턴에서는 팩토리에서 클라이언트가 원하는 객체를 생성해 줬다면 팩토리 메서드 패턴은 객체를 생성할 때 객체의 각 각의 
팩토리가 존재합니다. 각 각의 팩토리에는 팩토리 Interface가 존재하며 그 안에는 팩토리 메소드가 들어 있습니다. 

각 각의 팩토리는 interface 팩토리의 팩토리 메소드를 상속하며 각 팩토리에서 추가하고 싶은 기능을 넣으면 팩토리 메서드 패턴이 됩니다.

객체 생성 처리를 서브 클래스로 분리 해 처리하도록 캡슐화 하는 패턴
- 객체의 생성 코드를 별도의 클래스/메서드로 분리함으로써 **객체 생성의 변화**에 대비하는데 유용합니다.
- 특정 기능의 구현은 개별 클래스를 통해 제공되는 것이 바람직한 설계입니다. 기능의 변경이나 사오항에 따른 기능의 선택은 해당 객체를 생성하는
코드의 변경을 초래합니다.
  - 상황에 따라 적절한 객체를 생성하는 코드는 자주 중복될 수 있습니다.


## 역할

- Product: 
팩토리 메서드로 생성될 객체의 공통 인터페이스
- ConcreteProduct :
구체적으로 객체가 생성되는 클래스
- Creator :
팩토리 메서드를 갖는 클래스
- ConcreteCretor :
팩토리 메서드를 구현하는 클래스로 ConcreteProduct 객체를 생성


```java
public abstract class AnimalFactory {
    abstract Animal createAnimal();
}

class CatFactory extends AnimalFactory{
    private int count=0;
    public CatFactory(){
        this.count += 1;
    }
    public Cat createAnimal(){
        return new Cat();
    }
    public int getCount(){
        return this.count;
    }

}
class DogFactory extends AnimalFactory{
    private Dog dog = null;
    
    public Dog createAnimal(){
        return new Dog();
    }
    public Dog haveDog(){
        return this.dog;
    }
}

```

# References
- [heejeong Kwon- [Design Pattern] 팩토리 메서드 패턴이란](https://gmlwjd9405.github.io/2018/08/07/factory-method-pattern.html)
- [팩토리 메서드 패턴 - Youtube - 코드없는 프로그래밍](https://www.youtube.com/watch?v=ejXUhFKcbIU&list=PLDV-cCQnUlIYcAmW4j27i8aYPbja9HePm&index=3)