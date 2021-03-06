---
layout: post
title: Hibernate와 ORM(Object Relational Mapping)
description: >
 게시판 만들기 프로젝트를 진행하며 자세히 알지 못하는 부분에 대한 공부 중 Hibernate에 대한 공부 내용
sitemap: true
hide_last_modified: false
---

1. 개요
{:toc}

# 프로젝트 관리
[프로젝트 관리 문서](https://docs.google.com/spreadsheets/d/1xxuP3eXVIsYP-Pe4pwDcvYthXhtYNUvVXXgRPU3XWqw/edit?usp=sharing)

# Hibernate

## ORM

### 영속화
자바를 사용할때는 객체를 구현하고 사용하게됩니다. 객체는 현실 세계를 모델링하는 상태와 행동을 나타내는데 자바 애플리케이션에서 객체의 **영속성은 필요조건입니다.**
상태는 내구성 있는 스토리지에 영속화되도록 모델링되어 있기 때문에 영구적입니다.

하지만 데이터를 보관할 때는 관계형 데이터베이스에 의존하게 됩니다. 관계형 DB에서 데이터는 전통적으로 로우와 컬럼 형태로 데이터 간의 관계나 연관 관계를 나타냅니다.

자바 객체를 관계형 세상으로 가져온다는 것은 항상 어렵고 힘듬 일입니다. 이런 과정에서 객체-관계 연결(Object-Relational Mapping)이라고 합니다.

### 관계형 데이터 베이스
영속화된 객체를 내구성 있게 보관할 수 있는 저장공간의 필요합니다. 이 데이터들을 저장하기 위해서 DB라는 내구성 있는 저장공간이 필요합니다.


### 객체-관계의 임피던스 불일치
관계형 EB에는 행과 열의 2차워 형태로 데이터가 저장됩니다. 데이터 관계는 외래키 형태로 표현됩니다. 문제로는 도메인 객체를 관계형 DB에 저장할 때 발생합니다.
애플리케이션의 객체는 로우와 컬럼 형태가 아니므로 도메인 객체는 객체의 상태를 속성으로 가지고 있습니다. 그래서 도메인 객체 그래도 관계형 dB에 저장할 수가 없습니다.
이러한 불일치를 객체-관계 간 임피던스 불일치라고 합니다.

#### 상속 불일치

객체에서는 상속을 지원하지만 관계형 스키마에서는 지원하지 않습니다. 상속은 모든 객체지향 언어의 특징입니다.

상속 없이 현실 세계의 문제 상황을 표현하는 것은 매우 복잡한 일입니다. 그런데 DB는 상속 관계와 같은 형태를 알지 못합니다. 이것을 간단히 해결할 방법은 없지만 문제를 풀 수 있는 몇 가지 접근법이 있습니다.

#### 동일성 불일치

자바 애플리케이션에서 객체는 동일성과 동등성의 특징을 모두 가지고 있습니다.

```java
public class Trade {
 private long tradeId = -1;
 private double quantity = 0;
 private String security = null;
```

두 객체는 동일한 메모리 영역(주소)을 가리키므로 동일한 객체로 여겨집니다.

```java
Trace trade1 = new Trade();
Trade trade2 = trade1;
// 메모리 주소의 동일성 같은지 확인
if(trade==trade2){ ... }
// 값의 동등성을 확인
if(trade1.equals(trade2)){...}
```

`==` 연산자로 두 Trade 객체를 비교할 때 메모리 영역을 기준으로 비교하는데, 이 연산자는 두 Trade 객체의 메모리 주소값을 비교합니다. 두 객체가 같은 주소값을 가지고 있다면
두 객체는 동일 합니다.

하지만 한 객체의 값이 다른 객체의 값과 같다면 두 객체는 동등합니다. 앞 코드의 두 번째 코드 블록과 equals 메소드를 이용한 코드 블록은 동등성을 보여줍니다.
개발자는 동등성을 정확하게 표현할 수 있도록 equals 메소드를 작성할 의무가 있습니다.

자바에서는 `==` 연산자와 equals 메소드에 차이가 있습니다. 동일한 객체인지 확인하기 위해 `==` 연산자를 사용하고, 같은 값의 객체인지 확인하기 위해 equals 메소드를 사용합니다.

하지만 관계형 스키마에서는 동일성과 동등성의 개념이 없습니다. 각 행은 컬럼의 값을 통해 구분하기 때문입니다. DB는 동일성과 동등성을 구현하는 데 부족함을 채우기 위해서 기본키 전략을 사용합니다.

기본키 식별자는 구분된 각각의 로우를 나타내고 동일성과 동등성의 개념을 나타내기 위한 객체속성의 하나로 활용됩니다.

#### 관계와 연관 관계의 불일치

자바와 같은 객체지향 언어에서 연관 관계는 중요한 특징입니다. 다행히 관계형 DB에서도 연관 관계를 어느 정도 구현할 수 있습니다. 다른 테이블의 기본키를 참조하는 외래키는 관계형 스키마에서 연관 관계를 표현하는 방법입니다.

하지만 자바에서 객체 모델은 한 타입이 아닌 세 가지 타입의 연관 관계를 보여줍니다. 여러 타입의 연관 관계를 외래키/기본키 관계를 변환하는 작업은 불가능하진 않지만 무척 어려운 일입니다.

하이버네이트 같은 ORM 툴은 객체-관계 영속성 전략 과정을 자동화하기 위해 개발 되었습니다.