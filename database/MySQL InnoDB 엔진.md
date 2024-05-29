
# MySQL InnoDB 스토리지 엔진

> [MySQL 전체 구조 참고](https://github.com/jmxx219/CS-Study/blob/main/database/MySQL%20%EC%97%94%EC%A7%84%20%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98.md#mysql-%EC%A0%84%EC%B2%B4-%EA%B5%AC%EC%A1%B0)

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F7U5Z1%2FbtsjpE5ezxt%2FQnRLoKb6JBQR8oq2AfRY8K%2Fimg.png" width="500" height="300">

- MySQL 서버는 크게 `MySQL 엔진`과 `스토리지 엔진`으로 구성되고, 스토리지 엔진 중에 대표적으로 `InnoDB 스토리지 엔진`이 존재함
- 스토리지 엔진은 MySQL 서버에서 실제 데이터를 디스크 스토리지에 저장하거나 디스크 스토리지로부터 데이터를 읽어오는 역할을 전담함
  - MySQL 엔진과 플러그인 형태로 연동/분리 가능하고 핸들러 API(핸들러 요청)를 통해 스토리지 엔진에 읽기/쓰기 요청이 가능함

<br>
<br>


## InnoDB 스토리지 엔진

<img src="https://github.com/jmxx219/CS-Study/assets/52346113/429766df-8fa2-4a7c-b687-d52dcf18ed3d" width="500">

- MySQL 서버의 다른 스토리지 엔진과 달리 트랜잭션과 잠금, MVCC(Multi-Version Concurrency Control) 등의 기능을 내장하고 있다.
- MySQL 서버에서는 `InnoDB` 스토리지 엔진을 기본 스토리지 엔진으로 사용한다.

<br>

### 구조
- 메모리 영역
  - InnoDB 버퍼 풀: 실제 데이터 블록(페이지)을 메모리에 적재하는 영역 + 인서트 버퍼 + 언두 레코드
  - 로그 버퍼: 로그 스레드에 의해 로그 파일로 기록되기 전 버퍼링 하는 영역
- 디스크 영역
  - 시스템/사용자 테이블 스페이
  - 리두(Redo) 로그

<br>
<br>

## InnoDB 스토리지 엔진의 특징

### 1. PK에 의한 클러스터링

- 기본적으로 InnoDB의 모든 테이블은 기본적으로 PK를 기준으로 클러스터링되어 저장됨
  - 여기서 클러스터링된다는 것은, PK 값이 비슷한 레코드끼리 묶어서 저장하는 것을 의미함
  - 이처럼 PK 값이 레코드의 저장 위치를 결정하기 때문에 신중하게 결정해야 함
- PK가 클러스터링 인덱스이기 때문에 PK를 통한 Range 스캔이 매우 빠르게 처리될 수 있음

<br>

### 2. 외래 키(FK) 지원

- InnoDB 스토리지 엔진 레벨에서 지원하는 기능으로, MyISAM이나 MEMORY 테이블에서는 사용할 수 없음
- InnoDB에서 외래 키는 부모 테이블과 자식 테이블 모두 해당 컬럼에 인덱스 생성이 필요하고, 변경 시에는 반드시 부모 테이블이나 자식 테이블에 데이터가 있는지 체크하는 작업이 필요함
- 따라서 잠금이 여러 테이블로 전파되고, 그로 인해 데드락이 발생할 때가 많으므로 개발 시에 외래 키의 존재에 주의하는 것이 좋음

<br>

### 3. MVCC(Multi Version Concurrency Control)

- 개념
  - 동시 접근을 허용하는 데이터베이스에서 동시성을 제어하기 위해 사용하는 방법 중 하나
    - 여기서 `Multi Version`은 하나의 레코드에 대해서 여러 개의 버전을 동시에 관리한다는 의미함
  - 일반적으로 레코드 레벨의 트랜잭션을 지원하는 DBMS가 지원하는 기능
    - 여러 트랜잭션이 동시에 같은 데이터에 접근할 때, 데이터의 일관성과 동시성을 보장하는 방식
    - 트랜잭션 동시성과 일관성을 보장하기 위해 여러 버전의 데이터를 관리하는 방식
- 핵심
  - 잠금(Locking)을 사용하지 않으면서 일관된 값을 읽도록 만드는 것이 목적
    - `InnoDB` 스토리지 엔진은 트랜잭션의 격리 수준을 위해 잠금을 사용하지 않고 읽기 작업을 수행함
    - 즉, 잠금을 사용하지 않고 하나의 레코드에 대해 여러 개의 버전이 동시에 관리될 수 있다는 것을 의미함
  - InnoDB의 경우 [언두 로그](https://dev.mysql.com/doc/refman/8.0/en/innodb-undo-tablespaces.html)를 활용하여 이 기능을 구현함

<br>

#### 언두 로그(Undo Log)

- DML(UPDATE, DELETE)과 같이 데이터를 변경하는 쿼리로 데이터를 변경했을 때 변경되기 이전 데이터를 보관하는 공간, 백업해둔 데이터
  - 롤백 용도나 트랜잭션 격리 수준을 유지하면서 높은 동시성을 제공하기 위한 용도로 사용됨
- 기능
  - **트랜잭션 보장**: 트랜잭션이 롤백되면 언두 로그에 백업해둔 이전 버전의 데이터를 이용해 복구함
  - **격리 수준 보장**: 데이터를 변경하는 도중 다른 커넥션에서 데이터를 조회하면 격리 수준에 맞게 언두 로그에 백업해둔 데이터를 읽어서 반환함

<br>

#### 언두 로그 영역을 활용한 MVCC

- MySQL은 레코드를 여러 버전으로 관리하는 방법으로 트랜잭션의 커밋/롤백 여부와 상관없이 변경 이전 레코드의 데이터를 Undo 로그에 남겨두는 방법을 사용함
- Undo 로그를 남겨서 여러 버전으로 관리하면 격리 수준에 따라 Lock(잠금)을 사용하지 않고 일관된 조회가 가능함
  - `READ_UNCOMMITTED`: InnoDB 버퍼 풀이 현재 가지고 있는 최신 버전의 데이터를 읽어서 반환함
    - 즉, 커밋됐든 아니든 변경된 상태의 데이터를 반환함
  - `READ_COMMITTED`나 **그 이상의 격리 수준**인 경우: 아직 커밋되지 않았기 때문에 InnoDB 버퍼 풀이나 데이터 파일에 있는 내용 대신 변경되기 이전의 내용을 보관하고 있는 언두 영역의 데이터를 반환함
    - Undo 로그에 저장된 레코드 값으로 레코드를 조회하면 굳이 레코드의 Lock을 걸 필요가 없이 일관된 읽기가 가능해짐
  
   
- `UPDATE` 쿼리가 실행되면 어떻게 될까?
  ```SQL
  update member set password='realpassword' where id = 1;
  ```

  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FmLFMy%2Fbtsjk5JJaFM%2F2HwHehXQsABwtqC2AyXY0K%2Fimg.png" width="500">

  - `UPDATE` 쿼리가 실행되면 커밋 실행 여부와 관계없이 변경 이전의 값을 undo 로그에 저장하고, InnoDB 버퍼 풀에는 새로운 값으로 업데이트 됨
  - 아직 커밋이나 롤백이 되지 않은 이 상태에서 다른 사용자가 작업 중인 레코드를 조회(SELECT)하게 되면 격리 레벨에 따라서 어디에 있는 데이터를 조회할지 달라짐

> 이렇게 하나의 레코드에 대해서 여러 개의 버전 (버퍼 풀, 언두 로그)를 관리하기 때문에 'MVCC'라고 표현하며, 필요에 따라 어떤 데이터가 보여질지 달라지게 된다는 것을 의미함

<br>

### 4. 잠금 없는 일관된 읽기(Non-Locking Consistent Read)

> **MVCC를 사용해 잠금없는 일관된 읽기 제공**

- 읽기작업은 다른 트랜잭션의 잠금에 대기하지 않고 바로 실행 됨
  - 순수한 읽기 작업(SELECT)이 다른 트랜잭션의 변경 작업에 영향을 받지 않음
    - 특히 SERIALIZABLE이 아닌 격리 수준(`READ_UNCOMMITTED`, `READ_UNCOMMITTED`, `REPEATABLE_READ`)일때
- 활성 트랜잭션이 길어질 시, 오랜 시간 동안 활성 상태인 MySQL 서버 속도저하 문제 발생 가능
  - 원인: 잠금 없는 일관된 읽기의 특성(`언두 로그를 지속적으로 유지`) 때문
  - 따라서 트랜잭션 시작 시, 롤백이나 커밋을 통해 가능한 한 빠르게 트랜잭션 완료 권장


<br>

### 5. 자동 데드락 감지

> InnoDB 스토리지 엔진은 내부적으로 잠금이 교착 상태에 빠지지 않았는지 체크하기 위해, 잠금 대기 목록을 그래프(Wait-for List) 형태로 관리함
  
#### 데드락 감지 시스템 (`innodb_deadlock_detect`)

- 주기적인 잠금 대기 그래프 검사로 교착 상태에 빠진 트랜잭션들을 찾아 그 중 하나를 강제 종료
- `트랜잭션 강제 종료 판단 기준` : 트랜잭션의 언두 로그 양
  - 언두 로그 레코드를 더 적게 가진 트랜잭션이 일반적으로 롤백의 대상이 됨
  - 이유: 적은 언두로그 레코드 → 롤백 시 언두처리할 내용 적음 → 트랜잭션 강제 롤백으로 인한 MySQL 서버의 부하 덜 유발
- 데드락 감지 스레드의 성능
  - 느려질 경우
    - 서비스 쿼리를 처리 중인 스레드는 더는 작업 진행을 못하고 대기하면서 서비스에 악영향 미침
  - 동시성 처리의 개수에 따라 영향을 받음

<br>

### 6. 자동화된 장애 복구

> 데이터베이스 시스템이 예기치 않게 종료되었을 때 자동으로 장애 복구 수행.

- InnoDB 데이터 파일은 기본적으로 MySQL 서버가 시작될 때 항상 자동 복구 수행
- 이 단계에서 자동으로 복구될 수 없는 손상 있을 경우 자동 복구 멈추고 MySQL 서버 종료 됨
- 종료되었을 시, MySQL 서버의 설정 파일의 `innodb_force_recovery` 시스템 변수 설정으로 MySQL 서버 시작
  - `innodb_force_recovery`: 시스템 변수를 1~6으로 설정하여 MySQL 서버를 다시 시작

<br>
<br>


## InnoDB 메모리 구조


### InnoDB 버퍼 풀

> InnoDB 스토리지 엔진에서 가장 핵심적인 부분

- 데이터 캐싱
  - 버퍼 풀은 디스크의 데이터 파일이나 인덱스 정보를 메모리에 캐시해두는 공간
  - 페이지 단위로 테이블 데이터를 관리한다
  - 페이지 교체 알고리즘으로 [LRU](https://en.wikipedia.org/wiki/Page_replacement_algorithm#Least_recently_used)를 사용한다
- 쓰기 지연 버퍼
  - 버퍼 풀은 쓰기 작업을 지연시켜 일괄 작업으로 처리할 수 있게 해주는 버퍼 역할을 수행함
    - 변경된 데이터는 즉시 디스크에 쓰여지지 않고 버퍼 풀에 일시적으로 저장됨
    - 이후 적절한 시점에 이 데이터들이 일괄적으로 디스크에 쓰여짐
    - 이 과정을 통해 랜덤 I/O 작업을 줄이고, 시스템의 성능을 최적화함

<br>

### Adaptive Hash Index

- 페이지에 빠르게 접근하기 위한 해시 자료구조 기반 인덱스
- < 인덱스, 페이지 주소 값> 쌍으로 구성됨
자주 요청되는 페이지에 대해서 InnoDB가 자동으로 생성하는 인덱스

<br>

### 체인지 버퍼


<br>

### 로그 버퍼


<br>
<br>


> ### ➕ InnoDB 디스크 구조  
> [MySQL Architecture - InnoDB: On-Dist Structure](https://blog.ex-em.com/1699) 참고

<br>
<br>

### Ref
- [Real MySQL 04. 아키텍처 - 2. InnoDB 스토리지 엔진 아키텍처](https://leekoby.github.io/posts/real-my-sql-04-2-innodb-storage-engine-architecture/#-423-mvccmulti-version-concurrency-control)
- [[MySQL] InnoDB 스토리지 엔진 아키텍처 1](https://hyuuny.tistory.com/195)
- [MySQL 아키텍처](https://jeong-pro.tistory.com/239)
