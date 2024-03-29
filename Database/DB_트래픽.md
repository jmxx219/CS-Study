## 0. 하나의 데이터베이스로 구성이 돼 있으면?

- 트래픽이 적으면 하나의 데이터베이스에 CRUD 작업을 모두 수행해도 괜찮다. 하지만 트래픽이 증가하면 문제가 발생한다.

<br/>

### 문제점

- 트래픽으로 인해 데이터베이스 장애 발생확률이 높아지고 그로 인하여 운영 중인 애플리케이션에 바로 타격이 갈 수 있다. → 가용성의 문제
- 트래픽으로 인해 쿼리 수행 (수행시간이 비교적 긴 쿼리/쓰기 작업) 빈도가 높아지면 작업 병목이 발생할 수 있다. → 부하분산의 문제
    - 애플리케이션 단에서는 DB connection을 얻기 위해 대기하다가 TimeOut 발생

<br/>

### 대안점

- 데이터베이스 다중화를 하는 방식이 있다.
  
<br/>

# 1. 데이터베이스 다중화

## 1.1 Clustrering

- DB 서버를 두 대를 하나로 묶어서 운영한다.
- 다른 서버가 복구하는 동안 다른 서버가 동작함으로 서비스 중단없이 사용할 수 있다.
- 또한 가해지는 부하가 두 개로 나눠지므로 CPU와 memory 부하도 줄어든다.

<br/>

### 단점

- 여러 개의 서버가 하나의 스토리지를 공유해, 병목현상이 발생할 수 있다.
    - 한 서버는 Active 상태, 다른 한 서버는 Stand-by 상태로 둔다.
    - 서버 문제 발생 시, Fail over를 하여 Stand-by 서버를 Active로 전환해 문제에 대응할 수 있다.


<br/>

### Active & Stand-by 문제점

- Fail over가 발생하는 시간 동안에는 서비스가 중단된다.
    - 한대로 운영하기 때문에 효율이 1/2 정도가 된다.

 <br/>

## 1.2 Replication

- clustering과 달리 서버와 스토리지 모두를 확장
- master-slave 관계를 설정, master 서버에는 데이터 원본, slave 서버에는 복제본을 저장하는 방식

<br/>

### 장점

1. 가용성이 높아진다.
    1. Master DB가 장애로 죽을 경우, 슬레이브 서버를 마스터로 승격시켜 서비스를 빠르게 복구할 수 있다.
    
2. 부하분산이 가능하다.
    1. Master DB에서는 Insert, Update, Delete 작업을 Slave DB에서는 Select 작업을 처리해 부하를 분산시킬 수 있다.
    2. 인덱스가 걸려있는 테이블의 write 작업이 read 작업보다 상대적으로 더 많은 시간이 걸리고, 웹 서비스는 일반적으로 Write 보다는 Read의 작업 비중이 높다. 

<br/>

### 단점

- 서로 다른 서버로 운영함으로 버전 관리 필요
- 데이터를 동기화하는 방식이 비동기 방식이라, 일관성 있는 데이터를 얻지 못할 수 있다.
    - 동기방식은 속도가 느려진다.
- master 서버가 다운되면 복구 및 대처가 힘들다.

<br/>

데이터베이스 서버 내의 데이터 규모가 커진다면 scale up 방식으로는 고비용, SPOF, 증설 용량 한계 등의 문제가 있다. 그래서 이런 경우 수평적으로 증설하게 된다.


# 2. 샤딩

- 데이터베이스를 수평적으로 확장
- 하나의 데이터베이스를 여러개로 쪼갠다.
- 샤드에 있는 데이터는 중복되지 않는다.

<br/>

### 구성 시 고려해야 하는 점

- 재사딩
    - 데이터가 많아 샤드가 더 필요한 경우
    - 샤드간 데이터 분포가 불균형한 경우

<br/>

- 유명인사 문제
    - 조회가 많은 레코드가 한 샤드에 몰려있는 경우
        - 하나의 샤드에만 트래픽이 몰릴 수 있다.
    - 조회가 많은 레코드를 여러 샤드에 분산해서 할당
  
<br/>
  
- 조인, 비정규
    - 샤딩 후 데이터 조인이 힘들어진다.
    - 데이터베이스를 비정규화 해, 하나의 테이블에서 쿼리가 수행할 수 있도록 할 수 있다.

<br/>

### 샤딩을 적용할 때 NoSQL 추천

- join이 안되기 때문에 RDBMS의 장점이 사라진다.
- 응답 지연시간이 낮아야 할 경우
- 관계형 데이터가 아닐 경우
- 데이터를 직렬화/역직렬 하기만 하는 경우
- 아주 많은 데이터를 저장하는 경우
- 트래픽 양에 따른 규모 확장을 빠르고 손쉽게 하고 싶은 경우

<br/>

# 3. caching

- 값비싼 연산이나 자주 참조되는 데이터를 메모리 안에 둬서 후에 이어지는 request를 빠르게 처리될 수 있도록 한다.
- 데이터베이스의 호출 횟수를 줄일 수 있어 성능 개선과 DB 부하를 줄일 수 있다.

<br/>

### 사용 시 고려해야 하는 점

1. 데이터 갱신이 자주 일어나지 않는 데이터가 캐시 사용에 적합하다.
2. 휘발성 메모리이기 때문에 영속성으로 저장해야 하는 데이터는 db에 저장해야 한다.
3. 적절한 만료 기간이 필요하다. 만료 기간이 짧으면 db에서 데이터 호출을 자주 발생하고, 만료 기간이 길면 원본과 캐시에 저장된 데이터가 다를 수 있다. 
4. 일관성을 유지하기 위해서는 하나의 트랜잭션 내에서 데이터를 저장하는 연산과 캐시를 갱신하는 연산이 일어나야 한다.
5. 캐시 서버를 분산시켜 장애에 대응해야 한다.
6. 캐시가 가득 찬 경우 어떤 데이터를 내보낼지 정해야 한다.
    1. 대표적인 알고리즘 LRU, LFU, FIFO
7. 캐시 메모리의 공간이 작으면 저장된 데이터가 자주 바꿔 성능이 떨어진다.
