---
layout: post
title: JPA Auditing
description: >
 게시판 만들기 프로젝트를 진행하며 자세히 알지 못하는 부분에 대한 공부 중 JPA Auditing 대한 공부 내용
sitemap: true
hide_last_modified: false
---

1. 개요
{:toc}

# 프로젝트 관리
[프로젝트 관리 문서](https://docs.google.com/spreadsheets/d/1xxuP3eXVIsYP-Pe4pwDcvYthXhtYNUvVXXgRPU3XWqw/edit?usp=sharing)

# 시작

ORM의 맥락에서 DB 감사는 영구 엔티티 또는 단순히 엔티티 버전 관리와 관련된 이벤트를 추적 및 로깅하는 것을 의미합니다.
SQL 트리거에서 영감을 받은 이벤트는 엔티티에 대한 삽입, 업데이트 및 삭제 작업입니다. DB Auditing의 이점은 소스 버전 제어가 제공하는 이점과 유사합니다.

샘플 관련 에티티인  Bar과 Foo 입니다.

<img alt="jpaAuditing" height="200" src="https://www.baeldung.com/wp-content/uploads/2016/07/Screenshot_4.png" width="400"/>

# JPA Auditing

JPA에는 감사 API가 명시적으로 포함되어 있지 않지만 엔티티 수명 주기 이벤트를 사용하여 이 기능을 달성할 수 있습니다.

## @PrePersist, @PreUpdate, @PreRemove

JPA 엔티티 클래스에서 메서드를 콜백으로 지정할 수 있습니다. 이 메서드는 특정 엔티티 수명 주기 이벤트 동안 호출할 수 있습니다.
해당 DML 작업 이전에 실행된 콜백에 관심이 있으므로 @PrePersist, @PreUpdate, @PreRemove 콜백 주석을 사용할 수 있습니다.

```java
@Entity
public class Bar {
      
    @PrePersist
    public void onPrePersist() { ... }
      
    @PreUpdate
    public void onPreUpdate() { ... }
      
    @PreRemove
    public void onPreRemove() { ... }
      
}
```
위 메소드들은 아까 말한것처럼 트리거의 역할을 합니다. 만약, 새로운 엔티티를 호출하면 호출되기전에 @PrePersist 어노테이션이 달린 메소드가
실행이 됩니다. @PreUpdate 메소드는 update 명령이 실행이되면 업데이트 명령이 수행되기전 실행이 되는겁니다.

내부 콜백 메서드는 항상 void를 반환하고 인수를 사용하지 않아야 합니다. 모든 이름과 액세스 수준을 가질 수 있지만 static이 아니어야 합니다.
JPA의 @Version 어노테이션은 이 주제와 관련이 없습니다. 이는 Auditing 데이터보다 유연한 잠금과 관련이 있습니다.

## CallBackMethod

하지만 위 접근 방식에는 상당한 제한이 있습니다. 

> 일반적으로 '휴대용' 애플리케이션의 수명주기 방법은 엔터티 관리자나 쿼리 작업을 호출하거나, 다른 엔터티 인스턴스에 액세스하거나, 동일한 지속성 컨텍스트 내에서 관계를 수정하지 않아야 한다. 라이프사이클 콜백 방법은 호출된 엔티티의 비관계 상태를 수정할 수 있다.

Audit 프레임워크가 없는 경우, DB 스키마와 도메인 모델을 수동으로 유지 관리해야 합니다.  

간단한 사용 사례의 경우 "엔티티의 비관계 상태"만 관리할 수 있으므로 두 개의 새 속성을 엔티티에 추가해 봤습니다.작업 속성은 수행 된 작업의 이름을 저장하고 timestamp 속성은 작업의 타임스탬프에 대한 것입니다.

```java
@Entity
public class Bar {
     
    //...
     
    @Column(name = "operation")
    private String operation;
     
    @Column(name = "timestamp")
    private long timestamp;
     
    //...
     
    // standard setters and getters for the new properties
     
    //...
     
    @PrePersist
    public void onPrePersist() {
        audit("INSERT");
    }
     
    @PreUpdate
    public void onPreUpdate() {
        audit("UPDATE");
    }
     
    @PreRemove
    public void onPreRemove() {
        audit("DELETE");
    }
     
    private void audit(String operation) {
        setOperation(operation);
        setTimestamp((new Date()).getTime());
    }
     
}
```
이러한 감사를 여러 클래스에 추가해야 하는 경우 @EntityListeners를 사용하여 코드를 중앙 집중화할 수 있습니다.

```java
@EntityListeners(AuditListener.class)
@Entity
public class Bar { ... }

public class AuditListener {
    
    @PrePersist
    @PreUpdate
    @PreRemove
    private void beforeAnyOperation(Object object) { ... }
    
}
```

## 감사 로그 테이블 생성

- envers가 자동으로 생성할 수 있도록 `hibernate.hbm2ddl.auto`를 create, create-drip 또는 update 로 설정 하세요.
- 프로그래밍 방식으로 전체 DB 스키마를 내보내려면 `rg.hibernate.tool.EnversSchemaGenerator`를 사용 하세요.
- 적절한 DDL 문을 생성하기 위해 Ant 작업을 설정합니다.
- 매핑에서 DB 스키마를 생성하기 위해 Maven 플러그인을 사용하여 Enverts 스키마 내보기를 이요하시면 됩니다.

이 경우 bar_AUD 및 foo_AUD(Foo도 @Audited 로 설정한 경우) 테이블이 자동으로 생성됩니다. 감사 테이블은 REVTYPE(값은 추가의 경우 "0", 업데
이트 되는 경우"1", 엔티티가 제거 되는 경우 "2") 및 REV의 두 필드가 있는 엔티티 테이블의 모든 감사된 필드를 복사합니다.

## Spring Data JPA

Spring Data JPA는 JPA 제공자 상단에 추상화 계층을 추가하여 JPA를 확장하는 프레임워크 입니다. 이 계층은
Spring JPA 저장소 인터페이스를 확장하여 JPA 저장소 생성을 지원합니다.

목적을 위해 일반 CRUD 작업을 위한 인터페이스 인 CrudRepository<T, ID extends Serializable>
을 확장할 수 있습니다. 저장소를 만들고 다른 구성 요소에 주입하자마자 Spring Data는 자동으로 구현을 제공하고 감사 기능을 추가할 준비가 되었습니다.

### JPA 감사 활성화

감사를 활성화 하기 위해서 클래스에 @Configuration과 @EnableJpaAuditing을 추가해야합니다.

```java
@Configuration
@EnableTransactionManagement
@EnableJpaRepositories
@EnableJpaAuditing
public class PersistenceConfig { ... }
```

### Spring의 엔티티 콜백 리스터 추가하기

위에서 사용한듯이 JPA는 콜백 수신기 클래스를 지정하기 위해 @EntityListeners 어노테이션을 제공합니다. Spring
Data 는 자체 JPA 엔티티 리스너 클래스인 AuditingEntityListener를 제공합니다. Bar 엔티티에 대한 리스너를 지정해 보겠습니다.

```java
@Entity
@EntityListeners(AuditingEntityListener.class)
public class Bar { ... }
```

이제 Bar 엔터티 를 유지하고 업데이트할 때 수신기의 감사 정보를 캡처할 수 있습니다 .


### 생성 날짜 및 수정일자 추적

생성 날짜와 수정일시를 Bar 엔티티에 저장하기 위해 두개의 새 속성을 추가합니다. 속성은 그에 따라 @CreatedDate 및 
@LastModifiedDate 어노테이션을 할당해야하고 해당 값을 자동으로 설정합니다.

```java
@Entity
@EntityListeners(AuditingEntityListener.class) // <----- 설정 클래스 지정
public class Bar {
    
    //...
    
    @Column(name = "created_date", nullable = false, updatable = false)
    @CreatedDate // <----
    private long createdDate;

    @Column(name = "modified_date")
    @LastModifiedDate // <----
    private long modifiedDate;
    
    //...
    
}
```


일반적으로 속성을 기본 클래스(@MappedSuperClass로 어노테이션 할당을 함)로 이동합니다. 이 클래스는 감사 대상이 되는 모든
엔티티가 확장됩니다. 이 예에서는 단순성을 위해 Bar에 직접 추가합니다.

### Spring Security 로 변경 작정자 감사

Spring Security를 사용하는 경우 변경 사항이 적용된 시점과 변경한 사람을 추적할 수 있습니다.

```java
@Entity
@EntityListeners(AuditingEntityListener.class)
public class Bar {
    
    //...
    
    @Column(name = "created_by")
    @CreatedBy
    private String createdBy;

    @Column(name = "modified_by")
    @LastModifiedBy
    private String modifiedBy;
    
    //...
    
}
```

@CreatedBy, @LastModifiedBy 어노테이션이 달린 컬럼은 엔티티를 만들거나 마지막으로 수정한 사용자의 이름으로 채워집니다.
정보는 SecurityContext 인증 인스턴스에서 가져옵니다. 어노테이션 필드에 설정된 값을 사용자 정의하려면 AuditorAware<T> 인터페이스를 구현할 수 있습니다.

```java
public class AuditorAwareImpl implements AuditorAware<String> {
 
    @Override
    public String getCurrentAuditor() {
        // your custom logic
    }

}
```

현재 사용자를 조회하기 위해 AuditorAwareImpl을 사용하도록 앱을 구성하기 위해 AuditorAwareImpl의 인스턴스
로 초기화된 AuditorAware 유형의 Bean을 선언하고 Bean의 이름을 @EnableJpaAuditing에서 auditorAwareRef 매개변수의 값으로 지정합니다.

```java
@EnableJpaAuditing(auditorAwareRef="auditorProvider")
public class PersistenceConfig {
    
    //...
    
    @Bean
    AuditorAware<String> auditorProvider() {
        return new AuditorAwareImpl();
    }
    
    //...
    
}
```