## 브라우저에 URL입력시 네트워크 상 일어나는 일

```
브라우저에 www.github.com 입력시 일어나는 일
```
        

### 1. 브라우저에 URL 입력

- 핸들링 인풋 진행            
    브라우저 프로세스 안의 ui스레드가 입력된 텍스트 값 판단
    - `Search Query` 일 경우, 검색이 되도록 함(크롬 브라우저 → 구글 검색 엔진)
    - `URL` 일 경우, URL값을 네트워크 스레드에게 전달할 준비를 함

<br>
<br>

- URI, URL, URN

    <img width="555" alt="스크린샷 2023-10-10 오후 1 52 50" src="https://github.com/jmxx219/CS-Study/assets/50795805/63e432db-6730-45bd-a69c-b11cb508e600">

<br>

**URI**
- Uniform Resource Identifier (통합 자원 식별자)
- 인터넷 자원을 식별할 수 있는, 하나의 `리소스`를 가르키는 문자열
    - 이때 리소스는 HTTP와 같은 프로토콜에서 요청한 대상
- URI의 하위개념으로 `URL`, `URN` 존재

<br>

**URL**
- Uniform Resource Locator
- 네트워크 상에서 웹 페이지, 이미지, 동영상 등의 파일이 `위치`한 정보를 나타냄
- FTP, SMTP등 다른 프로토콜에서 사용 가능 


<br>

**URN**
- Uniform Resource Name 
- `URI`의 표준 포맷중 하나로 이름으로 리소스를 특정하는 URI
- 리소스의 name을 가리키는데 사용
    - http와같은 프로토콜을 제외함

- 특징
    - 리소스를 영구적이고 유일하게 식별할 수 있는 URI
    - 리소스 접근방법과 웹 상의 위치가 표기되지 않음
    - 리소스 자체에 부여된 영구적이고 유일한 이름이며 변하지 않음
    - 실제 자원을 찾기 위해선 URN을 URL로 변환해 이용

<br>

> **URL과 URI의 차이점**
> - URL은 어떻게 리소스를 얻을 것이고 어디에서 가져와야하는지 명시하는 URI
> - URN은 리소스를 어떻게 접근할 것인지 명시하지 않고 경로와 리소스 자체를 특정하는 것을 목표로하는 URI

<br>
<br>
<br>

### 2. URL의 IP주소 찾기

- 인터넷의 모든 URL에는 고유한 IP주소가 할당 됨
- IP주소
    - 인터넷 상의 각 컴퓨터 혹은 네트워크 장치 식별 시 사용
    - IP주소는 웹 사이트를 호스팅하는 웹 서버를 가리킴
    - DNS쿼리를 통해 얻은 IP주소는 원하는 웹 사이트, 서버 혹은 호스트의 위치를 가리킴
- DNS
    - 인터넷에서 도메인 이름을 해당 도메인의 IP주소로 변환하는 시스템

<br>

> DNS를 통해 변환 된 도메인 이름의 IP주소를 찾는다

<br>
<br>

### **캐시의 DNS기록 확인**

1. 브라우저 캐시 확인
    - 최근에 방문한 웹 사이트의 DNS조회 결과 캐시 
    - 브라우저는 이전에 방문한 웹 사이트의 DNS기록을 일정기간 저장함

2. OS 캐시 확인
    - 브라우저 캐시에 정보가 없거나 만료된 경우 OS 캐시 확인

3. host file 확인
    - IP주소와 도메인 주소를 매핑해주는 host file 확인

<br>

```
OS캐시는 모든 DNS조회에 대한 공통 캐시이며, 호스트 파일 캐시는 특정 매핑을 포함하는 호스트 파일과 연관 됨.
```
<br>

3. 라우터 캐시 확인
    - 라우터는 이전 DNS조회 결과를 임시 캐시에 저장하고 다른 장치에게 정보 제공

4. ISP 캐시 확인
    - 캐시 확인 마지막 단계로 ISP의 DNS캐시 확인
    - ISP의 DNS서버는 브라우저의 DNS요청을 처리하고 결과를 캐시에 저장
    - ISP의 캐시는 브라우저나 OS보다 오래 유지 

<br>
<br>

### **캐시에 없을 경우 DNS쿼리 진행**

> DNS서버에 IP주소 요청

<br>

**DNS**              
- Domain Name System (도메인 이름 시스템)
- 모든 IP주소를 구별하고 외우기 쉽지않기에, 사람이 기억하기 쉽게 이름을 정한 것
- 호스트의 도메인 이름과 네트워크 주소를 양변환 
- 도메인
    - 인터넷 호스트에 부여된 문자형의 유일한 이름
    - `계층적 도메인 관리 구조`에 의해 도메인명의 유일성 유지
    - 도메인 관리자가 상위 도메인 관리에게 등록 후 사용

<br>

**DNS 쿼리 과정**
1. 도메인 주소를 `Local DNS`에 전달

2. Local DNS는 `Root DNS`에 도메인 주소를 보내고 이에 맞는 IP주소 요청
- Local DNS Server
    - DNS 서비스를 요청하는 클라이언트 호스트가 소속된 DNS서버
    - 소속된 호스트들의 DNS서비스 요청 대행
    - 통신사(KT, SKT, LG)와 같은 기지국 

<br>

