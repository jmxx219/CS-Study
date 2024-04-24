## DBCP (DB Connection Pool)


### 사용 배경

**데이터베이스 커넥션**

- 커넥션이란 어플리케이션과 데이터베이스의 연결을 뜻하며, 어플리케이션에서 데이터베이스에 접속하고 종료하는 일련의 과정을 의미

- 웹 애플리케이션과 데이터베이스는 서로 다른 시스템으로, 이를 연결하기 위해 `데이터베이스 드라이버` 사용

- 데이버베이스 연결의 생애주기              

    1. 데이터베이스 드라이버를 사용하여 데이터베이스 연결 열기

    2. 데이터를 읽고 쓰기 위한 TCP 소켓 열기

    3. TCP 소켓을 사용해 데이터 통신

    4. 데이터베이스 연결 닫기

    5. TCP 소켓 닫기

- TCP기반 통신 시, 매번 connection을 열고닫는(3-way-handshake, 4-way-handshake) 시간적인 비용 발생으로 서비스 성능저하

<br>

> 효율적인 커넥션 관리는 성능과 안정성 측면에서 중요한 요인으로, 시스템의 많은 부하를 주는 커넥션을 여닫는 비용을 해결하기 위해 DBCP 등장

<br>


### DHCP

**개념**

- DataBase Connection Pool의 약자로, DB와 connection을 맺고있는 객체를 관리

- 웹 컨테이너가 실행되면서 DB와 미리 connection을 해놓은 객체들을 pool에 저장

- 클라이언트 요청 시 connection을 빌려주고, 처리가 끝나면 해당 connection을 반납받아 pool에 저장

<br>

**특징**

- HTTP 요청에 따라 pool에서 connection 객체를 사용하고 반납

- 물리적인 데이터베이스 connection 부하를 줄이고 연결 관리

- pool에 미리 생성된 connection으로, 요청마다 connection을 생성 하지 않아 연결시간이 소비되지 않음 

- connection을 계속 재사용하므로 생성되는 connection 수 제한적으로 설정

- DBCP 사용 시에도 교착상태(deadlock)에 주의해야 함
    - 스레드가 서로의 DB Connection이 반납되기만을 무한정 대기하게되며 발생

<br>

**DBCP의 동작방식(HikariCP)**


<img width="450" src="https://github.com/jmxx219/CS-Study/assets/74135929/e568fb33-5bcf-49ae-98b3-43a4218204e4">

[Step 1]
- Thread가 Connection을 요청하여 Connection Pool에 유휴 Connection을 찾아 반환한다.

> HikariCP의 경우 이전에 사용한 Connection이 존재하는지 확인하여, 이를 우선반환하는 특징이 있다.

<img width="450" src="https://github.com/jmxx219/CS-Study/assets/74135929/bc5bd439-2a1d-4a94-a0e5-245337ce24b3">

[Step 2]

- 가능한 Connection이 존재하지 않으면, HandOffQueue를 Polling하면서 다른 Thread의 Connection반납을 기다린다

> 지정한 TimeOut시간까지 대기하다 시간이 만료되면 예외를 던진다.

<img width="450" src="https://github.com/jmxx219/CS-Study/assets/74135929/18a2f256-9a2f-4f95-9fc2-ee26b0e00a85">

[Step 3]

- 사용한 Connection이 반납되면 Connection Pool이 Connection 사용내역을 기록하고, HandOffQueue에 반납된 Connection을 넣는다.

- 이를통해 HandOffQueue를 Polling하던 대기Thread가 Connection을 획득하고 작업을 이어나간다.
  
<br>

**Connection Pool의 크기**

- WAS에서 Connection을 사용하는 주체는 `스레드`

- 데이터베이스에서 connection의 수는 스레드의 수와 어느정도 일치

    - Connection이 많다는 것은 데이터베이스 서버가 스레드를 많이 사용함을 의미

    - 이에 Context Switching으로 인한 오버헤드가 더 많이 발생하기에 Connection Pool을 많이 늘려도 성능적인 한계 존재


- Connection Pool의 크기 설정

    - 크게 설정

        - 메모리 소모: 큼

            - 스레드가 사용하는 connection 외에 남은 connection은 메모리 공간만 차지

        - 사용자 대기시간: 줄어듦

    - 작게 설정

        - 메모리 소모: 작음

        - 사용자 대기시간: 길어짐

<br>
<br>
<br>

### DBCP 프레임워크

**종류**

- Apache Commons DBCP

- Tomcat DBCP

    - Tomcat에서 내장되어 사용 됨

    - Apache Commons DBCP 라이브러리를 바탕으로 만들어짐

