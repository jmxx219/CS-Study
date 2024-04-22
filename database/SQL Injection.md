# SQL Injection

SQL 인젝션은 악의적인 사용자가 보안상의 취약점을 이용하여, 조작된 쿼리문을 DB에 그대로 전달하여
비정상적 명령을 실행시키는 공격 기법이다.  

<br>

## 공격 목적

1. 정보 유출
2. 저장된 데이터 유출 및 조작
3. 원격 코드 실행
   * 일부 데이터베이스의 경우 확장 프로시저를 이용하여 원격으로 시스템 명령의 실행 가능
   * 시스템 명령의 실행은 원격 자원 접근 및 데이터 유출, 삭제 가능
4. 인증 우회
    * 공격의 대표적인 경우는 로그인 폼에서 발생
    * 이때 상위 권한을 가진 사용자의 권한으로 인증 절차를 우회하여 로그인


<br>

## 발생 조건

1. 사용자 입력값의 신뢰성 부족
   * 사용자로부터 입력 받은 값(폼 데이터, URL 파라미터)을 적절히 검증 또는 이스케이프하지 않은 경우 발생
2. 동적 SQL 쿼리 생성
   * 동적으로 SQL 쿼리를 생성하고 사용하는 경우, 사용자 입력 값이 직접 쿼리에 삽입되면서 발생
3. 실행 권한 문제
   * 사용자가 실행할 수 없는 쿼리를 실행하려고 하는 경우, 이를 불허하지 않고 실행하게 되면 발생


<br>

## 공격 종류 및 방법

| 구분           | 예시                                  |
|--------------|-------------------------------------|
| Classic SQLi | Error-based SQLi, Union-based SQLi  |
| Blind SQLi   | Boolean-based SQLi, Time-based SQLi |


<br>

### Error based SQL Injection

논리적 에러를 이용한 SQL 인젝션은 대중적인 공격 기법이다.

로그인 상황을 예시로 보자.  

```sql
SELECT * FROM users WHERE username = '입력된_사용자명' AND password = '입력된_비밀번호';
```

만약 사용자가 아래와 같이 입력한다면

```sql
'' OR '1'='1'; --
```

SQL은 최종적으로 다음과 같이 변한다.

```sql
SELECT * FROM users WHERE username = '' OR '1'='1'; --' AND password = '입력된_비밀번호';
```

'1'='1'라는 구문을 이용해 WHERE 절을 모두 참으로 만들고, --를 넣어줌으로써 뒤의 구문을 모두 주석처리 하였다.  
위 쿼리문이 실행된다면 Users 테이블에 있는 모든 정보를 조회하게 되고 가장 먼저 만들어진 계정으로 로그인에 성공하게 된다.  

보통은 관리자 계정을 제일 처음 만들기 때문에 관리자 계정에 로그인 할 수 있게되고, 관리자 계정을 탈취하게 된다면 관리자 권한으로
또 다른 2차피해까지 발생시킬 수 있게 된다.  

<br>

### Union based SQL Injection

SQL에서 UNION은 두 개의 쿼리문에 대한 결과를 통합하여 하나의 테이블로 보여주는 키워드이다.  
Union 인젝션을 성공하기 위해서는 두 가지 조건이 있다. 하나는 Union 하는 두 테이블의 컬럼 수가 같아야 하고, 데이터 형이 같아야 한다.  

예시를 살펴보자.  

```sql
SELECT username, password FROM users WHERE id = '$id';
```

위는 사용자의 아이디와 패스워드 확인 SQL쿼리이다. 
이때 '$id'에 아래와 같은 값을 입력한다면,

```sql
$id: 1 UNION SELECT null, table_name FROM information_schema.tables --
```

최종 쿼리는 아래와 같다.  


```sql
SELECT username, password FROM users WHERE id = '1' UNION SELECT null, table_name FROM information_schema.tables --';
```

`UNION SELECT null, table_name FROM information_schema.tables ` 부분은 사용자 테이블 대신 `information_schema`에서 테이블 이름을 
가져오는 부분이다. 

이 경우 공격자는 추가로 가져온 테이블의 정보를 이용하여 데이터베이스를 조작할 수 있다.  

<br>

### Boolean based SQL Injection

SQL 쿼리의 참/거짓 기반으로 공격자가 DB 정보를 추출하는 기법이다.  

예시를 살펴보자.  

```sql
SELECT username, password FROM users WHERE id = '$id';
```

공격자가 아이디가 '1'인 사용자의 비밀번호 길이를 확인하는 쿼리를 실행하려고 한다.  

```sql
$id: 1' AND LENGTH(password) > 10 --
```

