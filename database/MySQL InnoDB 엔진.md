# MySQL InnoDB 스토리지 엔진

## InnoDB 스토리지 엔진

<img src="https://dev.mysql.com/doc/refman/8.0/en/images/innodb-architecture-8-0.png" width="500" height="300">

- MySQL 서버에서 실제 데이터를 디스크 스토리지에 저장하거나 디스크 스토리지로부터 데이터를 읽어오는 역할을 전담한다.
- MySQL 서버의 다른 스토리지 엔진과 달리 트랜잭션과 잠금, MVCC(Multi-Version Concurrency Control) 등의 기능을 내장하고 있다.
- MySQL 서버에서는 `InnoDB` 스토리지 엔진을 기본 스토리지 엔진으로 사용한다.

<br>

## 특징

### PK에 의한 클러스터링

- InnoDB의 모든 테이블은 기본적으로 PK를 기준으로 클러스터링된다. 즉, PK 값 순서대로 디스크에 저장된다.
- PK(클러스터링) 인덱스가 자동 생성된다.
- PK를 통해서만 레코드에 접근 가능함
- PK를 통한 레인지 검색이 매우 빠름
- 클러스터링 떄문에 쓰기 성능 저하

<br>

### MVCC

- `InnoDB` 스토리지 엔진은 트랜잭션의 격리 수준을 위해 잠금을 사용하지 않고 읽기 작업을 수행한다.
    - 즉, 잠금을 사용하지 않고 하나의 레코드에 대해 여러 개의 버전이 동시에 관리될 수 있다는 것을 의미한다.
- `InnoDB`는 [언두 로그](https://dev.mysql.com/doc/refman/8.0/en/innodb-undo-tablespaces.html)를 사용해 이 기능을 구현한다.

<br>

#### 언두 로그 영역을 활용한 MVCC

![](https://github.com/dragonappear/learn/assets/89398909/3a14f761-519e-4770-b22a-1a6928428535)

`UPDATE` 쿼리가 실행되면 커밋 실행 여부와 관계없이 InnoDB 버퍼풀은 새로운 값으로 업데이트 된다. 디스크의 데이터 파일에는 체크포인트나 InnoDB의 Write 스레드에 의해 새로운 값으로 업데이트 되었을수도 있고 아닐 수도 있다.

아직 커밋이나 롤백이 되지 않은 상태에서 다른 사용자가 작업 중인 레코드를 조회하면 어떻게 데이터가 조회될까? 이 질문의 답은 MySQL 서버의 시스템 변수(`transaction_isolation`)에 설정된 격리 수준에 따라 달라진다.

- `READ_UNCOMMITTED`: InnoDB 버퍼 풀이 현재 가지고 있는 변경된 데이터를 읽어서 반환. 즉, 커밋됐든 아니든 변경된 상태의 데이터를 반환한다
- `READ_COMMITTED`나 그 이상의 격리 수준인 경우 : 아직 커밋되지 않았기 때문에 InnoDB 버퍼 풀이나 데이터 파일에 있는 내용 대신 변경되기 이전의 내용을 보관하고 있는 언두 영역의 데이터를 반환

<br>

### 레코드 단위 잠금 제공

- 레코드 단위로 잠금을 걸기 때문에 동시 처리 성능이 좋다.
  - 페이지나 테이블 레벨의 잠금에 비해 세밀하게 잠금을 걸 수 있기 때문이다.
  - 한 사용자가 특정 레코드를 수정하고 있는 동안, 다른 사용자는 동일 테이블의 다른 레코드를 동시에 조회하거나 수정할 수 있다.
- 정확하게는 레코드 자체를 잠그는 것이 아니라, 인덱스를 잠근다.
- 인덱스를 어떻게 설정하는지에 따라 잠금의 범위가 달라질 수 있다. 따라서 인덱스는 신중하게 설정해야한다.


<br>

### 버퍼 풀

<img src="https://velog.velcdn.com/images/dragonappear/post/8dcd6b3e-ebeb-44f4-98dc-867a0829ab0a/image.png" width="500" height="300">

#### 데이터 캐싱

- 버퍼 풀은 디스크의 데이터 파일이나 인덱스 정보를 메모리에 캐시해두는 공간
- 페이지 단위로 테이블 데이터를 관리한다
- 페이지 교체 알고리즘으로 [LRU](https://en.wikipedia.org/wiki/Page_replacement_algorithm#Least_recently_used)를 사용한다

#### 쓰기지연 버퍼
- 버퍼 풀은 쓰기 작업을 지연시켜 일괄 작업으로 처리할 수 있게 해주는 버퍼 역할을 수행한다.
  - 변경된 데이터는 즉시 디스크에 쓰여지지 않고 버퍼 풀에 일시적으로 저장된다. 
  - 이후 적절한 시점에 이 데이터들이 일괄적으로 디스크에 쓰여진다. 
  - 이 과정을 통해 랜덤 I/O 작업을 줄이고, 시스템의 성능을 최적화한다.

<br>

### Adaptive Hash Index

<img src="https://velog.velcdn.com/images/dragonappear/post/9c1792ef-5526-4acf-a9a9-905cea5ff003/image.png" width="500" height="300">

- 페이지에 빠르게 접근하기 위한 해시 자료구조 기반 인덱스
- < 인덱스, 페이지 주소 값> 쌍으로 구성됨
자주 요청되는 페이지에 대해서 InnoDB가 자동으로 생성하는 인덱스