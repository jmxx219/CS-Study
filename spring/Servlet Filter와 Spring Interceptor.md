# Servlet Filter와 Spring Interceptor

<br/>

### 공통 관심사항 (cross-cutting concern)

- 애플리케이션의 여러 로직에서 공통으로 관심이 있는 것
    - ex) 등록, 수정 삭제, 조회 등등 로직에서 인증이 공통 관심사
        - 컨트롤러에서 로그인 여부(인증)을 체크하는 로직을 모두 작성할 수 있지만, 모든 컨트롤러 로직에 공통으로 로그인 여부를 체크해야 함
        - 이때, 향후 로그인과 관련된 로직이 수정되면 작성한 모든 로직을 수정해야하는 큰 문제점이 발생함
    - 이처럼 공통 업무에 관련된 코드를 로직마다 작성하면 중복 코드도 많아지게 되고, 프로젝트 단위가 커질수록 서버에 부하를 줄 수 있음
- 스프링은 공통적으로 여러 작업을 처리함으로써 중복된 코드를 제거할 수 있는 기능들을 지원함
    - 스프링의 [AOP](https://github.com/jmxx219/CS-Study/blob/main/Java-Spring/AOP.md)로도 해결 가능
    - 웹과 관련된 공통 관심사는 `서블릿 필터` 또는 `스프링 인터셉터`를 사용하는 것이 좋음
        - 웹과 관련된 공통 관심사를 처리할 때는 HTTP의 헤더나 URL 정보들이 필요함
        - 서블릿 필터나 스프링 인터셉터는 HttpServletRequest를 제공함

<br/>
<br/>

## 서블릿 필터(Servlet Filter)

<img src="https://miro.medium.com/v2/resize:fit:1200/1*Cyyy59ISyWUq5jkt9GvZrA.png" width="650"/>

<br>

- `Filter`는 J2EE 표준 스펙 기능으로, 요청과 응답을 거른 뒤 정제하는 역할을 함
- [디스패처 서블릿](https://github.com/jmxx219/CS-Study/blob/main/Java-Spring/DispatcherServlet.md)에 요청이 전달되기 전/후에 URL 패턴에 맞는 모든 요청에 대해서 부가 작업을 처리할 수 있는 기능을 제공함
    - 디스패처 서블릿은 스프링의 앞단에 존재하는 프론트 컨트롤러로이기 때문에 필터는 스프링 범위 밖에서 처리가 됨
    - 따라서 필터는 스프링 컨테이너가 아닌 [톰캣](https://github.com/jmxx219/CS-Study/blob/main/Java-Spring/Tomcat.md#tomcat-1)과 같은 웹 컨테이너([서블릿 컨테이너](https://github.com/jmxx219/CS-Study/blob/main/Java-Spring/Servlet.md#%EC%84%9C%EB%B8%94%EB%A6%BF-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88))에 의해 관리됨

<br/>

### 필터 소개

- 필터 흐름
  - `HTTP 요청` ➙ `WAS` ➙ `필터` ➙ `서블릿(디스패처 서블릿)` ➙ `컨트롤러`
    - 필터를 적용하면 필터가 호출된 후에 서블릿이 호출됨
    - 모든 고객의 요청 로그를 남기는 요구사항이 있다면 필터를 사용
    - 필터는 특정 URL 패턴에 적용할 수 있음(`/*`: 모든 요청에 필터 적용)
- 필터 제한
  - 로그인 사용자: `HTTP 요청` ➙ `WAS` ➙ `필터` ➙ `서블릿` ➙ `컨트롤러`
  - 비 로그인 사용자: `HTTP 요청` ➙ `WAS` ➙ `필터(적절하지 않은 요청이라 판단, 서블릿 호출 X)`
    - 필터는 로직에 의해서 적절하지 않은 요청이라고 판단하면 서블릿을 호출하지 않음
- 필터 체인
  - `HTTP 요청` ➙ `WAS` ➙ `필터 1` ➙ `필터 2` ➙ `필터 3` ➙ `서블릿` ➙ `컨트롤러`
    - 필터는 체인으로 구성되며 중간에 필터를 자유롭게 추가할 수 있음

<br/>

### 필터 인터페이스

```java
public interface Filter {
    public default void init(FilterConfig filterConfig) throws ServletException {}
    public void doFilter(ServletRequest request, ServletResponse response,
            FilterChain chain) throws IOException, ServletException;
    public default void destroy() {}
}
```

<br> 

```java
public class RequestLoggingFilter implements Filter {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        HttpServletRequest httpRequest = (HttpServletRequest) request;
        
        // 로그 정보 수집
        String requestURI = httpRequest.getRequestURI();
        String method = httpRequest.getMethod();
        String clientIP = request.getRemoteAddr();

        // 요청 로그 출력
        context.log("Received Request: " + method + " " + requestURI + " from " + clientIP);
        
        // HTTP 헤더 로그
        Enumeration<String> headers = httpRequest.getHeaderNames();
        while (headers.hasMoreElements()) {
            String headerName = headers.nextElement();
            String headerValue = httpRequest.getHeader(headerName);
            context.log("Header: " + headerName + " - " + headerValue);
        }

        // 다음 필터 또는 대상 서블릿으로 요청 전달
        chain.doFilter(request, response);
    }
}
```

- 필터 인터페이스를 구현하고 등록하면, 서블릿 컨테이너가 필터를 싱글톤 객체로 생성하고 관리함
    - `init()`: 필터 초기화 메소드로, 서블릿 컨테이너가 생성될 때 호출
    - `doFilter()`: 고객의 요청이 올 때 마다 해당 메서드가 호출됨
        - url-pattern에 맞는 모든 HTTP 요청이 디스패처 서블릿으로 전달되기 전에 웹 컨테이너에 의해 실행되는 메소드로, 필터의 로직을 구현함
        - `FilterChain`은 `doFilter()`를 통해 다음 필터 대상으로 요청을 전달해줌
    - `destroy()`: 필터 종료 메소드로, 서블릿 컨테이너가 종료될 때 호출

<br/>

#### 필터 등록

<br>

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Bean
    public FilterRegistrationBean loginCheckFilter() {
        FilterRegistrationBean<Filter> filterRegistrationBean = new FilterRegistrationBean<>();
        filterRegistrationBean.setFilter(new LoginCheckFilter());
        filterRegistrationBean.setOrder(1);
        filterRegistrationBean.addUrlPatterns("/*");
        return filterRegistrationBean;
    }
}
```
- `FilterRegistrationBean`
    - 필터를 등록하는 방법은 여러가지가 존재하는데 스프링 부트를 사용한다면 `FilterRegistrationBean` 사용함
        - `setFilter(new LoginCheckFilter())`: 등록할 필터를 지정
        - `setOrder(1)`: 필터는 체인으로 동작하기 때문에 순서가 필요함(낮을 수록 먼저 동작)
        - `addUrlPatterns("/*")`: 필터를 적용할 URL 패턴을 지정(여러 패턴 지정 가능)
            - 필터의 URL 패턴의 룰은 서블릿과 동일함
- ` @ServletComponentScan`, `@WebFilter(filterName = "loginCheckFilter", urlPatterns = "/*")`
    - 필터 등록이 가능하지만 필터 순서 조절은 불가능

<br/>
<br/>

## 스프링 인터셉터(Spring Interceptor)

<br/>

> 서블릿 필터는 서블릿이 제공, 스프링 인터셉터는 스프링 MVC가 제공  
> 서블릿 필터와 같이 둘 다 웹과 관련된 공통 관심 사항을 효과적으로 처리

<br/>

- Spring이 제공하는 기술로, `Dispatcher Servlet`이 컨트롤러를 호출하기 전과 후에 인터셉터가 끼어들어 요청과 응답을 참조하거나 가공할 수 있는 기능을 제공함
    - 웹 컨테이너에서 동작하는 필터와 달리 인터셉터는 **스프링 컨텍스트**에서 동작함
- 필터와는 적용되는 순서와 범위, 사용 방법이 다름
    - 인터셉터는 스프링 MVC 구조에 특화된 필터 기능을 제공함
    - 스프링 MVC를 사용하고, 특별히 필터를 꼭 사용해야 하는 상황이 아닐 경우에는 인터셉터를 사용하는 것이 더 편리함
    - 또한, 스프링 인터셉터는 서블릿 필터보다 더 정교하고 다양한 기능을 지원함

<br/>

### 인터셉터 소개

- 인터셉터 흐름
  - `HTTP 요청` ➙ `WAS` ➙ `필터` ➙ `서블릿(디스패처 서블릿)` ➙ `스프링 인터셉터` ➙ `컨트롤러`
    - 스프링 인터셉터는 디스패처 서블릿과 컨트롤러 사이에서 컨트롤러 호출 직전에 호출됨
    - 스프링 MVC가 제공하는 기능이기 때문에 디스패처 서블릿 이후에 등장
    - 스프링 인터셉터에도 URL 패턴 적용 가능, 서블릿 URL 패턴과 다르며 매우 정밀하게 설정 가능
- 인터셉터 제한
  - 로그인 사용자: `HTTP 요청` ➙ `WAS` ➙ `필터` ➙ `서블릿` ➙ `스프링 인터셉터` ➙ `컨트롤러`
  - 비 로그인 사용자: `HTTP 요청` ➙ `WAS`➙ `필터` ➙ `서블릿` ➙ `스프링 인터셉터(적절하지 않은 요청이라 판단, 컨트롤러 호출 X)`
    - 인터셉터도 로직에 의해 적절하지 않은 요청이라고 판단하면 컨트롤러를 호출하지 않음
- 인터셉터 체인
  - `HTTP 요청` ➙ `WAS` ➙ `필터` ➙ `서블릿` ➙ `인터셉터1` ➙ `인터셉터2` ➙ `컨트롤러`
    - 체인으로 구성되어 중간에 자유롭게 인터셉터를 추가할 수 있음

<br/>

### 인터셉터 인터페이스

<br> 

```java
public interface HandlerInterceptor {
    default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {}
    default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable ModelAndView modelAndView) throws Exception {}
    default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable Exception ex) throws Exception {}
}
``` 

<br> 

```java
@Slf4j
public class LoginCheckInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        HttpSession session = request.getSession();
        if(session == null || session.getAttribute(SessionConst.LOGIN_MEMBER) == null) {
            log.info("미인증 사용자 요청");
            return false;
        }
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
        ModelAndView modelAndView) throws Exception {
        log.info("postHandle [{}]", modelAndView);
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response,
        Object handler, Exception ex) throws Exception {
        if (ex != null) {
            log.error("afterCompletion error!!", ex);
        }
    }
}
```

<br> 

- `HandlerInterceptor` 인터페이스를 구현함
    - 컨트롤러 호출 전(`preHandle()`), 컨트롤러 호출 후(`postHandle()`), 요청 완료 이후(`afterCompletion()`)와 같이 단계적으로 세분화되어 있음
        - 서블릿 필터의 경우 단순하게 `doFilter()` 하나만 제공됨
    - 어떤 컨트롤러(`handler`)가 호출되는지 호출 정보와 어떤 `ModelAndView`가 반환되는지 응답 정보도 받을 수 있음
        - 서블릿 필터의 경우 단순히 `request`와 `response`만 제공함
- `preHandle()`
    -  Controller가 호출되기 전에 실행됨
    -  컨트롤러 이전에 처리해야 하는 전처리 작업이나 요청 정보를 가공하거나 추가하는 경우에 사용
- `postHandle()`
    - Controller가 호출된 후에 실행됨 (View 렌더링 전)
    - 컨트롤러가 반환하는 ModelAndView 타입의 정보가 제공되지만, 최근에는 RestAPI 기반의 컨트롤러를 개발하면서 자주 사용되지 않음
    - 컨트롤러 하위 계층에서 예외가 발생하면 postHandle은 호출되지 않음
- `afterCompletion()`
    - 모든 뷰에서 최종 결과를 생성하는 일을 포함해 모든 작업이 완료된 후에 실행됨 (View 렌더링 후)
    - 항상 호출되기 때문에 예외와 무관하게 공통 처리를 할 때 사용함
        - 예외가 발생하면 예외(`ex`)를 파라미터로 받아서 어떤 예외가 발생했는지 로그로 출력할 수 있음



<br/>

#### 인터셉터 등록

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginCheckInterceptor())
                .order(1)
                .addPathPatterns("/**")
                .excludePathPatterns("/", "/members/add", "/login", "/logout",
                        "/css/**", "/*.ico", "/error");
    }
}
```

