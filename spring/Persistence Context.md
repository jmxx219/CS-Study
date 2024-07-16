# Persistence Context

- 자바 애플리케이션에서 EntityManager를 통해 엔티티의 생명 주기 및 범위를 관리하는 환경
- @PersistenceContext - JPA에서 EntityManager를 주입하기 위해 사용되는 어노테이션

<br>

## EntityManager

- JPA에서 가장 중요한 인터페이스 중 하나
- 엔티티를 관리하고 DB 연산(CRUD)을 수행
- 엔티티 인스턴스의 생명 주기 관리

<br>

## 사용 예시

```java
import javax.persistence.EntityManager;
import javax.persistence.PersistenceContext;
import org.springframework.stereotype.Repository;

@Repository
public class MyRepository {

    @PersistenceContext
    private EntityManager entityManager;

    public void doSomething() {
        // EntityManager를 사용하여 데이터베이스 연산 수행
        MyEntity entity = entityManager.find(MyEntity.class, 1L);
    }
}
// 실제로 이런식으로 활용하진 않는다.
```

<br>


## 엔티티의 생명주기

- 비영속(new/transient): 영속성 컨텍스트와 전혀 관계가 없는 상태
- 영속(managed): 영속성 컨텍스트에 저장된 상태
- 준영속(detached): 영속성 컨텍스트에 저장되었다가 분리된 상태
- 삭제(removed): 삭제된 상태

![https://velog.velcdn.com/images/neptunes032/post/ecd3b113-862f-4158-a208-e1eeec92d61d/image.png](https://velog.velcdn.com/images/neptunes032/post/ecd3b113-862f-4158-a208-e1eeec92d61d/image.png)

<br>


## 주요 메소드

1. **`persist(Object entity)`**: 새로운 엔티티를 영속성 컨텍스트에 추가합니다.
2. **`find(Class entityClass, Object primaryKey)`**: 기본 키를 사용하여 데이터베이스에서 엔티티를 찾습니다.
3. **`merge(Object entity)`**: 분리된 엔티티의 변경 사항을 영속성 컨텍스트에 병합합니다.
4. **`remove(Object entity)`**: 엔티티를 삭제합니다.
5. **`flush()`**: 영속성 컨텍스트의 변경 사항을 데이터베이스에 반영합니다.

<br>

## 주요 특징

- @PersistenceContext로 주입된 EntityManager는 트랜잭션 범위를 가진다.
- 트랜잭션 시작시 EntityManager 생성, 트랜잭션 종료시 EntityManager 종료 (생명주기)
- Spring은 EntityManagerFactory를 관리하며 이를 통해 필요한 EntityManager를 제공
- 엔티티를 식별자 값으로 구분 - 영속 상태는 식별자 값이 반드시 있어야 함
- 1차 캐시
    - 영속 상태의 엔티티가 저장되는 공간, 조회시 존재하지 않으면 이곳에 DB에서 조회
- 동일성 보장
    - 엔티티의 동일성을 보장한다.
    
    ```java
    Member a = em.find(Member.class, "member1");
    Member b = em.find(Member.class, "member1");
    System.out.print(a==b) // true
    
    // 동등성 비교 : 실제 인스턴스는 다를 수 있지만 인스턴스가 가지고 있는 값이 같다. 
    // equals()메소드를 구현해서 비교한다.
    ```
    
- 쓰기 지연
    - 엔티티를 저장하더라도 바로 INSERT Query를 날리지 않고 내부 쿼리 저장소에 SQL을 모아두고 트랜잭션 커밋 시 쿼리를 DB에 날린다.
- 변경 감지
    - 트랜잭션 내의 엔티티와 스냅샷을 비교하여 변경된 엔티티를 찾는다 (Dirty Check)
    - 변경된 엔티티 발견시 수정 쿼리 생성하여 쓰기 지연 SQL 저장소에 저장
    - 변경 감지는 영속성 컨텍스트가 관리하는 영속 상태의 엔티티만 적용
    - 예시
    
    ```java
    EntityManager em = emf.createEntityManager();
    EntityTransaction transaction = em.getTransaction();
    transaction.begin();
    
    Member member = em.find(Member.class, "member1");
    member.setName("노영삼");
    
    transaction.commit();
    ```