3. Root DNS에서 `TLD 서버` 정보 얻음
    - TLD서버에도 IP주소를 찾을 수 없다면, Local DNS는 Authoritative DNS 서버에게 찾고자하는 IP주소 요청

- Root Server
    - 일반적으로 TLD서버의 IP주소 제공
    - 전 세계에 수백개의 복제 서버 존재
    - 13개의 관리기관에 의해 관리
    - Local DNS 서버에 의해 처음 접속 됨

<br>

- TLD Server
    - 최상위 도메인(Top Level Domain)에 대한 DNS서비스 담당
    - `도메인 관리 서버`, `도메인 레지스트리`라고도 불림
    - 특정 최상위 도메인에 대한 관련 정보 저장 및 관리
        - 도메인 등록, 업데이트, 해지, DNS 구성 
        - 특정 최상위 도메인: (ex, `.com`, `.net`, `.org`)
    - 일반적으로 책임(Authoritative) DNS 서버의 IP 주소 제공

<br>

-  책임(Authoritative) DNS 서버
    - 특정 호스트의 도메인 명 정보를 유지하고 있는 서버
    - 규모가 큰 기관은 대부분 자신의 호스트들에 대한 책임 DNS서버 유지
    - 소규모 기관은 외부의 책임 DNS 서버 위탁

<br>

4. Authoritative DNS 서버에서 Local DNS 서버에게 찾는 IP주소 정보 제공

5. Local DNS는 제공받은 IP주소 수신
    - 수신 받은 IP주소를 캐싱
    - 이후 다른 요청시 응답가능하도록 IP주소를 단말(PC)에게 전달

<br>

> Local DNS 서버가 여러 DNS 서버에 차례대로 각 서버에게 요청하며 답을 찾는 과정을 `재귀적 쿼리(Recursive Query)`라고 함.

<br>
<br>
<br>

### 3. 브라우저와 서버의 연결 (TCP connection)
- 인터넷 프로토콜을 이용해 브라우저와 서버 연결
- 웹 사이트의 경우 HTTP요청에 일반적으로 **TCP연결 진행**
    - [3-way handshake](https://github.com/jmxx219/CS-Study/blob/main/network/3-way%20handshake.md)을 통한 TCP connection 완성

<br>
<br>
<br>

### 4. 브라우저가 웹 서버에 HTTP 요청
- TCP연결 후, 데이터 전송

<br>
<br>
<br>

### 5. 요청에 대한 서버의 응답
- 서버는 요청을 처리하고 response 생성
- 서버가 [HTTP response](https://github.com/jmxx219/CS-Study/blob/main/network/HTTP%20응답코드.md)를 보냄

<br>
<br>

**Web Server**          
- 개념
    - HTTP 프로토콜을 이용해 정적인 웹페이지를 보여주는 역할을 하는 서버
         - 클라이언트로부터 HTTP요청을 받아드리고 HTML문서와 같은 웹 페이지를 반환하는 컴퓨터 프로그램
        - 위 기능을 제공하는 컴퓨터 프로그램을 실행하는 컴퓨터(하드웨어 측면)
    - HTTP 프로토콜 기반으로, 클라이언트의 요청을 서비스하는 기능
    - 기능
        - 정적 컨텐츠 제공
            - WAS를 거치지 않고 바로 자원 제공
        - 동적 컨텐츠 제공을 위한 요청 전달
            - 동적컨텐츠 요청을 WAS로 넘겨주고, WAS에서 처리한 결과를 클라이언트에게 전달
- 필요성
    - 단순 정적 컨텐츠 처리를 맡으며 서버 부하 방지 및 효율적 분산처리 수행
- 예시
    - Apache, Nginx, IIS(window 전용 web서버)


<br>
<br>

**WAS (Web Application Server)**
- 개념
    - DB조회 및 다양한 로직을 처리를 요구하는 동적 컨텐츠를 제공하기 위해 만들어진 application server
    - HTTP를 통해 컴퓨터나 장치에 어플리케이션을 수행해주는 미들웨어
    - `웹 컨테이너`, `서블릿 컨테이너`라고도 불림
        - 컨테이너란 JSP, Servlet을 실행할 수 있는 소프트웨어
        - 즉 WAS는 JSP, Servlet 구동환경을 제공
- 역할
    - Web Server 기능들을 구조적으로 분리해 처리하는 목적으로 제시
    - 현재 WAS가 가지는 Web server도 정적 컨텐츠를 처리하는데 성능상 큰 차이 없음
- 필요성
    - 자원의 효율적 사용: 요청에 맞는 데이터를 DB에서 가져와 비즈니스 로직에 맞춰 그때그때 결과 제공
- 예시
    - Tomcat, JBoss, Jeus, Web Sphere

<br>

> Web Server와 WAS의 분리 이유
> - 기능 분리로 서버 부하 방지
> - 물리적 분리로 보안 강화
> - 여러대의 WAS연결로 장애극복 쉽게 대응
> - 여러 웹 어플리케이션 서비스 가능            
> 이와 같이 자원이용의 효율성 및 장애극복, 배포 및 유지보수 편의성, 효율적 분산처리를 위해 분리


<br>
<br>
<br>

### 6. 브라우저는 HTML content를 보여줌
- 브라우저는 HTML content를 단계적으로 보여줌
- HTML 스켈레톤 렌더링 → HTML tag체크 후 추가 필요 요소(이미지, CSS, js파일..) Get 요청 → 정적파일 캐싱 → www.github.com 화면 보임