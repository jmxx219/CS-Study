# Tomcat

## 배경지식

### Web Server      

**개념**
- HTTP 프로토콜을 이용해 정적인 웹페이지를 보여주는 역할을 하는 서버
    - 클라이언트로부터 HTTP요청을 받아드리고 HTML문서와 같은 웹 페이지를 반환하는 컴퓨터 프로그램
    - 위 기능을 제공하는 컴퓨터 프로그램을 실행하는 컴퓨터(하드웨어 측면)
- HTTP 프로토콜 기반으로, 클라이언트의 요청을 서비스하는 기능

<br>

**역할**
- 정적 컨텐츠 제공
    - WAS를 거치지 않고 바로 자원 제공
- 동적 컨텐츠 제공을 위한 요청 전달
    - 동적컨텐츠 요청을 WAS로 넘겨주고, WAS에서 처리한 결과를 클라이언트에게 전달

<br>

**기능**
- Reverse Proxy
    - 서버와 클라이언트 사이 프록시를 두고 프록시를 통해 데이터를 주고받음
    - 보안의 이유로 서버 내부 구조를 감추기 위해 사용

    ```
    Forward Proxy : 서버에 방문하는 클라이언트의 주소를 감춤
    Reverse Proxy : 클라이언트에게 서버의 주소를 감춤
    ```
- 로드 밸런싱
    - 클라이언트의 요청에 따른 처리를 동작 중인 여러 WAS에게 적절히 분배
- 캐싱
    - Reverse Proxy의 캐시를 의미
    - 서버로 찾아오는 클라이언트들이 자주, 반복적으로 요청하는 리소스들을 프록시 서버에 저장하고 제공
- 주기적인 체크
    - 웹 서버에 존재하는 수 많은 모듈을 사용해 WAS서비스가 정상적으로 동작하고 있는지 체크

> Reverse Proxy, 로드밸런싱, 캐싱의 역할을  Web Server가 담당하기에 Web Server와 WAS를 함께 이용한다

<br>

**필요성**
- 단순 정적 컨텐츠 처리를 맡으며 서버 부하 방지 및 효율적 분산처리 수행

<br>

**예시**
- Apache, NginX, IIS(window 전용 web서버)

<br>
<br>

### WAS (Web Application Server)

**개념**
- DB조회 및 다양한 로직을 처리를 요구하는 동적 컨텐츠를 제공하기 위해 만들어진 Application server
- HTTP를 통해 컴퓨터나 장치에 어플리케이션을 수행해주는 미들웨어
- `웹 컨테이너`, `서블릿 컨테이너`라고도 불림
    - `웹 컨테이너`
        - JSP, Servlet을 실행할 수 있는 소프트웨어
        - 웹 서버에서 JSP를 요청하면 톰캣에서는 JSP파일을 서블릿으로 변환하여 컴파일을 수행하고, 서블릿 수행결과를 웹 서버에게 전달
        - JSP컨테이너가 탑재되어있는 WASsms JSP페이지를 컴파일해 동적인 페이지 생성
    - 즉 WAS는 JSP, Servlet 구동환경을 제공

> JSP (Java Server Pages)
> - HTML코드에 JAVA코드를 넣어 동적 웹페이지를 생성하는 웹어플리케이션 도구
> - JSP가 실행되면 서블릿으로 변환되며 웹 어플리케이션 서버에서 동작되며 필요한 기능 수행
> - 위를 통해 생성된 데이터를 웹 페이지와 함께 클라이언트로 응답

<br>

**역할**
- Web Server 기능들을 구조적으로 분리해 처리하는 목적으로 제시
- 현재 WAS가 가지는 Web server도 정적 컨텐츠를 처리하는데 성능상 큰 차이 없음

<br>

**기능**
- 프로그램 실행 환경과 데이터베이스 접속 기능 제공
- 여러개의 트랜잭션 관리
- 업무를 처리하는 비즈니스 로직 수행
- Web Service 플랫폼으로서의 역할

<br>

**필요성**
- 자원의 효율적 사용: 요청에 맞는 데이터를 DB에서 가져와 비즈니스 로직에 맞춰 그때그때 결과 제공

<br>

**예시**
- Tomcat, ,Jetty, Undertow, Boss, Jeus, Web Sphere

> 위의 예시들은 자바진영에서 사용하는 것을 WAS로, 자바이외의 다른 진영에서는 WAS역할을 명확히 구분해놓지 않고있다.

<br>

