## DBCP (DB Connection Pool)

DB와 Client가 Connection을 어떻게 구성하는지 설명해 주세요.

### 사용 배경

**데이터베이스 커넥션**
- 웹 애플리케이션과 데이터베이스는 서로 다른 시스템
- 이를 연결하기 위해 `데이터베이스 드라이버` 사용

- 데이버베이스 연결의 생애주기              
    - 1. 데이터베이스 드라이버를 사용하여 데이터베이스 연결 열기
    - 2. 데이터를 읽고 쓰기 위한 TCP 소켓 열기
    - 3. TCP 소켓을 사용해 데이터 통신
    - 4. 데이터베이스 연결 닫기
    - 5. TCP 소켓 닫기

- TCP기반 통신 시 매번 connection을 열고닫는(3-way-handshake, 4-way-handshake) 시간적인 비용 발생으로 서비스 성능저하

<br>

> 위의 문제를 해결하기 위해 DBCP 등장

<br>
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


**DBCP의 동작**
1. 백엔드 서버는 API요청을 받기 전, 미리 DB connection을 진행
    - 연결된 DB connection들을 pool로 관리함

2. Get Connection
    - 백엔드 서버에서 DB 데이터 필요 시, DB connection pool의 사용하지 않는 connection을 가져옴
    - 해당 connection을 통해 DB서버에 쿼리 요청

        ```
        유휴 커넥션 존재 시 해당 커넥션을 반환.
        유휴 커넥션이 존재하지 않을 경우 다른 스레드가 커넥션을 반납하기까지 대기
        ```

3. Close Connection
    - 백엔드 서버가 쿼리 응답을 받았다면, 이전에 get connection한 connection을  connection pool에 다시 반납
    - TCP연결을 끊는 것이아닌, connection pool로의 반납

<br>

> **이때 connection pool이 DBCP (DataBase Connection Pool)이다.**


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

### DBCP 프레임워크

**종류**
- Apache Commons DBCP
- Tomcat DBCP
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


**주요 DB서버 파라미터 (HikariCP)**
- `max_connections`
    - 클라이언트와 맺을 수 있는 최대 connections 수
    - 신규서버 투입, DBCP connection 수 늘릴때 등 문제없이 동작하기 위해 적절한 값 설정


- `wait_timeout`
    - DB 서버에서 connection이 inactive할 때, 다시 요청이 오기까지 얼마의 시간을 기다린 뒤에 close 할 것인지 결정
    - 일정시간을 기다린 후에도 요청이 오지않을 경우 연결해제
        - DBCP의 어떤 connection이 비정상적인 상태일때, DB서버는 이를 모르기때문에 계속 대기하는 것을 방지하기 위함