- Oracle UCP

- HikariCP

<br>

**HikariCP**       

- 가벼운 용량과 빠른 속도를 가지는  JDBC 데이터베이스 커넥션 풀 프레임워크

- spring-boot에 기본적으로 내장

    - spring-boot는 항상 HikariCP를 우선적으로 사용함

    - HikariCP를 사용할 수 없는 경우, Tomcat DBCP → Apachde Commons DBCP → Oracle UCP 순으로 선택

- HikariCP는 바이트코드 수준까지 극단적으로 최적화되어있기에 가장 많이 사용 됨

<br>
<br>


**주요 DB서버 파라미터 (HikariCP)**
- `max_connections`
   
    - 클라이언트와 맺을 수 있는 최대 connections 수
   
    - 신규서버 투입, DBCP connection 수 늘릴때 등 문제없이 동작하기 위해 적절한 값 설정


- `wait_timeout`
   
    - DB 서버에서 connection이 inactive할 때, 다시 요청이 오기까지 얼마의 시간을 기다린 뒤에 close 할 것인지 결정
   
    - 일정시간을 기다린 후에도 요청이 오지않을 경우 연결해제
        - DBCP의 어떤 connection이 비정상적인 상태일때, DB서버는 이를 모르기때문에 계속 대기하는 것을 방지하기 위함

<br>
<br>

### 관련 개념

**Data Source**

- `javax.sql.DataSource 인터페이스`

- Connection Pool을 관리하는 목적으로 사용되는 객체

- Connection Pool을 어플리케이션 단에서 어떻게 관리할지 구현하는 인터페이스
    
    - 어플리케이션에서는 해당 Data source 인터페이스를 통해 Connection을 얻어오고 반납하는 등의 작업 구현

<br>

**JDBC**

- 개념

    - `Java DataBase Connectivity`, 자바에서 데이터베이스를 조작하는 표준 SQL 인터페이스 API

- 특징

    - 데이터베이스들은 JDBC를 사용하기 위한 각각의 Driver 제공

    - DBMS 독립성으로, 애플리케이션은 특정 DBMS에 종속되지 않고 JDBC API를 통해 다양한 DBMS와 통신 가능
    
    - 인터페이스 기반 구축(데이터베이스 커넥션 인터페이스)
    
    - 일반적인 JDBC는 Database Pool방식을 사용하지 않고 DB에서 정보를 가져올때마다 
    
    **Driver를 로드**하고 **커넥션을 열고닫음**

    - 해당 비효율적인 부분을 DBCP를 통해 효율적으로 처리
    
    - 이런 비효율성으로 상용 어플에서 JDBC방식 거의 사용하지 않음

<br>

**JNDI**

- 개념
    
    - `Java Naming and Directory Interface`
    
    - Java 애플리케이션에서 리소스, 객체 및 서비스에 대한 표준 네이밍 및 디렉터리 서비스에 접근할 수 있게 해주는 API 및 스펙

- 기능
    
    - 객체검색
    
    - 이름과 객체 연결
    
    - 분산환경 지원 

- 특징
    
    - 데이터베이스의 DB Pool을 미리 Naming 시켜주는 방법 중 하나
        - WAS에서 JNDI를 활용하여 DBCP를 관리해 데이터베이스 연결관리의 효율성을 높일 수 있음
    
    - Static 객체 사용
        - JNDI로 설정된 데이터베이스 연결 풀은 WAS 내에서 관리되며, 이는 static 객체로 설정될 수 있음
        - 이러한 정적객체를 사용하여 데이터베이스 연결을 쉽게 가져다 사용 가능
        - 애플리케이션 전체에서 공유되므로 연결을 여러 번 생성할 필요가 없어 효율성 향상
        
    - 어플리케이션 확장성
        - JNDI를 사용하여 DB 연결을 관리하면 애플리케이션의 확장성이 향상
        -  새로운 애플리케이션 서버 노드 추가 및 새로운 인스턴스 배포 시, JNDI를 통해 설정된 연결 풀을 공유하고 확장
        
<br>

> JNDI를 활용하여 WAS에서 DB Connection Pool을 관리하고, 정적 객체로 연결을 쉽게 사용함으로써 데이터베이스 연결 관리의 효율성을 높일 수 있음.           
> 더불어 애플리케이션의 성능과 확장성을 개선할 수 있음
>
---

Reference

[느리더라도 꾸준하게 (Connection Pool이란?)](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcwF60R%2FbtrpDL5kdvI%2FhQ1GvAJ8TCeme50J4Wwmc1%2Fimg.png)
