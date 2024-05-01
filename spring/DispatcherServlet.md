# DispatcherServlet

> [서블릿 참고](https://github.com/jmxx219/CS-Study/blob/main/spring/Servlet.md)

<br>

## DispatcherServlet이란?

- 스프링 MVC가 사용하는 핵심 개념
- HTTP 프로토콜로 들어오는 모든 요청을 가장 먼저 받아 적합한 컨트롤러에 위임해주는 프론트 컨트롤러로 정의함
    - 클라이언트로부터 어떤 요청이 들어오면 `서블릿 컨테이너`가 요청을 받는데, 이때 공통된 작업은 디스패처 서블릿에서 처리하고, 이외 작업은 다른 세부 컨트롤러로 위임함
- 과거에는 모든 서블릿을 URL 매핑을 위해 web.xml에 모두 등록해주어야 했음
    - DispatcherServlet의 등장으로 애플리케이션으로 들어오는 모든 요청을 핸들링해주고 공통 작업을 처리하면서 web.xml의 의존을 낮출 수 있게 됨

<br>

### 상속 구조(계층 구조)

- `DispatcherServlet` ➡ `FrameworkServlet` ➡ `HttpServletBean` ➡ `HttpServlet` ➡ `GenericServlet` ➡ `Servlet`
    ```java
    public class DispatcherServlet extends FrameworkServlet {
    }
    
    public abstract class FrameworkServlet extends HttpServletBean {
    }
    
    public abstract class HttpServletBean extends HttpServlet {
    }
    ```
- 디스패처 서블릿도 [서블릿](https://github.com/jmxx219/CS-Study/blob/main/spring/Servlet.md)의 일종으로, HttpServlet을 상속함


<br>

### 프론트 컨트롤러 패턴(Front Controller Pattern)

- 도입 배경
  - MVC 패턴 중 컨트롤러가 많아지고, 컨트롤러에서 공통으로 처리해야 하는 부분이 증가한다면 코드 중복이 발생함
    - 각 컨트롤러마다 공통 로직을 만들어야 했음
  - 프론트 컨트롤러 도입 후에는 공통 로직은 프론트 컨트롤러에서 처리하고, 각자 처리해야하는 로직은 각 컨틀롤러에 처리함
    - 공통의 관심사를 별도로 모으는 역할
- 특징
  - 서블릿 하나로 클라이언트의 요청을 모두 받음
  - 프론트 컨트롤러가 요청에 맞는 컨트롤러를 찾아서 호출함
  - 공통 처리 가능
  - 프론트 컨트롤러를 제외한 나머지 컨트롤러는 서블릿을 사용하지 않아도 됨 ➡ 프론트 컨트롤러가 나머지 컨트롤러를 직접 호출해주기 때문
  - 스프링 웹 MCV의 핵심으로, `DispatcherServlet`이 FrontController 패턴으로 구현됨
- 도입 이후 처리과정
    1. 클라이언트 - HTTP 요청
    2. Front Controller - URL 매핑 정보에서 컨트롤러 조회 후 컨트롤러 호출
    3. Controller - ModelAndView 반환
    4. Front Controller - ViewResolver 호출
    5. ViewResolver - View*반환
    6. Front Controller - 클라이언트에 View 응답을 전달

<br>

### 어댑터 패턴(Adapter Pattern)

- 도입 배경
  - 서블릿 컨테이너에서 요청이 오면 모두 디스패처 서블릿을 거쳐 세부 컨트롤러로 가는데, 각각의 세부 컨트롤러 인터페이스는 완전히 다른 인터페이스이기 때문에 서로 간의 호환이 불가능한 상태임
  - 이때 어댑터 패턴을 사용하면 기존 구조를 유지하면서 프레임워크의 기능을 확장할 수 있음
    - 기존에는 한 가지 방식의 인터페이스만 사용가능했지만, 어댑터 패턴을 이용하여 프론트 컨트롤러가 다양한 인터페이스를 처리할 수 있도록 함
- 특징
  - 어댑터 패턴은 호환되지 않는 인터페이스들을 연결하는 디자인 패턴으로, 기존의 클래스를 수정하지 않고도 특정 인터페이스를 필요로 하는 코드에서 사용할 수 있게 도와줌
      - 어댑터용 인터페이스를 만들어 처리할 수 있는 컨트롤러를 판단하여 그 컨트롤러를 호출함
  - `스프링 MVC의 핸들러 어댑터(HandlerAdapter) 인터페이스`가 바로 어댑터 패턴을 사용함
      - 스프링 MVC가 사용하는 어댑터의 이름이 컨트롤러 어댑터가 아니라 핸들러 어댑터인 이유는 컨트롤러 뿐만 아니라, 어댑터가 지원하기만 하면 어떤 것이라도 URL에 매핑하여 사용할 수 있기 때문
- 도입 이후 처리과정
  1. 클라이언트 - HTTP 요청
  2. Front Controller
     - Handler Mapping 정보에서 핸들러 조회
     - 핸들러를 처리할 수 있는 Handler Adapter 조회
     - 핸들러 어댑터의 handle(handler) 호출
  3. Handler Adapter
     - handler(실제 Controller) 호출
     - ModelView 반환
  4. Front Controller - **ViewResolver** 호출
  5. ViewResolver - **View** 반환
  6. Front Controller - 클라이언트에 View 응답을 전달

<br>
<br>

## 디스패처 서블릿의 동작 과정

<br>

<img alt="디스패처 서블릿 동자 과정" height="400" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/b3afda6d-bf0e-49d2-bb59-56082a28ceb7"/>

<br>
<br>

1. `DispatcherServlet`으로 클라이언트의 **웹 요청**(HttpServletRequest)이 들어옴
2. 웹 요청을 `HandlerMapping`에 위임하여 해당 요청을 처리할 `Handler(Controller)`를 탐색
3. 찾은 Handler를 실행할 수 있는 `HandlerAdapter`를 탐색
4. 찾은 `HandlerAdapter`를 사용해서 Handler의 메서드를 실행
5. 이때 Handler의 반환값은 `Model`과 `View`임
6. View 이름을 `ViewResolver`에게 전달하고,` ViewResolver`는 해당하는 View 객체를 반환함
7. `DispatcherServlet`은 View 에게 Model을 전달하고 화면 표시를 요청하는데, 이때 Model이 null이면 View를 그대로 사용함
    - 반면 값이 있으면 View에 Model 데이터를 렌더링함
9. 최종적으로 DispatcherServlet은 **View 결과**(HttpServletResponse)를 클라이언트에게 반환함

> 위 흐름은 `@Controller` 기준이고,  `@RestController`의 경우 6, 7과정이 생략된다.  
> 즉, ViewResolver를 타지 않고 반환값에 알맞는 `MessageConverter`를 찾아 응답을 작성한다.

<br>
<br>

## 디스패처 서블릿 설정 방법

> 디스패처 서블릿을 구현하는 방법에는 2가지가 있다.

1. `web.xml`에 DispatcherServlet 선언 방법
    - xml에 DispatcherServlet을 선언하고 URI로 요청 경로를 매핑하여 사용할 수 있음
    - 표준 J2EE 서블릿 설정 예시
        ```xml
        <web-app>
     
            <servlet>
                <servlet-name>example</servlet-name>
                <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
                <load-on-startup>1</load-on-startup>
            </servlet>
     
            <servlet-mapping>
                <servlet-name>example</servlet-name>
                <url-pattern>*.form</url-pattern>
            </servlet-mapping>
     
        </web-app>
        ```
2. DispatcherServlet 상속 후 `@Webservlet` 애노테이션 사용 방법
   ```java
   @WebServlet(name = "helloServlet", urlPatterns = "/hello")
   public class HelloServlet extends DispatcherServlet {
   }
   ```
    - DispatcherServlet을 상속하여 `@WebServlet` 애노테이션에 urlPatterns를 지정하여 사용할 수 있음
    - `@WebServlet` 애노테이션을 사용하려면 `Httpservlet`을 상속해야 하는데 위에서 말했듯이 DispatcherServlet은 HttpServlet을 상속하기 때문에 서블릿 클래스에서 DispatcherServlet을 상속해도 문제 없음


<br>
<br>

### Ref

- [Spring DispatcherServlet(디스패처서블릿) 개념부터 동작 과정까지](https://zzang9ha.tistory.com/441)
- [스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1/)  
- [[Spring] 📚 Dispatcher Servlet 이해하기](https://velog.io/@betterfuture4/Spring-Dispatcher-Servlet-%EC%A0%95%EB%A6%AC)  
- [[Spring] Dispatcher-Servlet(디스패처 서블릿)이란? 디스패처 서블릿의 개념과 동작 과정](https://mangkyu.tistory.com/18)  
- [DispatcherServlet - Part 1](https://tecoble.techcourse.co.kr/post/2021-06-25-dispatcherservlet-part-1/)  
- [자바 어댑터 패턴은 어떻게 쓰일까?](https://yozm.wishket.com/magazine/detail/2077/)
