# 디스패처 서블릿 DispatcherServlet

**디스패처 서블릿**(DispatcherServlet)은 스프링 MVC가 사용하는 핵심 개념이다.  
클라이언트로부터 어떤 요청이 들어오면 서블릿 컨테이너가 요청을 받는다. 이때 공통된 작업은 디스패처 서블릿에서
처리하고, 이외 작업은 다른 세부 컨트롤러로 위임한다.  

디스패처 서블릿의 상속 구조는 아래와 같다.  

**DispatcherServlet -> FrameworkServlet -> HttpServletBean -> HttpServlet**

```java
public class DispatcherServlet extends FrameworkServlet {
}

public abstract class FrameworkServlet extends HttpServletBean {
}

public abstract class HttpServletBean extends HttpServlet {
}
```

이제 차근차근 살펴보자.


> **MVC**란 
> 
> Model-View-Controller 의 약자로 스프링이 사용하고 있는 디자인 패턴이다.
> 모델은 데이터 가공을 담당하는 컴포넌트이고, 뷰는 사용자에게 보여지는 인터페이스, 컨트롤러는 모델과 뷰를 이어주는 다리(Bridge) 역할을 한다. 

> **자바 서블릿**(Servlet)이란
>
> 자바를 사용하여 웹페이지를 동적으로 생성하는 서버측 프로그램 혹은 그 사양을 말하며, 흔히 '서블릿'이라고 불린다.   
> 웹 요청과 응답의 흐름을 간단히 메서드 호출만으로 체계적으로 다룰 수 있게 해주는 기술이다.
> 서블릿 컨테이너는 이런 서블릿을 담은 컨테이너로 예시로 톰캣이 있다.  

<br>

## 프론트 컨트롤러 패턴 Front Controller Pattern

MVC 패턴 중 컨트롤러가 많아지고, 컨트롤러에서 공통으로 처리해야 하는 부분이 증가한다면 코드 중복이 발생할 것이다. 만약 메서드로 분리한다고
하여도 결국에는 메서드를 호출해야 하고, 실수로 호출하지 않을시에는 문제가 생긴다.  
**프론트 컨트롤러 패턴**을 사용하여 이러한 문제를 해결할 수 있다.  

프론트 컨트롤러 패턴은 컨트롤러 호출 전에 공통 기능 수행을 위한 컨트롤러를 만들어 어떤 요청이던 
이 컨트롤러를 지나가도록 만드는 패턴이다. 


* 프론트 컨트롤러 서블릿 하나로 클라이언트 요청을 전부 받는다.
* 입구가 하나이기 때문에 여기서 공통된 기능을 수행한다.
* 프론트 컨트롤러에서 서블릿을 사용하면 프론트 컨트롤러를 제외한 나머지 컨트롤러에서는 서블릿을 사용하지 않아도 된다. 

스프링 MVC의 **디스패처 서블릿**이 바로 이 프론트 컨트롤러 패턴을 사용한다.  

그렇다면 공통된 기능을 수행한 다음 디스패처 서블릿은 어떻게 요청을 세부 컨트롤러를 찾을 수 있을까  
바로 핸들러 어댑터를 사용한다.  


<br>

## 어댑터 패턴  Adapter Pattern

위의 상황으로 예시를 들어보면, 서블릿 컨테이너에서 요청이 오면 모두 디스패처 서블릿을 거쳐 세부 컨트롤러로 가는데, 
각각의 세부 컨트롤러 인터페이스는 완전히 다른 인터페이스이다. 때문에 서로간의 호환이 불가능한 상태인데, 어댑터 패턴을 
사용하여 처리할 수 있다.  

어댑터 패턴은 호환되지 않는 인터페이스들을 연결하는 디자인 패턴이다. 이 패턴은 기존의 클래스를 수정하지 않고도, 
특정 인터페이스를 필요로 하는 코드에서 사용할 수 있게 도와준다.  
어댑터용 인터페이스를 만들어 처리할 수 있는 컨트롤러를 판단하여 그 컨트롤러를 호출한다.  

