---
layout: post
title: Java Casting (형변환)
description: >
 게시판 만들기 프로젝트를 진행하며 자세히 알지 못하는 부분에 대한 공부 중 Casting 대한 공부 내용
sitemap: true
hide_last_modified: false
---

1. 개요
{:toc}

발텅 문서를 보고 작성하며 중간 중간 제가 필요한 내용을 추가 하였습니다.
{:.note}

# 프로젝트 관리
[프로젝트 관리 문서](https://docs.google.com/spreadsheets/d/1xxuP3eXVIsYP-Pe4pwDcvYthXhtYNUvVXXgRPU3XWqw/edit?usp=sharing)

# Java Casting (형변환)

기본 변환과 참조 변수 캐스팅은 비슷해 보이지만 다른 개념입니다.

두 경우 모드, 한 유형을 다른 유형으로 전환하고 있습니다. 그러나 단순화된 방식으로 기분 변수에는 값이 포함되며 기본 변수의 변환은
값의 되돌릴수 없는 변경을 의미합니다.

```java
double myDouble = 1.1;
int myInt = (int) myDouble;
```

위의 예에서 변환한 후 myInt 변수는 1 이고 이 변수에서 이전 값 1.1 을 복원할 수 없습니다 .

**참조 변수는 위와 같은 반식으로 변환이 이뤄지지 않습니다.**

참조 변수는 개체만 참조하지만 개체 자체는 포함하지 않습니다. 그리고 참조 변수를 캐스팅하면 참조하는 개체를 건드리지 않고 이 개체에 다른 방식으로 레이블을 지정하여
작업 기회를 확대하거나 축소할 수 있습니다.

참조는 개체에 대한 리모컨과 같습니다. 리모콘은 종류에 따라 버튼이 더 많거나 적으며, 객체 자체는 힙에 저장됩니다. 캐스팅을 할 때 리모컨의 종류는 변경하지만 개체 자체는 변경하지 않습니다.


## 업캐스팅

서브클래스에서 슈퍼틀래스로의 캐스팅을 업캐스팅이라고 합니다. 일반적으로 업캐스팅은 컴파일러에서 암시적으로 수행됩니다. (묵시적 형번환)

업캐스팅은 java의 도 다른 핵심 개념인 상속과 밀접한 관련이 있습니다. 참조 변수를 사용하여 보다 구체적인 유형을 참조하는것이 일반적입니다. 그리고 이를 수행할대 마다 암시적 업캐스팅이 발생합니다.

```java
public class Animal{
    public void eat() {
        // ...
    }
}

public class Cat extends Animal{
    public void eat() {
        // ...
    }
    public void meow() {
        // ...
    }
}

// Cat 클래스의 개체를 만들고 Cat 유형의 참조 변수에 할당할 수 있습니다.
Cat cat = new Cat(); // 참조 변수 할당.
// Animal 유형의 참조 변수에 할당할 수도 있습니다.
Animal animal = cat; // <-- 이 과정에서 암시적 업캐스팅이 발생하게 됩니다.
```

`animal = (Animal) cat;`

참조는 선언된 유형의 모든 하위 유형을 참조할 수 있습니다.

업캐스팅을 사용하여 Cat 인스턴스에서 사용할 수 있는 메서드의 수를 제한 했지만 (Cat 메소드 2개 Animal 메서드 1개)
인스턴스 자체는 변경하지 않았습니다. 이제 Cat과 관련된 어떤 것도 할 수 없습니다. 동물 변수에 대해 `meow()`를 호출할 수 없습니다.

`meow()`를 호출할려면 animal에서 cat으로 다운캐스팅해야 합니다.

## 다형성

```java
public class Dog extends Animal {
    public void eat() {
        // ...
    }
}

public class AnimalFeeder {

    public void feed(List<Animal> animals) {
        animals.forEach(animal -> {
            animal.eat();
        });
    }
}
```

개발자는 animalFeeder 모골게 있는 동물이 무엇인지 신경쓰는것을 원하지 않습니다. 

암시적 업캐스팅은 동물 목록에 특정 유형의 개체를 추가할 때 발생합니다.

```java
List<Animal> animals = new ArrayList<>();
animals.add(new Cat());
animals.add(new Dog());
new AnimalFeeder().feed(animals);
```

고양이과 개를 추가하며 암시적으로 Animal 유형으로 업캐스팅했습니다. 각각의 고양이와 개는 동물이며 이것은 다형성을 나타냅니다.

모든 자바의 오브젝트들은 다형성을 가지고있는데 그 이유는 각 오브젝트들은 `Object`이기 때문입니다.
Animal의 인스턴스를 Object type의 참조 변수에 할당할 수 있으며 컴파일러는 오류를 나타내지 않습니다.

`Object object = new Animal();`

그렇기 때문에 우리가 생성하는 모든 Java객체에는 이미 toString()과 같은 Object 특정 메소드가 있습니다. 인터페이스로의 업캐스팅도 비슷합니다.

Mew 인터페이스를 만들고 Cat이 구현 하도록 할 수 있습니다.

```java
public interface Mew {
    public void meow();
}

public class Cat extends Animal implements Mew {
    
    public void eat() {
         // ... 
    }

    public void meow() {
         // ... 
    }
}

Mew mew = new Cat();
```
Cat은 mew 입니다. 업캐스팅은 허용되며 암묵적으로 수행됩니다.

- 참조 변수는 개체가 변수와 동일한 유형이거나 하위 유형인 경우 개체를 참조할 수 있습니다.
- 업캐스팅은 암시적으로 발생합니다.
- 모든 Java 객체는 다형성이며 업캐스팅으로 인해 상위 유형의 객체로 처리될 수 있습니다.
## Primitive, Wrapper

- `int`는 primitive 자료형이며 산술 연산이 가능하고 null로 초기화할 수 없지만
- `Integer`는 Wrapper 클래스로 이루어져있으며 Unboxing을 하지 않으면 산순 연산ㅇ이 불가능하지만, null 값을 처리할 수 있습니다.
null 값을 처리할 수있기에 SQl과 연동할 경우 처리가 용이합니다.


| Primitive 자료형 | Wrapper 클래스 |
|:-------------:|:-----------:|
|      int      |   Integer   |
|     long      |    Long     |
|     float     |    Float    |
|    double     |   Double    |
|      ...      |     ...     |



### Boxing
Primitive 자료형 -> Wrapper 클래스
`int` --> `Integer`로 캐스팅하는걸 Boxing이라고 합니다.
### Unboxing
Wrapper 클래스 -> Primitive 자료형 
반대로 `Integer` --> `int`로 캐스팅하는걸 Unboxing이라고 하죠.