- `WebMvcConfigurer`가 제공하는 `addInterceptors()`를 사용해서 인터셉터를 등록함
  - `registry.addInterceptor(new LoginCheckInterceptor())`: 인터셉터를 등록
  - `order(1)`: 인터셉터의 호출 순서를 지정(낮을 수록 먼저 호출)
  - `addPathPatterns("/**")`: 인터셉터를 적용할 URL 패턴을 지정(`/**`: 모든 요청에 적용)
  - `excludePathPatterns("/css/**", "/*.ico", "/error")`: 인터셉터에서 제외할 패턴을 지정
- URL 패턴
  - `addPathPatterns()`와 `excludePathPatterns()`로 필터보다 더 정밀하게 URL 패턴 지정 가능
  - 스프링이 제공하는 URL 경로는 서블릿이 제공하는 URL 경로와 완전히 다름
    - 더욱 자세하고 세밀하게 설정 가능([PathPattern](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/util/pattern/PathPattern.html) 참고)


<br/>

### 스프링 인터셉터 호출 흐름

<br>

> [디스패처 서블릿의 동작 과정 참고](https://github.com/jmxx219/CS-Study/blob/main/Java-Spring/DispatcherServlet.md#%EB%94%94%EC%8A%A4%ED%8C%A8%EC%B2%98-%EC%84%9C%EB%B8%94%EB%A6%BF%EC%9D%98-%EB%8F%99%EC%9E%91-%EA%B3%BC%EC%A0%95)

<br/>
<img width="600" src="https://github.com/jmxx219/CS-Study/assets/52346113/4304949f-d623-4f72-ba98-3563714abab5">
<br/>

1. 클라이언트 - HTTP 요청
2. Dispatcher Servlet
   1. `preHandle` 호출
      - 컨트롤러 호출 전에 호출(정확히는 핸들러 어댑터 호출 전에 호출)
        - `preHandle`의 응답값이 `true`이면 다음으로 진행, `false`이면 더 진행 x
        - `false`인 경우, 나머지 인터셉터는 물론이고 핸들러 어댑터도 호출되지 않음
   2. 핸들러 매핑 정보에서 핸들러 조회
   3. 핸들러 어댑터 목록에서 핸들러를 처리할 수 있는 핸들러 어댑터 조회
   4. 핸들러 어댑터의 handle(handler) 호출
3. Handler Adapter
   1. handler(실제 컨트롤러) 호출
   2. ModelAndView 반환
4. Dispatcher Servlet
   1. `postHandle` 호출
      - 컨트롤러 호출 후에 호출됨(정확히는 핸들러 어댑터 호출 후에 호출)
5. ViewResolver - View 반환 
6. Dispatcher Servlet
   1. render(model) 호출 
   2. `afterCompletion` 호출 
      - 뷰가 렌더링 된 이후에 호출됨
7. View - 클라이언트에 HTML 응답


<br/>
<br/>

## 필터(Filter)와 인터셉터(Interceptor) 비교

||Filter|Intercptor|
|----|------|------|
|관리 주체 컨테이너|서블릿 컨테이너|스프링 컨테이너|
|Request, Response 객체 조작 가능 여부|O|X|
|처리 순서|등록된 순서대로 처리되며(지정한 순번이 있으면 순번대로 처리), 마지막 Filter에서 처리가 완료되면 실제 요청을 처리하는 Servlet이 호출됨|지정한 순번대로 처리되며, 마지막 Interceptor에서 처리가 완료되면 실제 요청을 처리하는 HandlerAdapter(controller)가 호출됨|
|요청과 응답|HTTP 요청과 응답의 처리 전/후로 조작|HandlerApdaptor(controller) 요청과 응답의 처리 전/후로 조작|
|구현 방법| java.servlet 패키지의 Filter 인터페이스 구현|org.springframework.web.servlet 패키지의 HandlerInterceptor 인터페이스 구현|
|용도|보안 및 인증/인가 관련 작업 <br> 모든 요청에 대한 로깅 또는 검사 <br> 이미지/데이터 압축 및 문자열 인코딩 <br> Spring과 분리되어야 하는 기능|세부적인 보안 및 인증/인가 공통 작업 <br> API 호출에 대한 로깅 또는 검사 <br> Controller로 넘겨주는 정보(데이터)의 가공|


<br/>
<br/>

## ➕ Spring MVC 처리 흐름 참고

<img width="600" src="https://aaronryu.github.io/2021/02/14/a-tutorial-for-spring-mvc-and-security/order-of-filters-and-interceptors.png">
<br/>
<br/>

## Ref

- [필터(Filter)와 인터셉터(Interceptor)의 개념 및 차이](https://dev-coco.tistory.com/173)
- [인프런 spring mvc2 강의](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-2)
- [Spring MVC, Security 동작 원리와 처리 흐름](https://aaronryu.github.io/2021/02/14/a-tutorial-for-spring-mvc-and-security/)


<br/>
