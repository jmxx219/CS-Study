# DB Lock

데이터베이스에서 Lock은 여러 트랜잭션(데이터베이스 작업 단위)이 동시에 데이터에 접근하고 수정하는 상황에서 데이터의 일관성과 무결성을 보장하기 위해 사용되는 개념

Lock의 종류는 락 적용 요소에 따라 크게 두 가지로 나뉘어 진다.

<br/>

### Shared Lock (공유 잠금)

- Row Level Lock
- 데이터를 읽을 때 사용하는 Lock (S로 표기)

- 하나의 트랜잭션이 데이터를 읽고 있는 경우, 다른 트랜잭션들도 해당 데이터를 읽을 수는 있지만, 쓰기 작업은 금지된다. 
- 데이터의 일관성을 유지할 수 있다. 
- 트랜잭션 격리수준 중 Repeatable Read 단계와 연관

<br/>

### Exclusive Lock (배타적 잠금)

- Row Level Lock
- 데이터를 변경하고자 할 때 사용되며, 트랜잭션이 완료될 때 까지 유지된다.  (X로 표기)
- 특정 트랜잭션이 데이터를 수정하고 있는 경우, 해당 데이터에 대한 다른 모든 트랜잭션의 접근이 금지된다. 
- 트랜잭션 격리수준 중 Serializable 단계와 연관

<br/>

### Intention Lock

- MySQL InnoDB 엔진에는 intention lock의 개념도 존재한다. 
- row에 대해서 나중에 어떤 row-level 락을 걸 것을 알려주기 위해 미리 table-level에 걸어두는 lock
- Intention Shared Lock 과 Intention Exclusive Lock 두 가지가 존재한다. 

<br/>

**SELECT … LOCK IN SHARE MODE 이 실행되면**

1. Intention Shared Lock (IS) 이 테이블에 걸림
2. row-level 에 S-Lock 이 걸림

**SELECT … FOR UPDATE, INSERT, DELETE, UPDATE 이 실행되면**

1. intention exclusive lock (IX) 이 테이블에 걸림
2. row-level 에 X-Lock 이 걸림

> IS, IX 락은 여러 트랜잭션에서 동시에 접근 가능하지만, row-level 의 실제 락인 S, X 락에서 접근 제어를 하게 된다.


<br/>

#### Q. row-level 및 table-level 에서 두 번 Lock하는 이유는?

A. 트랜잭션에서 이미 테이블에 대해 락이 걸려있는데, B 트랜잭션에서 해당 테이블의 특정 row에 lock을 거는것을 원천적으로 방지 할 수 있다.
(반대의 경우도 마찬가지)

EX) row-level의 write이 일어나고 있을때 테이블 스키마가 변경되서는 안된다. write query의 경우 이미 IX 락을 획득한 상태이기 때문에 해당 테이블의 스키마가 변경되는것을 막을 수 있다.


|      | X        | IX         | S          | IS         |
| ---- | -------- | ---------- | ---------- | ---------- |
| X    | Conflict | Conflict   | Conflict   | Conflict   |
| IX   | Conflict | Compatible | Conflict   | Compatible |
| S    | Conflict | Conflict   | Compatible | Compatible |
| IS   | Conflict | Compatible | Compatible | Compatible |


<br/>

## Blocking (블로킹)

**Lock간의 경합 (Race Condition)이 발생하여 특정 Transition이 작업을 진행하지 못하고 멈춰선 상태.** 공유 락 끼리는 블로킹이 발생하지 않지만 베타 락은 블로킹을 발생시킨다. 

**블로킹을 해소하기 위해서는 이전 트랜잭션이 완료 (commit / rollback)이 되어야 한다.**

<br/>

### 해결방안

1. SQL문의 리팩토링
2. 트랜잭션을 가능한 짧게 정의
3. 동일한 데이터를 동시에 변경하는 작업을 하지 않도록 설계
4. 대용량 작업이 불가피할 경우, 작업 단위를 쪼개거나 lock_timeout을 설정

<br/>

## Dead Lock (데드락)

두개의 트랜젝션간에 각각의 트랜젝션이 가지고 있는 리소스의 Lock을 획득하려고 할 때 발생