#### Web Server와 WAS의 분리이유
- 기능 분리로 서버 부하 방지
- 물리적 분리로 보안강화
- 여러대의 WAS 연결이 가능하므로 로드밸런싱, fail over, fail back 처리에 유리
- 여러 웹 어플리케이션 서비스 가능

<Br>
<br>

## Apache Tomcat 이란?
- Apache 재단에서 제공하는 공개 소프트웨어 웹 어플리케이션(WAS)
- Tomcat이 Apache의 기능 일부를 가져와 제공해주는 형태로 **Apache Tomcat**으로 같이 합쳐서 부름

<br>

### Apache
- Apache 소프트웨어 재단에서 관리하는 HTTP 웹 서버
    - 흔히 부르는 apache server는 해당 재단에서 후원하는 오픈소스 프로젝트 커뮤니티에서 만든 http 웹 서버를 의미

<br>

### Tomcat

**개념**
- Apache 재단에서 개발한 서블릿 컨테이너(WAS)
- JSP, 자바 서블릿과 같은 자바 기술을 실행하고 관리하는데 사용

<br>

**구성**
- Coyote (HTTP Connector)
    - 톰캣의 HTTP Connector 역할 담당
        - 클라이언트와 서버간의 통신 처리
        - HTTP 프로토콜 지원
    - 아파치 웹서버와 함께 정적파일 서비스
    > 클라인트와 통신을 담당

<br>

- Catalina (Servlet Container)
    - 톰캣의 Servlet Container 역할
        - JSP, Java Servlet을 호스팅하는 환경 제공
        - 서블릿의 라이프사이클 관리
    > 서블릿과 JSP의 실행

<br>

- Jasper (JSP Engine)
    - 톰캣의 JSP엔진
        - JSP페이지를 서블릿으로 변환하고 실행하는 역할
        - 클라이언트가 JSP페이지에 접근하면 이를 서블릿 코드로 변환하고 컴파일 후 실행
    > JSP페이지의 처리

<br>

**동작**
1. `HTTP request`: HTTP요청을 Coyote에서 받아 Catalina로 전달
2. `Catalina` : Catalina에서 전달받은 HTTP요청을 처리할 웹 어플리케이션(Context)를 찾음
3. `Context` : WEB-INF/web.xml 파일 내용을 참조해 전달받은 요청을 서블릿에게 전달
4. `Servlet` : 요청된 Servlet을 통해 생성된 jsp 파일들이 호출될 때, Jasper (JSP Engine)가 Validation Check / Compile 등을 수행

<br>

**특징**
- Tomcat은 자바로 작성된 프로그램으로 JVM위에서 동작
    - 하나의 JVM에 하나의 Tomcat Instance(Server)가 하나의 프로세스로 동작

<br>
<br>

## Embedded Tomcat

### 외장 톰캣

**어플리케이션 실행**
- 톰캣 설치
    - 실제 자바로 작성된 코드와 상호작용할 `서블릿 컨테이너`가 필요함
- 톰캣 설정 파일 구성
- 톰켓 webapp 디렉토리에 빌드된 스프링 어플리케이션 war파일 포함
- 톰캣 실행

**특징**
- Host설정으로 apache virtual host 설정 가능
    - Apache HTTPd에서 사용하는 Virtual Host 기능 제공
        - `Virtual Host` : main host 하위에 가상의 호스트를 소프트웨어적으로 둘 수 있는 기능
    > 즉 여러 어플리케이션을 하나의 포트로 서비스 할 수 있음

<br>

### 내장 톰캣

- Spring boot에는 톰캣이 내장되어있음
    - 어플리케이션 빌드와 실행만으로 웹 어플리케이션 서비스 가능
    - 톰캣을 설치할 필요없이 어플리케이션 바로 실행 가능

**어플리케이션 실행**
- 빌드된 스프링부트 어플리케이션 jar, war를 자바 명령어로 실행

**특징**
- 여러 호스트를 분기할 수 있는 기능을 사용하기 매우 복잡
    - 내장톰캣으로 실행하는 Spring boot어플리케이션은 그 자체가 하나의 프로그램으로 서블릿 컨테이너가 하나의 포트를 독자적으로 점유
- 톰캣 구성 부분의 관심사를 분리하여 개발자가 코드 작성에 집중 가능

<br>
<br>

### Ref
[아파치 톰캣 내부구조](https://kadensungbincho.tistory.com/62)          
[Web - 아파치(Apache)/톰캣(Tomcat)이란?](https://determination.tistory.com/entry/Web-아파치Apache톰캣Tomcat이란)            
[Embedded Tomcat과 Tomcat의 차이](https://thxwelchs.github.io/EmbeddedTomcat과Tomcat의차이/)