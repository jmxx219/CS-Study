# DB JOIN
<img width="1122" alt="스크린샷 2023-07-07 오전 11 12 33" src="https://github.com/jmxx219/CS-Study/assets/50795805/a9dcbb14-94fe-4723-bc90-7e903ac9b31e">

<Br>

### JOIN
<br>

**개념**
- 두 릴레이션으로부터 관련된 튜플들을 결합하여 하나의 튜플로 만드는 가장 대표적인 데이터 연결 방법

**목적**
- 둘 이상의 테이블을 결합해 하나의 테이블처럼 사용
- 두 개 이상의 릴레이션을 갖는 관계 데이터 베이스에 대해서 릴레이션들 간의 관계를 처리할 수 있음

**특성**
- 데이터베이스에서 테이블 분리로 **데이터 중복 최소화** 와 **데이터의 일관성 유지**

- 질의 처리에서 가장 시간이 많이 소요되는 연산 중 하나

- 어떤 조인 방향이나 조인 방법을 선택할 지라도 결과 집합은 동일하나, 조인하고자 하는 두 집합의 데이터 상황에 따라 조인방향, 조인 방법에 따른 수행 속도의 영향이 큼




<Br>
<br>

## DB Join의 종류

### INNER JOIN과 OUTER JOIN
- JOIN은 크게 INNER JOIN과 OUTER JOIN으로 구분된다
    - INNER JOIN : `INNER JOIN`, CROSS JOIN, EQUI JOIN, NON-EQUI JOIN, NATURAL JOIN
    - OUTER JOIN : `LEFT JOIN`, `RIGHT JOIN`, `FULL OUTER JOIN`

- #### OUTER JOIN
    - 두 테이블 간의 교집합이 되는 데이터뿐만 아니라 **해당되지 않는 값**까지 가져옴
    - **드라이빙 테이블**이 필요하다
        - `기준 테이블(드라이빙 테이블)`의 모든 정보는 가져옴
        - `대상 테이블`은 기준 테이블과 연결되는 행을 포함하는 정보 가져옴
    - 데이터의 개수: 기준이 되는 테이블의 데이터 수
    - 일반적으로 LEFT(RIGHT) JOIN과 LEFT(RIGHT) OUTHER JOIN은 같은 의미로 사용한다

<br>

> 드라이빙 테이블 : 처음으로 가져오는, 기준이 되는 테이블


<br>
<br>

### 대표적인 DB JOIN
<br>

### INNER JOIN

- `( A ∩ B )`
- 테이블 간 JOIN 조건을 만족하는 행만 반환
    - 공통적인 부분만 SELECT
- 데이터의 개수 : 데이터의 교집합 부분
- MySQL에서는 JOIN, INNER JOIN, CROSS JOIN이 모두 같은 의미로 사용 됨
- 어느 테이블을 기준으로 JOIN하든 JOIN관계에 부합하는 레코드를 모두 가짐

```sql
SELECT A.ID, A.ENAME, A.KNAME
FROM A INNER JOIN B
ON A.ID = B.ID;
```

<br>


<br>

### LEFT (OUTER) JOIN
- `( (A ∩ B) ∪ (A - B) )`
- 조인 기준이 왼쪽으로, 왼쪽 테이블은 모두 SELECT 됨
    - 왼쪽 테이블 + 교집합
- 왼쪽 테이블의 모든 레코드가 결과에 포함되며, 오른쪽 테이블에서 일치하는 레코드가 없는 경우에는 NULL 값으로 채워짐

- EXCLUSIVE LEFT JOIN
    - 왼쪽 테이블의 데이터만 가져오는 것

```sql
SELECT A.ID, A.ENAME, A.KNAME
FROM A LEFT OUTER JOIN B
ON A.ID = B.ID;
```

<br>
<br>

