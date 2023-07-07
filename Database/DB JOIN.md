DB Join이 무엇인지 설명하고, 각각의 종류에 대해 설명해 주세요.          
사실, JOIN은 상당한 시간이 걸릴 수 있기에 내부적으로 다양한 구현 방식을 사용하고 있습니다. 그 예시에 대해 설명해 주세요.            
그렇다면 입력한 쿼리에서 어떤 구현 방식을 사용하는지는 어떻게 알 수 있나요?         
앞 질문들을 통해 인덱스의 중요성을 알 수 있었는데, 그렇다면 JOIN의 성능도 인덱스의 유무의 영향을 받나요?

# DB JOIN
<img width="1122" alt="스크린샷 2023-07-07 오전 11 12 33" src="https://github.com/jmxx219/CS-Study/assets/50795805/a9dcbb14-94fe-4723-bc90-7e903ac9b31e">

<Br>

#### DB JOIN (SQL JOIN)
: 데이터베이스에서 테이블들을 결합하는 작업을 일컬음

#### JOIN
: 데이터베이스 내 여러 테이블에서 가져온 레코드를 조합하여 하나의 테이블이나 결과 집합으로 표현

- 두 테이블의 조인을 위해서는 기본키와 외래키 관계로 맺어져야 함 → `일대다 관계`

<Br>
<br>

## DB Join의 종류

### INNER JOIN과 OUTER JOIN
- JOIN은 크게 INNER JOIN과 OUTER JOIN으로 구분된다
    - INNER JOIN : `INNER JOIN`, `CROSS JOIN`, `EQUI JOIN`, `NON-EQUI JOIN`, `NATURAL JOIN`
    - OUTER JOIN : `LEFT JOIN`, `RIGHT JOIN`, `FULL OUTER JOIN`

- #### OUTER JOIN
    - 두 테이블 간의 교집합이 되는 데이터뿐만 아니라 **해당되지 않는 값**까지 가져옴
    - *드라이빙 테이블이 필요하다
        - `기준 테이블(드라이빙 테이블)`의 모든 정보는 가져옴
        - `대상 테이블`은 JOIN조건이 일치하지 않아도 가져옴 
    - 데이터의 개수: 기준이 되는 테이블의 데이터 수
    - 일반적으로 LEFT/RIGHT JOIN, LEFT/RIGHT OUTHER JOIN은 같은 의미로 사용한다

<br>

`드라이빙 테이블 : 처음으로 가져오는, 기준이 되는 테이블`


<br>
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

#### **EXCLUSIVE JOIN**
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

### FULL OUTER JOIN
- 두 개의 테이블에서 일치하는 레코드뿐만 아니라 양쪽 테이블에 일치하지 않는 레코드도 모두 포함하는 JOIN 연산
- 일치하는 레코드가 없는 경우에도 NULL 값으로 채워짐
- 대부분의 DB는 FULL OUTER JOIN을 지원하지 않는다
    - 하지만 간접적으로 구현하는 방법이 존재한다
    (LEFT 조인한 테이블과 RIGHT조인한 테이블을 UNION 합집합)

```sql
SELECT *
FROM A FULL OUTER JOIN B
ON A.ID = B.ID;
```

```sql
SELECT * from A LEFT JOIN B on A_ID = B.ID
UNION
SELECT * from A RIGHT JOIN B on A_ID = B.ID
```



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

## DB에서 JOIN의 필요성
- 