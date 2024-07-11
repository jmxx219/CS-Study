# Hibernate, JPA, Spring Data JPA

<img width="300" alt="스크린샷 2024-07-09 오후 11 08 13" src="https://github.com/jmxx219/CS-Study/assets/50795805/36ad4a09-59d5-4e6b-a661-73f97eae03bf">

<br>
<br>

## JPA (Java Persistence API)

> Java 어플리케이션에서 관계형 데이터베이스를 사용하는 방식을 정의한 **인터페이스**

<br>

### 개념
- 자바 플랫폼에서 [ORM](https://github.com/jmxx219/CS-Study/blob/main/spring/ORM.md)을 정의하는 표준 명세
- 자바 객체와 데이터베이스 간 매핑을 정의하는 표준 제공
    - 어노테이션 기반 매핑

<br>

### 특징
- JPA는 특정 기능을 하는 라이브러리가 아닌 `인터페이스`
    - 단순 명세로 구현이 없음
    - 다양한 ORM프레임워크에서 구현할 수 있는 공통API 제공
    - JPA 정의 패키지 `javax.persistence`는 interface, enum, Exception, Annotation으로 이뤄짐
- 영속성 컨텍스트 제공

<br>

### 사용 이유
- 관계형 데이터베이스와 객체의 `패러다임 불일치 문제` 해결
    - SQL에 종속적인 개발이 아닌 객체지향적인 코드 작성 가능
- 생산성
    - 간단한 CRUD 알아서 다 짜주기에 SQL작성 시 많은 시간투자X
    - 반복적인 SQL을 만드는 단순 반복작업X
- 유지보수
    - 필드 추가 시 JPA가 자동으로 SQL처리
- 성능
    - 1차 캐시와 동일성 보장
        - 같은 트랜잭션안에서는 같은 엔티티를 반환하기 때문에 DB와의 통신 횟수를 줄일 수 있음
    - 트랜잭션을 지원하는 쓰기지연
        - 트랜잭션을 commit 하기 전까지 INSERT SQL을 메모리에 쌓고 한번에 DB로 SQL 전송
    - 지연 로딩, 즉시 로딩
- 데이터 접근 추상화와 벤더 독립성
    - 인터페이스로 추상화된 데이터 접근을 제공하기에, DB변경시 JPA에게 알려 종속성 해결 가능

<br>
<br>


### ➕ 패러다임 불일치 해결

**■ 상속**
- 문제
    - 객체 지향 프로그래밍: 클래스 상속이 일반적
    - 관계형 데이터베이스: 상속 개념 없음
- 해결
    - JPA는 상속 전략 제공으로 객체 상속 구조를 테이블에 매핑
        - 상속전략 : `단일 테이블 전략`, `조인 전략`, `테이블당 클래스 전략`

**■ 연관관계**
- 문제
    - 객체 지향 프로그래밍: 객체 간의 참조로 연관관계 표현 
    - 관계형 데이터베이스: 외래 키로 연관관계 표현
- 해결
    - JPA는 애노테이션을 사용하여 객체 간의 연관관계를 데이터베이스 테이블의 외래 키와 매핑
        - 어노테이션: `@OneToOne`, `@OneToMany`, `@ManyToOne`, `@ManyToMany`

**■ 그래프 탐색**
- 문제
    - 객체 지향 프로그래밍: 객체 그래프 탐색이 쉬움
    - 관계형 데이터베이스: 조인 쿼리를 사용해야 함
- 해결
    - JPA는 지연 로딩(Lazy Loading)과 즉시 로딩(Eager Loading)을 통해 객체 그래프 탐색을 효율적으로 처리
    - 연관된 객체를 사용하는 시점에 적절한 쿼리를 실행하여 연관된 객체 조회 가능

**■ 비교**
- 문제
    - 객체의 동일성: 객체는 동일성 비교(==)와 동등성 비교(equals())가 존재
    - 데이터베이스의 동일성: 기본 키 값이 동일한지로 판단
    - JDBC를 사용하여 같은 키값 두 번 조회 시, new 연산자로 새로운 인스턴스가 생성되어 반환되기에 동일성 비교 실패
- 해결
    - 엔티티 식별자 정의
    - 1차 캐시
        - 1차 캐시를 사용하여 동일한 식별자를 가진 엔티티가 항상 동일한 객체로 반환되도록 보장
    - 동일 트랜잭션에서 같은 행 조회 시 동일한 객체 반환하는 것을 보장
    

<br>
<br>
<br>

## Hibernate

> JPA라는 명세의 구현체로, 인터페이스를 직접 구현한 라이브러리

<br>

### 개념
- 자바언어를 위한 ORM 프레임워크로, 자바 애플리케이션에서 데이터베이스 연동을 쉽게 처리할 수 있도록 도움 
- JPA의 구현체로 JPA 인터페이스를 구현하며, 내부적으로 JDBC API 사용

<br>


### 특징
- JPA의 구현체 중 하나
    - Hibernate이외의 다른 JPA구현체 사용가능
    - EX) EclipseLink, OpenJPA 
- HQL (Hibernate Query Language)
    - 데이터베이스 독립적인 쿼리 언어
    - SQL과 유사하지만 테이블 대신 객체를 대상으로 쿼리를 작성
- 자동화된 데이터베이스 연동

> JPA의 구현체로 지연로딩, 캐시지원과 같이 JPA의 특징을 가짐

<br>
<br>


### JPA와 Hibernate의 상속 및 구현 관계
<br>

| JPA              | Hibernate             |
|-----------------------------|---------------------------------------|
| `EntityManagerFactory`      | `SessionFactory`                      |
| `EntityManager`             | `Session`                             |
| `EntityTransaction`         | `Transaction`                         |

<br>