스프링 MVC의 **핸들러 어댑터**(HandlerAdapter) 인터페이스가 바로 어댑터 패턴을 사용한다.  

여기서 스프링 MVC가 사용하는 어댑터의 이름이 컨트롤러 어댑터가 아니라 핸들러 어댑터인 이유가 궁금할 수도 있다. 
이유는 어댑터를 사용하기 때문에, 컨트롤러 뿐만 아니라 어댑터가 지원하기만 하면, 어떤 것이라도 URL에 매핑하여 사용할 수 있기 
때문이다.  

그럼 이제 스프링 MVC의 디스패처 서블릿의 동작 방식을 살펴보자.  

<br>

## 디스패처 서블릿의 동작 과정

<br>

<img alt="디스패처 서블릿 동자 과정" height="400" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/b3afda6d-bf0e-49d2-bb59-56082a28ceb7"/>

<br>

1. `DispatcherServlet`으로 클라이언트의 **웹 요청**(HttpServletRequest)이 들어온다.
2. 웹 요청을 `HandlerMapping`에 위임하여 해당 요청을 처리할 Handler(Controller)를 탐색한다.
3. 찾은 Handler를 실행할 수 있는 `HandlerAdapter`를 탐색한다. 
4. 찾은 `HandlerAdapter`를 사용해서 Handler의 메서드를 실행한다
5. 이때 Handler의 반환값은 `Model`과 `View`다. 
6. View 이름을 `ViewResolver`에게 전달하고, ViewResolver는 해당하는 View 객체를 반환한다. 
7. `DispatcherServlet`은 View 에게 Model을 전달하고 화면 표시를 요청한다. 이때, Model이 null이면 View를 그대로 사용한다. 반면 값이 있으면 View에 Model 데이터를 렌더링한다.
8. 최종적으로 DispatcherServlet은 **View 결과**(HttpServletResponse)를 클라이언트에게 반환한다.

위 흐름은 `@Controller` 기준이다. `@RestController`의 경우 6, 7과정이 생략된다. 즉, ViewResolver를 타지 않고 반환값에 
알맞는 MessageConverter를 찾아 응답을 작성한다.  

<br>


## 디스패처 서블릿 설정 방법

디스패처 서블릿을 구현하는 방법에는 2가지가 있다. 

1. `web.xml`에 DispatcherServlet 선언 방법

    xml에 DispatcherServlet을 선언하고 URI로 요청 경로를 매핑하여 사용할 수 있다.  
    
    아래는 표준 J2EE 서블릿 설정 예시이다.  
    
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

    DispatcherServlet을 상속하여 `@WebServlet` 애노테이션에 urlPatterns를 지정하여 사용할 수 있다. 
    `@WebServlet` 애노테이션을 사용하려면 `Httpservlet`을 상속해야 하는데 위에서 말했듯이 DispatcherServlet은 HttpServlet을 상속하기 때문에
    서블릿 클래스에서 DispatcherServlet을 상속해도 문제 없다.  
    
    ```java
    @WebServlet(name = "helloServlet", urlPatterns = "/hello")
    public class HelloServlet extends DispatcherServlet {
    }
    ```

<br>


#### ref

[스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1/)  
[[Spring] 📚 Dispatcher Servlet 이해하기](https://velog.io/@betterfuture4/Spring-Dispatcher-Servlet-%EC%A0%95%EB%A6%AC)  
[[Spring] Dispatcher-Servlet(디스패처 서블릿)이란? 디스패처 서블릿의 개념과 동작 과정](https://mangkyu.tistory.com/18)  
[DispatcherServlet - Part 1](https://tecoble.techcourse.co.kr/post/2021-06-25-dispatcherservlet-part-1/)  
[자바 어댑터 패턴은 어떻게 쓰일까?](https://yozm.wishket.com/magazine/detail/2077/)  
