# @Transactional

<br/>

## 트랜잭션(Transacntion)이란?

[트랜잭션 참고](https://github.com/jmxx219/CS-Study/blob/main/Database/%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98.md)

- 개념
    - DBMS(데이터베이스 관리 시스템) 또는 유사한 시스템에서 발생하는 연산들의 상호작용 단위
        - 유사한 시스템이란 트랜잭션의 성공과 실패가 분명하고 상호독립적이며, 일관되고 믿을 수 있는 시스템을 의미함
    - 데이터베이스의 상태를 변경시키기 위해 수행하는 작업 단위 또는 한꺼번에 모두 수행되어야 할 일련의 연산을 의미
- 필요성
    - 데이터베이스의 데이터를 수정하는 도중에 예외가 발생할 경우, DB의 데이터들은 수정되기 전의 상태로 돌아가야 하고 다시 수정 작업이 진행되어야 함
    - 이 때문에 데이터의 일관성을 유지하면서 안정적으로 데이터를 복구하기 위해서 트랜잭션을 사용함
- 특징
    - ACID 원칙을 적용하여 데이터 일관성과 무결성을 보장함
    - 트랜잭션 내 연산은 모두 독릭적으로 이루어지며, 그 과정 중간에 다른 연산은 할 수 없고 하나의 연산이라도 오류가 발생하면 해당 트랜잭션은 취소되고 모두 원래대로 되돌아가야 함
- 트랜잭션 연산(작업)
    - 커밋: 하나의 트랜잭션이 성공적으로 끝났으며, 데이터베이스가 일관성이 있는 상태로 유지될 때 트랜잭션이 마무리되었다는 것을 트랜잭션 관리자에게 알리기 위한 연산
    - 롤백: 트랜잭션 처리가 비정상적으로 종료된 경우, 트랜잭션을 다시 시작하거나 트랜잭션의 부분 연산 결과를 취소시킴


<br/>
<br/>

## 스프링에서 트랜잭션 처리


### 0. 순수 JDBCTemplete 사용


```java
public class MemberService {
    public void updateAge(Long memberId) {
        Connection connection = dataSource.getConnection(); // (1)
        try(connection){
            connection.setAutoCommit(false); // (2)
            
            // 비즈니스 로직 수행
            Member member = memberRepository.findById(connection, memberId);
            memberRepository.update(connection, memberId, member.getAge() + 1);

            connection.commit(); // (3)
        }catch(SQLException e){
            connection.rollback(); // (4)
        }finally{
            connection.close(); // (5)
        }
    }
}
```
- 개념
    - Java에서 데이터베이스 트랜잭션을 시작하는 유일한 방법으로, Connection 객체의 메서드를 이용하여 트랜잭션을 생성할 수 있음 
    - Spring의 `@Transactional`도 JDBC의 기본 방식으로 동작함
- 동작 방식
    1. 데이터베이스를 쓰기 위해 연결 진행함(`Connection` 객체 생성)
    2. `setAutoCommit(false)`로 트랜잭션을 직접 관리할 수 있음
    3. 비즈니스 로직 수행 뒤, commit 실행
    4. 예외가 발생했을 때 rollback 실행
- 문제점
    - 트랜잭션 동기화 문제
        - 개발자가 직접 여러 개의 작업을 하나의 트랜잭션으로 관리하려면 `connection` 객체를 공유하는 등 많은 작업들을 하게 됨
            - ex) 같은 트랜잭션을 유지하기 위해서 `connection`을 `DAO` 호출할 때마다 파라미터로 넘겨주어야 함
        - 해결: `Spring 트랜잭션 동기화`
    - JDBC 구현 기술이 서비스 계층에 누수되는 문제
        - 트랜잭션을 적용하기 위해 서비스 계층이 JDBC 기술에 의존되어있음(데이터 접근 기술에 의존적인 코드)
        - 향후 JDBC에서 JPA와 같은 기술로 바뀌면 JDBC 의존성 코드 때문에 서비스 코드도 모두 변경해야하는 문제 발생
        - 해결: `Spring 트랜잭션 추상화`
    - 순수 비즈니스 로직과는 다른 관심사의 일(DB 접근 관련)을 수행하고 있음
        - 해결: `Spring 선언전 트랜잭션`
    - 트랜잭션 적용 코드 반복 문제(try, catch, finally ...)


<br/>

> 스프링은 트랜잭션과 관련한 핵심 기술 3가지를 제공하고 있음

<br/>

### 1. 트랜잭션 동기화

```java
public void method() throws Exception {
    // (1) 동기화 작업 초기화
    TransactionSynchronizationManager.initSynchronization(); 
    // (2) DB 커넥션 생성 및 트랜잭션 시작
    Connection connection = DataSourceUtils.getConnection(dataSource); 
    connection.setAuthCommit(false); 

    try{
        // 비즈니스 로직 수행
        connection.commit(); // (3)
    }catch(Exception e){
        connection.rollback(); // (4)
        throw e;
    }finally{
        DataSourceUtils.releaseConnection(connection, dataSource); // 커넥션 자원 해제
        // (5) 동기화 작업 종료 및 정리
        TransactionSynchronizationManager.unbindResource(this.dataSource);
        TransactionSynchronizationManager.clearSynchronization();
    }
}
```

