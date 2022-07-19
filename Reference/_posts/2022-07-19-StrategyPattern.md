---
layout: post
title: Strategy Pattern
description: >
 게시판 만들기 프로젝트를 진행하며 자세히 알지 못하는 부분에 대한 공부 중 전략 패턴 대한 공부 내용
sitemap: true
hide_last_modified: false
---

1. 개요
{:toc}

# 프로젝트 관리
[프로젝트 관리 문서](https://docs.google.com/spreadsheets/d/1xxuP3eXVIsYP-Pe4pwDcvYthXhtYNUvVXXgRPU3XWqw/edit?usp=sharing)


# 전략 패턴

- 행위를 클래스로 캡슐화해 동적으로 행위를 자유롭게 바꿀 수 있게 해주는 패턴

- 같은 문제를 해겨할는 여러 알고리즘이 클래스별로 캡슐화되어 있고 이들이 필요할 때 교체할 수 있도록
함으로써 동일한 문제를 다른 알고리즘으로 해결할 수 있게 하는 디자인 패턴
- 객체가 할 수 있는 행위들을 각 각의 전략으로 만들어 놓고 동적으로 행위의 수정이 필요한 경우 전략을 바꾼느 것만으로 행위의
수정이 가능하도록 만든 패턴

## 예시

![stragyPattern](/assets/img/blog/posting/backend/stragypattern.svg)
```java
public abstract class Device {
    private String name;
    public Device(String name) {this.name = name;}
    public Strng getName() {return this.name;}

    public abstract void print();
    public abstract void input();
}

public class Phone extends Device{
    public Phone(String){super(name);}
    public void print(){/* 터치 액정을 통해 출력*/ }
    public void input(){/* 터치 액정을 통한 입력*/ }
}

public class Computer extends Device{
    public Computer(String){super(name);}
    public void print(){/* 모니터를 통해 출력*/ }
    public void input(){/* 마우스 키보드을 통한 입력*/ }
}

public class Client {
    public static void main(String[] args)
    {
        Device s9 = new Phone("갤9+");
        Device superCom = new Computer("슈퍼컴");

        s9.print(); 
        s9.input();

        superCom.print();
        superCom.input();
    }
}
```

기존의 객체를 선언하고 객체의 메소드를 이용할때는 위와 같이 사용하였습니다. 하지만 Phone의 메서드
print의 출력을 스피커로 변경하기 위해서는 아래와 같이 클래스를 변경해야 합니다. 

```java
public class Phone extedns Device{
    public Phone(String){super(name);}
    public void print(){/* 스피커를 통해 출력*/ }
    public void input(){/* 터치 액정을 통한 입력*/ }
}
```
하지만 이와 같은 경우는 Solid 법칙 중 OCP(Open-Close Principle)에 위배를 하게되므로 좋은 수정 방법이 아닙니다.
그리고 Phone와 Compute 클래스에 중복되는 코드가 존재합니다. 

위와 같은 방법을 해결하는 방법이 **전략 패턴**입니다.

위 Device 클래스의 input과 print메서드 들이 문제를 발생시키는 요인이 됩니다.
이를 캡슐화하기 위해서는 외부에서 구체적인 print,input를 클래스들로 은닉해야 합니다. 

print, input 을 위한 인터페이스를 각자 만들고 이들을 실제 실현한 클래스들을 만들어야합니다.
![strategyPatternAfter](/assets/img/blog/posting/backend/strategypattern.svg)

Device 클래스가 출력, 입력 기능을 이용하는 클라이언트 역할을 수행합니다. PrintingStrategy, InputtingStrategy 인터페이스에 의해
캡슐화가 되었으며 이 인터페이스들이 Device 클래스의 변경을 차단할 수 있습니다.

전략 패턴을 이용하면 새로운 기능의 추가가 기존의 코드 영향을 미치지 않으므로 OCP를 만족하게 됩니다.

위 처럼 변화된 구조에서는 외부에서 출력,입력 기능을 임의대로 수정하기 위해서는 setter 메서드가 필요합니다.

```java
public abstract class Device {
    private String name;
    private PrintingStrategy printingStrategy;
    private InputtingStrategy inputtingStrategy;

    public Device(String name) {this.name = name;}
    public Strng getName() {return this.name;}

    public void print() { printingStrategy.print();}
    public void input() { inputtingStrategy.input();}
    
    public void setInputtingStrategy(InputtingStrategy inputtingStrategy){
        this.inputtingStrategy = inputtingStrategy;
    }
    public void setPrintingStrategy(PrintingStrategy printingStrategy){
        this.printingStrategy = printingStrategy;
    }
}

interface PrintingStrategy{public void print();}

public class MonitorStrategy implements PrintingStrategy {
    public void print() {/* 모니터로 출력 */}
}
public class SpeakerStrategy implements PrintingStrategy {
    public void print() {/* 스피커로 출력 */}
}

interface InputtingStrategy{public void input();}
public class TouchPannelStrategy implements InputtingStrategy {
public void input() {/* 터치패널로로 입력 */}
}
public class MouseKeyboardStrategy implements InputtingStrategy {
public void input() {/* 마우스,키보드로 입력 */}
}

public class Phone extends Device{
    public Phone(String){super(name);}
}

public class Computer extends Device{
    public Computer(String){super(name);}
}

public class Client {
    public static void main(String[] args)
    {
        Device s9 = new Phone("갤9+");
        Device superCom = new Computer("슈퍼컴");

        s9.setInputtingStrategy(new TouchPannelStrategy());
        s9.setPrintingStrategy(new MonitorStrategy());


        superCom.setInputtingStrategy(new MouseKeyboardStrategy());
        superCom.setPrintingStrategy(new SpeakerStrategy());

        s9.print(); // 모니터로 출력
        s9.input(); // 터치 패널로 입력

        superCom.print(); // 스피커로 출력
        superCom.input(); // 마우스키보드로 입력
    }
}
```

# References
- [전략 패턴 - gmlwjd9405님](https://gmlwjd9405.github.io/2018/07/06/strategy-pattern.html)
- [전략 패턴 - victolee님](https://victorydntmd.tistory.com/292)