### RIGHT (OUTER) JOIN
- `( (A ∩ B) ∪ (A - B) )`
- 조인 기준이 오른쪽으로, 오른쪽 테이블은 모두 SELECT 됨
    - 오른쪽 테이블 + 교집합
- 오른쪽 테이블의 모든 레코드가 결과에 포함되며, 왼쪽 테이블에서 일치하는 레코드가 없는 경우에는 NULL 값으로 채워짐

```sql
SELECT A.ID, A.ENAME, A.KNAME
FROM A RIGHT OUTER JOIN B
ON A.ID = B.ID;
```

<br>
<br>

### EXCLUSIVE JOIN

` 위 그림의 LEFT, RIGHT JOIN에서  초승달 모양에 해당하는 JOIN`

- 어느 특정 테이블에 있는 레코드만 가져오는 것
- 두 테이블 조인시, 둘 중 한가지 테이블에만 있는 데이터를 가져옴 
    - 왼쪽/오른쪽 테이블 - 교집합
- 별도의 EXCLUSIVE JOIN함수가 존재하는 것이 아닌 LEFT, RIGHT JOIN과 WHERE절의 조건을 함께 사용해 만드는 조인 문법


```sql
SELECT A.ID, A.ENAME, A.KNAME
FROM A LEFT OUTER JOIN B
ON A.ID = B.ID;
WHERE B.ID IS NULL  // 조인한 B 테이블의 값이 null만 출력 >> 조인이 안된 A 레코드 나머지값만 출력
```

<br>

> **LEFT, RIGHT JOIN시 주의점**
>1. INNER JOIN과는 달리 조인하는 테이블의 순서가 상당히 중요하다.
>2. 조인 순서의 일관성을 위해 LEFTRIGHT JOIN을 쓰다가 갑자기 INNER JOIN 이나 다른 조인을 사용하지 않는다.


<br>
<br>

### FULL OUTER JOIN
- 두 개의 테이블에서 일치하는 레코드뿐만 아니라 양쪽 테이블에 일치하지 않는 레코드도 모두 포함하는 JOIN 연산
- 일치하는 레코드가 없는 경우에도 NULL 값으로 채워짐
- 대부분의 DB는 FULL OUTER JOIN을 지원하지 않는다
    - 하지만 간접적으로 구현하는 방법이 존재한다
    (LEFT 조인한 테이블과 RIGHT조인한 테이블을 UNION 합집합)

`FULL OUTER JOIN`
```sql
SELECT *
FROM A FULL OUTER JOIN B
ON A.ID = B.ID;
```

<Br>

`LEFT 조인한 테이블과 RIGHT조인한 테이블을 UNION 합집합으로 구현`
```sql
SELECT * from A LEFT JOIN B on A_ID = B.ID
UNION
SELECT * from A RIGHT JOIN B on A_ID = B.ID
```


<br>
<br>

### UNION JOIN
- 여러개의 SELECT문의 결과를 하나의 테이블이나 결과 집합으로 합치는 연산
- 각각의 SELECT문으로 선택된 **필드의 개수**와 **타입**은 모두 같아야하며 **필드순서** 또한 같아야함
- 중복된 행을 제거하고 유일한 결과 반환
    - 기본 집합 쿼리에는 (DISTINCT) 중복제거가 자동 포함되어있음
    - 중복되는 레코드까지 모두 출력하고 싶다면 `UNION ALL` 키워드 사용

```sql
SELECT 필드이름 from 테이블A
UNION
SELECT 필드이름 from 테이블B
```

<br>

>**FULL OUTER JOIN**은 두 개의 테이블을 조인하여 모든 레코드를 포함하는 결과를 생성하는 반면, **UNION JOIN**은 두 개의 SELECT 문의 결과를 합치는 것을 의미

<br>

FMI - [JOIN의 내부적 구현 방식에 대한 세부내용](https://velog.io/@impala/DB-Join-Operation)

---
### 참고

[세 가지 JOIN 계획 정리](https://s2choco.tistory.com/32)