- 개념
    - 트랜잭션을 시작하기 위한 `Connection` 객체를 특별한 저장소에 보관해두고 필요할 때 꺼내쓸 수 있도록 하는 기술
- `TransactionSynchronizationManager`
    - 트랜잭션 동기화 저장소로, 작업 스레드마다 독립적으로 `Connection` 객체를 저장하고 관리함
    - 작업 쓰레드마다 `Connection` 객체를 독립적으로 관리하기 때문에, 멀티쓰레드 환경에서도 충돌이 발생하지 않음
    - `DataSourceUtils`을 사용해 `Connection` 객체를 가져와 사용함
- 문제점: 여전히 data 접근 기술에 의존적임

<br/>

### 2. 트랜잭션 추상화

```java
public class MemberService {
    private PlatformTransactionManager transactionManager;

    public void setTransactionManager(PlatformTransactionManager transactionManager) {
        this.transactionManager = transactionManager;
    }

    public void method() throws Exception {
        // (1) 트랜잭션 시작
        TransactionStatus status = this.transactionManager.getTransaction(new DefaultTransactionDefinition());

        try{
            // 비즈니스 로직 수행
            transactionManager.commit(status); // (2)
        }catch(Exception e){
            transactionManager.rollback(status); // (3)
            throw e;
        }
    }
}
```

- 개념
    - 스프링은 트랜잭션 기술의 공통점을 담은 트랜잭션 추상화 기술을 제공하고 있음
    - 이를 이용하여 애플리케이션에 각 기술(JDBC, JPA, Hibernate 등)에 종속적인 코드를 이용하지 않고도 일관되게 트랜잭션을 처리할 수 있도록 함
        - 기술 별로 가져오는 트랜잭션 형태만 다르고 기능은 같이 때문에 추상화가 가능함
    - 스프링 트랜잭션 추상화의 핵심: `PlatformTransactionManager` 인터페이스
- `PlatformTransactionManager`
    - 사용할 DB(JDBC)의 `DataSource`를 생성자에 넣어 트랜잭션 추상 오브젝트를 생성하여 사용함
    - 이때 환경에 맞는 `TransactionManager` 클래스를 DI 받아 사용함
        - JDBC: `transactionManager = new DataSourceTransactionManager(dataSource)`
        - JPA: `transactionManager = new JpaTransactionManager()`
    - `getTransaction()`으로 트랜잭션을 시작함
- 문제점: 여전히 순수 비즈니스 로직과는 다른 관심사의 일(DB 접근)을 수행함

<br/>

### 3. AOP를 이용한 트랜잭션 분리

```java
@Transactional
public class MemberService {

    public void updateAge(Long memberId) throws Exception {
        // 비즈니스 로직 수행
        Member member = memberRepository.findById(memberId);
        memberRepository.update(memberId, member.getAge() + 1);
    }
}
```

- 소스 코드에 직접 트랜잭션 관련 로직을 넣지 않고, 비즈니스 로직에서 완전히 분리하는 방식
- 스프링은 트랜잭션 코드와 같은 부가 기능 코드를 비즈니스 로직 클래스 밖으로 빼내서 별도의 모듈로 만드는 AOP를 고안함
    - 이를 적용한 트랜잭션 어노테이션이 `@Transactional`임
- 이 어노테이션을 적용하면 핵심 비즈니스 로직만 남게 됨

<br/>
<br/>

## @Transactional 어노테이션

- 개념
    - 스프링에서 많이 사용되는 선언적 트랜잭션 방식으로, 적용된 범위가 트랜잭션이 되도록 보장해줌
        - 선언적 트랜잭션이란 설정 파일 또는 어노테이션 방식으로 간편하게 트랜잭션에 관한 행위를 정의하는 것
    - `getConnection()`, `setAutoCommit(false)`, commit(), `rollback()` 등의 JDBC에서 필요한 코드를 삽입해줌
- 사용
    - Spring에서 ServiceLayer에 해당 어노테이션을 추가하여 트랜잭션 처리를 진행함
    - 서비스 클래스 혹은 메서드 단위에 어노테이션을 추가하여 사용함
    - `@Transactional` 어노테이션을 사용하라면 설정에 `@EnableTransactionManagement`를 추가해야 하는데, 스프링 부트에서는 자동으로 설정되어 있음
- 옵션
    - `isolation` : 트랜잭션에서 일관성 없는 데이터 허용 수준을 설정(격리수준)
    - `propagation` : 트랜잭션 동작 도중 다른 트랜잭션을 호출할 때, 어떻게 할 것인지 설정하는 옵션
    - `noRollbackFor` : 특정 예외 발생 시 rollback을 수행하지 않는 옵션
    - `rollbackFor` : 특정 예외 발생 시 rollback을 수행하는 옵션
    - `timeout` : 지정한 시간 내에 메소드 수행이 완료되지 않으면 rollback을 하는 옵션
    - `readOnly`: 트랜잭션을 읽기 전용으로 설정

