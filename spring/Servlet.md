# Servlet
## Servlet(서블릿)이란?

![다운로드 (2)](https://github.com/jmxx219/CS-Study/assets/64017307/28863883-c348-4ee1-b05c-8b78363d59e7)


### 클라이언트의 요청을 처리하고 그 결과를 반환하는 Servlet클래스의 구현 구칙을 지킨 자바 웹 프로그래밍 기술

- 동적 웹페이지를 만들때 사용되는 자바 기반의 웹 애플리케이션 프로그래밍 기술
- 서블릿은 서버에서 실행되다 웹 클라이언트가 요청을 하면, 해당기능을 수행 후 웹 클라이언트에 결과를 전송해준다
- 서블릿의 생성은 Java Servlet API를 통해 구현이 된다.


## Servlet(서블릿)의 특징
- 클라이언트의 Request에 대해 동적으로 작동하는 웹 애플리케이션 컴포넌트이다
- 주로 HTML을 사용하여 Response 한다 (XML, JSON, 일반 텍스트 모두 가능)
- HTML 변경 시 Servlet을 재 컴파일해야 하는 단점이 있다
- JAVA의 스레드를 활용하여 동작한다
- HTTP 프로토콜 서비스를 지원하는 javax.servlet.http.HttpServlet 클래스를 상속받는다
- Spring MVC에서 컨트롤러로 사용될 수 있다(DispatcherServlet)
- 보안기능을 적용하기 쉽다

## Servlet의 동작과정

![images](https://github.com/jmxx219/CS-Study/assets/64017307/60a686cb-a46d-4c4d-9ba4-315ba9bfd110)


클라이언트가 웹서버에 요청하면 웹 서버는 JAR or WAS에 위임한다. WAS는 요청에 해당하는 서블릿을 실행하고 그 서블릿은 요청에 대한 기능을 수행 후 결과를 반환하고 클라이언트에 전송한다.

 - Tomcat, Jetty같은 웹 애플리케이션은 컨테이너는 서블릿의 라이프사이클을 담당함

```
1. 클라이언트 요청
2. HttpServletRequest, HttpServletResponse 객체 생성
3. Web.xml이 어느 서블릿에 대해 요청한 것인지 탐색
4. 해당하는 서블릿에서 service() 메소드 호출 
5. doGet() 또는 doPost() 호출 
6. 동적 페이지 생성 후 ServletResponse 객체에 응답 전송
7. HttpServletRequest, HttpServletResponse 객체 소멸
```

## 정적 웹 페이지와 서블릿 요청의 비교
- 정적 웹 페이지 :
    - 브라우저가 요청하면 WEB에서는 저장된 페이지를 제공함
- 동적 웹 페이지 :
    - 과거 정적 웹페이지에서 업그레이드 되면서 연산기능을 가지게 됨
    - 과거에 서버라고 불리던 것은 WEB과 WAS(Web Application Server)로 나뉘게 되었다.
    - WEB서버에서는 사용자 요청에 따라 정적 페이지를 제공하고, WAS는 사용자 요청 중에 '연산'이 필요한 부분만 맡아 결과를 제공한다
    - WAS는 연산결과를 WEB서버에 제공, 웹서버 정적 페이지를 만들어 사용자에게 전달함
    -> 이때 WAS에 연산을 당당하는 것이 'Servlet' 이다.

    `서블릿은 WAS안에 있는 서블릿 컨테이너 or 웹 컨테이너 공간에서 활동함.`

## 서블릿의 생명주기
![images](https://velog.velcdn.com/images%2Fcorone_hi%2Fpost%2Fd118c7e4-af36-444d-ad13-f60cc7d20814%2Fimage.png)

서블릿의 생명주기는 서블릿 로딩 ~ 소멸될때까지의 수명을 말한다.

1. 서블릿 로딩
2. 서블릿 인스턴스 생성
3. init() 메소드 호출
4. 각 클라이언트 요청에 대해 반복적으로 service() 메소드를 호출
5. 소멸하기 위해 destroy() 메소드 호출


### 주요 메소드

- init()
    - 서블릿의 일생동안 단 한번 호출된다
    - 디폴트 생성자를 이용하여 서블릿 객체를 생성하고 init()메소드를 사용하여 서블릿 객체를 초기화 할때 사용함
        ```java
        public void init(ServletConfig config) throws ServletException
        ```
- service()
    - 요청이 있을때 마다 호출된다
    - Http 메소드를 보고 doGet() or doPost() 호출할지 결정한다
        ```java
        public void service(ServletRequest request, ServletResponse response) throws ServletException, IOException
        ```
- destroy()
    - 서블릿의 일생동안 단 한번 호출된다
        - 서블릿이 소멸할때 호출하며, 자원해제와 관련된 작업을 한다
        ```java
        public void destroy()
        ```


## 서블릿 컨테이너

### 개념
- 구현되어 있는 서블릿 클래스의 규칙에 맞게 서블릿을 담고 관리해주는 컨테이너이다.
    - 서블릿을 관리해주는 컨테이너
- 클라이언트의 요청(Request)을 받고 응답(Response)할 수 있게, 웹 서버와 소켓으로 통신하며 대표적으로 톰캣(Tomcat)이 있다. 
    - 클라이언트에서 요청을 하면 컨테이너는 HttpServletRequest, HttpServletResponse 두 객체를 생성하여 post, get여부에 따라 동적인 페이지를 생성하여 응답을 보낸다.
- 톰캣은 웹 서버와 통신하여 JSP(Java Server Page)와 Servlet이 작동하는 환경을 제공해준다.

### 역할
- 웹 서버와 통신지원
    - 일반적으로 소켓 만들고 listen, accept등을 하고 웹 서버와 통신하지만, 서블릿 컨테이너는 이러한 기능을 API로 제공하여 생략할 수 있게 해준다.
    개발자가 서블릿에 구현해야할 비즈니스 로직에만 초점을 둘 수 있게 지원해준다.
- 서블릿 생명주기 관리
    - 서블릿 컨테이너는 서블릿 클래스 로딩하여 인스턴스화하고 init() 메소드를 호출, 적절한 서블릿 메소드 등을 호출하게 도와준다. 
    - 수명이 다 된 서블릿을 적절하게 가비지 콜텍터를 호출하여 필요없는 자원 낭비를 막아준다.
- 멀티스레드 지원 및 관리
    - 서블릿 컨테이너는 Request가 올때마다 새로운 자바 스레드를 하나씩 생성한다. 이후 HTTP 서비스 메소드를 실행하고 난 후 자동으로 소멸한다.
    - 따라서 동시에 여러 요청이 들어와도 멀티쓰레딩 환경에서 동시다발적인 작업을 관리할 수 있다.
- 선언적인 보안 관리
    - 서블릿 컨테이너는 보안관련 기능을 제다해주므로 보안관련 메소드를 구현하지 않아도 된다.
    - 일반적으로 보안관리는 XML 배포 서술자에다가 기록하므로, 보안에 대해 수정할 일이 생겨도 자바 소스 코드를 수정하여 다시 컴파일 하지 않아도 보안관리가 가능하다.
 