이 입력을 SQL 쿼리에 삽입하면 아래와 같이 된다.  

```sql
SELECT username, password FROM users WHERE id = '1' AND LENGTH(password) > 10 --';
```

해당 값의 결과가 웹 페이지에 반영되면 공격자는 비밀번호의 길이를 추출할 수 있다. 이런 과정을 반복하여
결과들을 조합한다면 원하는 정보를 얻을 수 있다.  

<br>

### Time based SQL Injection

DB 서버의 응답 시간을 기반으로 공격자가 데이터베이스 정보를 추출하는 기법이다.   
이 공격은 DB 쿼리에 시간을 지연시키는 함수를 삽입하여 결과를 확인하는 방식으로 작동한다. 
공격자는 쿼리의 참/거짓 여부에 따라 서버 응답 시간의 변화를 관찰하고, 이를 통해 DB 정보를 유추하거나 추출한다.  

예시로 아이디와 비밀번호 조회 코드를 살펴보자.  

```sql
SELECT username, password FROM users WHERE id = '$id';
```

공격자가 아이디가 '1'인 사용자 비빌번호의 첫 번째 글짜가 'a'인지 확인하고자 한다.

```sql
$id: 1' AND SUBSTRING(password, 1, 1) = 'a' AND SLEEP(5) --
```

이 입력을 SQL 쿼리에 삽입하면 결과적으로 아래와 같이 변한다.  

```sql
SELECT username, password FROM users WHERE id = '1' AND SUBSTRING(password, 1, 1) = 'a' AND SLEEP(5) --';
```

여기서 `SUBSTRING(password, 1, 1) = 'a'` 조건이 만족될 때까지 서버는 5초 동안 응답을 보류하게 되고, 공격자는 이를 이용하여
패스워드의 첫 글자를 추측할 수 있다.  


<br>

## 대응 방안

1. **화이트리스트 기반 검증**   
   * 화이트리스트 기반의 검증은 허용되는 값의 목록을 작성하고, 입력 값이 이 목록에 속하는지 확인하는 방식. 
   * 허용되지 않은 문자나 특수 문자를 입력으로 거부함으로써 공격을 방지할 수 있다.

2. **Prepared Statement 사용**  
   * Prepared Statement는 입력 값을 문자열로 취급하고 데이터베이스의 파라미터로 처리하는 방법. 
   * 이는 SQL 쿼리를 미리 컴파일하여 실행하는 방식으로, 공격자가 의도한 쿼리를 실행할 수 없게 한다.

3. **에러 메시지 최소화**  
   * 에러 메시지에는 민감한 정보가 포함될 수 있다. 
     * ex) 데이터베이스 버전정보
   * 따라서 실제 사용자에게는 유용한 정보만을 포함하도록 에러 메시지를 최소화해야 함. 
   * 이로서 공격자가 데이터베이스 구조를 파악하기 어렵게 만듭니다.

4. **웹 방화벽 사용**  
   * 웹 방화벽은 웹 애플리케이션의 트래픽을 감시하고, 악의적인 행위로부터 보호하는 역할. 
   * SQL Injection과 같은 웹 공격을 탐지하고 차단할 수 있다.

5. **입력 값의 정규화**  
   * 입력 값이 예상과 다른 형식이면 거부하도록 정규화를 수행. 
   * 예를 들어, 숫자를 기대하는 필드에 문자열이 입력되는 경우를 방지할 수 있다.

6. **최소 권한 원칙**  
   * 데이터베이스 계정에는 최소한의 권한만 부여하는 것이 중요. 
   * 애플리케이션이 필요로 하는 최소한의 권한만 부여함으로써 특정 공격에 대한 영향을 최소화.

7. **보안 업데이트**
   * 데이터베이스 시스템, 웹 서버, 프레임워크 등을 최신으로 유지하고 보안 업데이트를 적용하는 것이 중요. 
   * 새로운 취약점에 대한 패치가 제공되면 빠르게 업데이트해야 한다. 

이러한 방법들을 종합적으로 사용함으로써 SQL 인젝션에 안전한 웹 애플리케이션을 구축할 수 있다.

<br>

> `블랙리스트 vs 화이트리스트`  
> 화이트리스트는 허용되는 값의 목록을 정의하고, 블랙리스트는 금지되는 값을 정의한다.  
> 때문에 화이트리스트를 사용하는 것이 블랙리스트보다 안전하다.  


<br>

#### ref

[SQL Injection 이란? (SQL 삽입 공격)](https://noirstar.tistory.com/264)  
[SQL Injection and How to Prevent It?](https://www.baeldung.com/sql-injection)  
[[웹해킹] SQL Injection 총정리](https://gomguk.tistory.com/118)  