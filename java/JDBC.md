# JDBC
> JDBC (Java Database Connectivity)

<br>

## JDBC

**배경**
- 데이터베이스 접근의 표준화를 위해 등장
- JDBC 존재 전 데이터베이스마다 존재하는 고유한 API를 직접 사용함
    - 데이터베이스 교체 시 기존의 코드 모두 수정해야하는 불편함 존재

<br>

**개념**
- Java 프로그램이 데이터베이스에 접근할 수 있게 해주는 기술
    - Java를 이용한 데이터베이스 접속과 SQL 문장의 실행, 그리고 실행 결과로 얻어진 데이터의 핸들링을 제공하는 방법과 절차에 관한 규약
    - Java 프로그램 내에서 SQL문을 실행하기 위한 자바 API
    - JAVA는 표준 인터페이스인 JDBC API를 제공

<br>

**특징**
- JDBC는 데이터베이스 독립성을 유지
    - 이를 통해 다양한 데이터베이스 시스템`(MySQL, Oracle, SQL Server 등)`과 상호 작용가능

<br>
<br>

## JDBC API

**개념**
- Java에서 데이터베이스와 상호 작용하기 위한 표준 인터페이스
- Java 애플리케이션이 데이터베이스 작업을 수행할 수 있게 해주는 인터페이스를 정의
    - 이를 통해 데이터베이스와의 통신을 추상화 함

<br>
<br>

**특징**

 <img width="500" alt="스크린샷 2024-07-02 오후 3 22 10" src="https://github.com/jmxx219/CS-Study/assets/50795805/9eab42c6-8325-4147-881d-30c691b4a91c">

- JDBC API에서 제공하는 `표준 인터페이스`는 대부분 인터페이스로 구현 됨
    - DBMS 종류에 상관없이 사용 가능하도록하기 위함
    - 각 DBMS 회사에서는 이 인터페이스들을 구현한 클래스 파일들을 JDBC 드라이버로 묶어서 제공
- Java 어플리케이션과 실제 데이터베이스가 연동하기 위해서는 각 DBMS 종류에 맞는 드라이버를 다운받아 사용

<br>
<br>


**JDBC 표준 인터페이스**
> JDBC는 표준 인터페이스(java.sql Package)를 정의하여 제공

<br>


- java.sql.Driver
   - 데이터베이스 드라이버를 로드하고 연결을 설정하는 역할

- java.sql.Connection
    - 특정 데이터베이스와 연결정보를 가지는 인터페이스
    - DriverManager로부터 Connection 객체를 가져옴
    - 데이터베이스 연결을 나타내며, 데이터베이스와의 통신을 관리

- java.sql.Statement
    - SQL query문을 DB에 전송하는 방법을 정의한 인터페이스
    - Connection을 통해 가져옴
    - SQL 문을 실행하고 결과를 반환받는 데 사용

- java.sql.ResultSet
    - SELECT 구문 실행 결과를 조회할 수 있는 방법을 정의한 인터페이스
    - 쿼리 결과를 테이블 형식으로 제공
    - 데이터베이스에서 검색된 데이터를 읽는 데 사용

- java.sql.PreparedStatement
    - SQL 문을 미리 컴파일하여 실행 성능을 향상시키는 데 사용

<br>
<br>
<br>


## JDBC 드라이버
- DBMS와 통신을 담당하는 자바 클래스
- 각 DBMS를 위한 JDBC Driver를 통해 서로 다른 DBMS 사용 가능
    - DBMS별로 알맞은 JDBC드라이버 필요

<br>
<br>
<br>


## JDBC 동작 흐름

**전체 흐름**

<img width="500" alt="스크린샷 2024-07-02 오후 2 26 28" src="https://github.com/jmxx219/CS-Study/assets/50795805/4acc9e3d-ca3d-4549-8f17-ce7e9f79e647">

- Java 애플리케이션 내에서 DB에 접근하는 구조
    - 이때 `JDBC API` 이용
    -  `JDBC API` 를 이용하기 위해선 `JDBC 드라이버`로딩 필요

<br>
<br>


**세부 흐름**

<img width="500" alt="스크린샷 2024-07-02 오후 3 29 41" src="https://github.com/jmxx219/CS-Study/assets/50795805/3e841e26-2951-441e-9a5f-f077d295f659">

- JDBC 드라이버 로딩
    - 사용하고자 하는 JDBC 드라이버 로딩
    - JDBC 드라이버는 `DriverManager` 클래스를 통해 로딩 됨

- Connection 객체 생성
    - JDBC 드라이버가 정상적으로 로딩됐을때, DriverManager를 통해 Connection 객체 생성
        - `Connection 객체`: 데이터베이스와 연결되는 세션
    - Connection객체를 생성하는 작업은 비용이 많이 들기에 [Connection Poo](https://github.com/jmxx219/CS-Study/blob/main/database/DBCP.md#dbcp-db-connection-pool) 이용

- Statement 객체 생성
    - `Statement 객체`
        - 작성된 SQL 쿼리문을 실행하기 위한 객체
        - 정적 SQL 쿼리 문자열을 입력으로 가짐

- Query 실행
    - 생성된 Statement 객체를 이용해 입력한 SQL 쿼리 실행

- ResultSet 객체로부터 데이터 조회
    - 실행된 SQL 쿼리문에 대한 결과 데이터 셋

- ResultSet, Statement, Connection 객체들의 Close
    - JDBC API를 통해 사용된 객체들은 생성된 객체들을 사용한 순서의 역순으로 Close


<br>
<br>
<br>


### Ref
[[Java] JDBC란 무엇인가? - Java Database Connectivity](https://ittrue.tistory.com/250)         