![img](https://blog.kakaocdn.net/dn/KsWNc/btrJWMxiQYq/YyHA42p2v0pMXMmkH9DTZk/img.png)

위와 같이 각각의 트랜잭션에 Lock을 걸고 상대방 Lock에 접근하여 반환 받지 못하는 상황에서 Dead Lock이 발생하게 된다

<br/>

### DeadLock 해결 방법

1. **Dead Lock 이 감지되면 둘 중 하나의 트랜잭션을 강제 종료한다.** 

   실제로, Oracle 에서는 데드락이 감지되면 한쪽 Transaction을 강제로 풀어버린다

   이렇게 되면 하나의 트랜잭션A의 마지막 실행 내용에 오류가 발생되고 커밋이 발생되도록 유지한다

   또 다른 트랜잭션 B는 아직 대기중이며, 트랜잭션 A의 커밋을 기다린다

2. Dead Lock 방지를 위해 접근 순서를 동일하게 하는 것이 중요 -> 접근 순서 규칙을 정한다. 


<br/>

## Lock Level & Escalation

SQL 명령어에 따라서 Lock의 설정대상이 데이터 row일지 database일지 나누어진다. 

### Lock Level

1. **페이지 수준 잠금 (Page-Level Locking)**: 데이터를 페이지 단위로 잠금을 설정하는 것을 의미한다.  즉, 페이지 내에 포함된 여러 레코드가 함께 잠겨서 동시에 접근하지 못하게 된다 .
2. **행 수준 잠금 (Row-Level Locking):** 데이터를 개별 행 단위로 잠금을 설정하는 것을 말한다. 이 경우에는 특정 행만 잠겨서 다른 트랜잭션은 해당 행에 접근할 수 없게 된다.
3. **테이블 수준 잠금 (Table-Level Locking):** 테이블과 인덱스에 모두 잠금을 설정. Select table, Alter table, Vacuum, Refresh, Index, Drop, Truncate 등의 작업에서 해당레벨의 락이 설정된다.
4. **데이터베이스 수준 잠금 (Database-Level Locking)**: 데이터베이스를 복구하거나 스키마를 변경할 때 발생

<br/>

### **Escalation**

- 잠금 수준을 최적화하는 과정

Lock Escalation은 데이터베이스 시스템이 특정 Lock Level에서 다른 Lock Level로 전환하는 과정을 의미한다. 일반적으로 특정 트랜잭션에서 많은 수의 잠금을 설정하려는 경우 시스템은 이를 관리하기 위해 Lock Level을 높일 수 있다. 

 Lock 레벨이 낮을 수록 동시성이 좋아지지만, 관리해야할 Lock이 많아지기 때문에 메모리 효율성은 떨어짐. 반대로 Lock레벨이 높을 수록 관리리소스는 낮지만, 동시성은 떨어진다.

<br/>

## Optimistic Lock (낙관적 락)과 Pessimistic Lock(비관적 락)

### Optimistic Lock

- **데이터 갱신시 충돌이 발생하지 않을 것** 으로 간주하는 방식
- 데이터를 읽을 때 잠금을 설정하지 않고, **데이터를 변경하기 전에 충돌을 검출하는 방식**이다.
- 주로 데이터 충돌이 적은 상황에서 사용

<br/>

### Pessimistic Lock

- **데이터 갱신시 충돌이 발생할 것** 으로 보고 미리 잠금을 하는 방식
- 데이터를 읽을 때나 변경할 때 바로 잠금을 설정하여 다른 트랜잭션의 접근을 막는 방식이다.
- 무결성에 장점이 있지만 데드락의 위험성이 존재한다. 

<br/>

| 구분   | Optimistic Lock                            | Pessimistic Lock                                     |
| ------ | ------------------------------------------ | ---------------------------------------------------- |
| 정의   | 충돌이 없을 것으로 가정하여 락을 걸지 않음 | 충돌을 예상하고 미리 락을 검                         |
| 사용법 | JPA 사용시 @Version                        | Mode 설정 및 쿼리에 직접 사용, DB단에서 설정 가능    |
| 별칭   | 낙관적인 락 / 비선점적인 락                | 비관적인 락 / 선점적인 락                            |
| 장점   | 데드락 가능성이 적으며 성능의 이점         | 충돌에 대한 오버헤드가 줄어들며 무결성을 지키기 용이 |
| 단점   | 충돌이 발생하면 오버헤드 발생              | 충돌이 없으면 오버헤드가 발생                        |

<br/>

### 낙관적 락/비관적 락 VS 공유 락/배타적 락

- 낙관적 락과 비관적 락은 **데이터 충돌의 빈도와 대응 방식**을 나타내며
- 공유 잠금과 배타적 잠금은 **데이터의 읽기와 쓰기 작업 간의 관계**를 나타낸다. 


<br/>

<details>

<summary>참고</summary>

- [DB 락]

  https://kamja-ming.tistory.com/entry/DB-%EB%9D%BDLock%EC%9D%B4%EB%9E%80

  https://chrisjune-13837.medium.com/db-lock-%EB%9D%BD%EC%9D%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80-d908296d0279

- [낙관적 락과 비관적 락]

  https://backtony.github.io/interview/2021-11-23-interview-8/#4-optimistic-vs-pessimistic

- [MySQL 락]

  https://velog.io/@soyeon207/DB-Lock-%EC%B4%9D%EC%A0%95%EB%A6%AC-1-InnoDB-%EC%9D%98-Lock
</details>