* SessionFactory
    - 애플리케이션 당 하나만 생성되는 객체
    - 데이터베이스와의 연결을 설정하고 Session 생성

* Session
    - 데이터베이스 작업을 수행하는 객체
    - 엔티티 저장, 수정, 삭제, 조회 시 사용
    - 짧은 시간 동안 유지되며 사용 후에는 닫아야 함

* Transaction
    - 데이터베이스 작업의 원자성을 보장하는 객체
    - 작업 단위를 정의하고 커밋 또는 롤백 가능

* Entity
   - 데이터베이스 테이블에 매핑되는 자바 클래스
   - 클래스의 필드와 데이터베이스의 컬럼 간의 매핑을 설정

<br>
<br>
<br>


## Spring Data JPA

> Spring에서 제공하는, JPA를 쓰기 편하게 만들어 놓은 모듈

<br>

### 개념
- Spring에서 제공하는 모듈로 JPA 위에 추가적인 기능 제공
    - JPA 기반 애플리케이션 개발을 보다 간편하게 만드는 라이브러리/프레임워크
- JPA의 구현체 위에 동작하며, 복잡한 데이터 접근 로직을 단순화하고 자동화 함
    
<br>

### 특징
- `Repository` 인터페이스 제공을 통해 이뤄짐
    - JPA를 한 단계 **추상화** 시킨 인터페이스
    - JPA의 EntityManager와 직접 상호작용하지 않고도 데이터 접근 가능
    - 동작
        1. JpaRepository<> 인터페이스 상속
        2. Spring Data JPA가 구현 클래스를 대신 만듦(프록시 기술 사용)
        3. 만든 구현 클래스의 인스턴스를 만들어 **스프링 빈**으로 등록
- 쿼리 메서드 기능
    - 인터페이스에 메서드만 적어두면, 메서드 이름을 분석해서 JPQL 쿼리를 자동으로 만들고 실행하는 기능 제공
- JPQL 직접 사용도 가능
    - 쿼리 메서드 기능 대신 직접 JPQL 사용하고 싶을 시, `@Query`로 JPQL 작성
    - 이때 매서드 이름으로 실행되는 규칙은 무시 됨
- 쉬운 페이징과 정렬 처리 기능 제공

<br>
<br>

## ➕ JPQL과 QueryDSL

### JPQL `(Java Persistence Query Language)`
- 개념
    - JPA의 쿼리언어로 객체지향 쿼리 언어
- 특징
    - 객체 중심
        - 데이터베이스 테이블이 아닌 엔티티 객체를 대상으로 쿼리 수행
    - 표준화
        - JPA 표준에 포함되어 있어, 특정 JPA 구현체에 종속되지 않음
    - 유연성
        - 다양한 조건, 정렬, 집계 등 지원
        - 네이티브 SQL과 통합 가능
- 예시
    ```java
    // 모든 사용자를 조회하는 JPQL 쿼리
    String jpql = "SELECT u FROM User u";
    List<User> users = entityManager.createQuery(jpql, User.class).getResultList();
    ```

<br>

### QueryDSL
- 개념
    - 타입 안전한 쿼리를 작성할 수 있게 해주는 Java 라이브러리
    - 타입 세이프한 SQL쿼리 형태를 자바 코드로 작성할 수 있게해주는 프레임워크
- 특징
    - 타입 안정성
        - 쿼리 작성 시 타입 안전성을 보장하여 컴파일 시점에 오류 검출
    - 가독성
        - 메서드 체이닝을 통해 가독성이 높은 쿼리 작성 가능
    - 자동 완성
        - IDE에서 자동 완성을 지원하여 개발 생산성 높임
    - SQL, JPA, MongoDB, Elasticsearch 등 다양한 데이터 소스에 대한 쿼리 지원
    - 문자가 아닌 코드로 작성
    - 동적 쿼리 편하게 작성 가능
- 예시
    ```java
    // QueryDSL을 사용하여 모든 사용자를 조회하는 쿼리
    JPAQueryFactory queryFactory = new JPAQueryFactory(entityManager);
    QUser user = QUser.user;
    List<User> users = queryFactory.selectFrom(user).fetch();
    ```

<br>


<br>
<br>
<br>

## 요약

<br>

|                | JPA                                           | Hibernate                                   | Spring Data JPA                                     |
|--------------------|-----------------------------------------------|---------------------------------------------|-----------------------------------------------------|
| 개념               | 자바 플랫폼의 ORM 표준 명세 | 자바 기반의 ORM 프레임워크                   | JPA를 쉽게 사용하도록 도와주는 Spring의 하위 모듈 |
| 주요 기능           | 표준화된 ORM 명세, JPQL, 표준 API                | HQL, 캐시, 자동 테이블 생성 등 | Repository 패턴, 메서드 이름 기반 쿼리 생성, 페이징 및 정렬 |
| 설정 방법           | 애노테이션                                      | XML, 애노테이션                              | 애노테이션, 인터페이스 기반                          |
| 사용 방식           | 표준 JPA API 사용 (EntityManager 등)                               | Hibernate API 사용 (Session, Transaction 등)                         | Spring Data JPA 인터페이스 사용 (JpaRepository)                      |
| 구현체             | Hibernate, EclipseLink, OpenJPA 등               | -                           | Hibernate 등 JPA 구현체 위에서 동작                  |



<br>
<br>
<br>

### Ref
[JPA, Hibernate, 그리고 Spring Data JPA의 차이점](https://suhwan.dev/2019/02/24/jpa-vs-hibernate-vs-spring-data-jpa/)               
[[JPA] JPA란 ? 그리고 Spring Data JPA](https://hstory0208.tistory.com/entry/JPA-JPA란-그리고-Spring-Data-JPA)