### isolation

```java
@Trasanctional(isolation = Isolation.READ_UNCOMMITTED)
public static void main() {}
```

- READ_UNCOMMITTED (level 0) 
  - 트랙잭션에 처리중인 혹은 아직 커밋되지 않은 데이터를 다른 트랜잭션이 읽는 것을 허용한다. 
  - 어떤 사용자가 A라는 데이터를 B라는 데이터로 변경하는 동안 다른 사용자는 B라는 아직 완료되지 않은 Dirty 데이터를 읽을 수 있다.
  - Dirty Read : 다른 트랜잭션에서 처리 작업이 끝나지 않았는데 읽을 수 있는 것 (READ_UNCMMITED에서만 발생하는 현상)
    - 예를 들어, 트랜잭션 A가 데이터를 수정하고 있지만 아직 커밋하지 않았는데, 트랜잭션 B가 그 수정 중인 데이터를 읽으면, 트랜잭션 A가 롤백되어 수정이 취소된 경우 B는 잘못된(더 이상 존재하지 않는) 데이터를 읽게 됩니다.
- READ_COMMITTED (level 1)
  - 트랜잭션이 커밋되어 확정된 데이터만 읽는 것을 허용한다. 
  - Dirty Read 현상은 방지할 수 있지만 Non-Repeatable Read를 현상이 발생할 수 있다.
    - Non-Repeatable Read : 한 트랜잭션 내에서 같은 데이터를 두 번 조회했을 때 두 조회 결과가 다른 경우 발생하는 현상이다. 
    - 트랜잭션에서 2번 SELECT를 하고 첫 번째 SELECT와 두 번째 SELECT 사이에 B 트랜잭션에서 수정을 한다면 A 트랜잭션의 두 SELECT 쿼리는 서로 다른 결과를 가지고 있게 된다.
- REPEATABLE_READ (level 2)
  - 트랜잭션이 완료될 때 까지 SELECT 문장이 사용하는 모든 데이터에 shared lock이 걸리므로 다른 사용자는 그 영역에 해당되는 데이터에 대한 `수정이 불가능`하다. (읽기는 가능)
    - 한 트랜잭션 동안 두 번의 SELECT 쿼리가 일어난다면 이 격리 level에서는 그 트랜잭션 동안 데이터 수정이 불가능해서 두 쿼리의 값이 동일하게 되는 것이다.
  - Non-Repeatable을 방지할 수 있지만 여전히 Phantom Read 문제는 발생한다. 
    - Phantom Read : 한 트랜잭션 내에서 같은 쿼리를 두 번 실행했을 때 결과 집합의 행이 다른 경우 발생합니다. 
    - 즉, `Phantom Read`는 `삽입, 삭제`에 관한것이고 `Non-Repeatable Read`는 `수정`에 관한 것이다.
- SERIALIZABLE (level 3)
  - 트랜잭션이 완료될 때까지 삽입, 수정, 삭제가 모두 불가능하다.

#### 각 벤더별 Default 격리 수준

- ORACLE : READ COMMITED
- MySQL : REPEATABLE READ
- SQL Server : READ COMMITED
- PostgreSQL : READ COMMITED


<br/>

### ➕ 읽기에 `@Transactional(readonly=true)`를 붙이는 이유

- JPA의 경우, 해당 옵션을 `true`로 설정하면 트랜잭션이 커밋되어도 [영속성 컨텍스트](https://github.com/jmxx219/CS-Study/blob/main/Java-Spring/ORM.md#%EC%98%81%EC%86%8D%EC%84%B1-%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8persistence-context)를 플러시 하지 않음. 
- 플러시할 때 수행되는 엔티티는 변경 감지를 위한 스냅샵 비교 로직이 수행되지 않으므로 성능을 약간 향상 시킬 수 있음


<br/>


### 동작 원리

- 트랜잭션은 스프링이 지원하는 어노테이션 기반 AOP를 통해 구현되어 있음
    - `import org.springframework.transaction.annotation.Transactional`
- 클래스나 메소드에 @Transactional이 선언되면 해당 클래스에 트랜잭션이 적용된 프록시 객체가 생성됨
- 프록시 객체는 @Transactional이 포함된 메서드가 호출될 경우, 트랜잭션을 시작하고 Commit 또는 Rollback을 수행함
    - `CheckedException` or 예외가 없을 때 `Commit` 수행
    - `UncheckedException`이 발생하면 `Rollback` 수행


<br/>


### 테스트 환경에서 @Transactional 동작
- 반복 가능한 테스트 지원
- 각각의 테스트를 실행할 때마다 트랜잭션을 시작하고 테스트가 끝나면 트랜잭션을 강제로 롤백함
    - 해당 어노테이션이 테스트 케이스에서 사용될 때만 롤백됨


<br/>
