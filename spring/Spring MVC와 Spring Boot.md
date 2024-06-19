# Spring MVC와 Spring Boot


## Spring MVC

<img src="https://velog.velcdn.com/images/hwan2da/post/e5669153-0ed2-4b93-a563-2692515eeff2/image.png" width="500" />

- Spring Framework의 핵심 모듈로 Model-View-Controller 패턴을 통해 명확한 역할 분리를 통해 계층을 관리하며 유지보수성을 높인다.
- 비즈니스 로직, 프레젠테이션 로직, 데이터 액세스 로직 등이 한 곳에 혼재되어 처리되던 기존의 문제를 해결하기 위해 만들어짐
- 아키텍처 및 플로우


<br>

### 핵심 구성요소

- `DispatcherServlet`
  - 모든 HTTP 요청을 중앙에서 처리하는 Front Controller
  - 요청을 받아 적절한 핸들러(Controller)를 반환하고, View를 통해 응답한다.
- `HandlerMapping`
  - 어떤 컨트롤러가 요청을 처리할지 결정
  - URL 패턴과 컨트롤러 메서드를 맵핑해준다.
- `Controller`
  - 실제 비즈니스 로직 처리
- `ViewResolver`
  - 논리적인 View 이름을 실제 View 객체로 변환하여 반환
  - JSP, Thymeleaf 등의 템플릿 엔진과 통합하여 View를 렌더링

<br>
<br>

## Spring Boot

- 애플리케이션의 복잡한 설정은 자동화하고, 개발자가 더 빠르게 애플리케이션을 구축할 수 있도록 설계된 Spring Framework의 구성요소
- 자동 설정, 스타터 종속성 관리, 독립 실행형 애플리케이션 등을 지원한다.
- 기존의 Spring Framework의 복잡성(XML 설정, 초기 설정의 복잡성 등)을 해결하기 위해 만들어진 프로젝트

<br>

### 특징

- 자동 설정(Auto Configuration)

  - 애플리케이션의 설정을 자동으로 수행한다. `@EnableAutoConfiguration` 을 통해 이뤄지며, 개발자는 필요한 설정을 최소한으로 작성할 수 있다.
  - 주로 웹 서버 설정, 데이터 베이스 연결 등이 있다.

- 스타터 종속성(Starter Dependencies)

  - 프로젝트에 필요한 의존성을 쉽게 추가할 수 있도록 여러 스타터 Dependencies를 제공한다.
  - 예를 들어 웹 애플리케이션 개발을 위한 `spring-boot-starter-web`, 데이터 접근을 위한 `spring-boot-starter-data-jpa` 등이 있다.

- 독립 실행형 애플리케이션(Stand-alone Application)

  - Spring Boot는 내장 서버(Tomcat, Jetty 등)을 포함하여 독립 실행형 애플리케이션을 만들 수 있다. 별도의 외부 서버 설정 없이 실행 가능.

- Spring Actutator

  - Spring Boot 애플리케이션의 모니터링과 관리 기능을 제공
  - 다양한 엔드포인트를 통해 애플리케이션의 메트릭스를 확인할 수 있다.
      - 예를 들어, `/actuator/loggers`: 로깅 관련 설정과 정보를 제공
  - 헬스 체크 기능을 통해 애플리케이션의 전반적인 상태를 확인할 수 있다.
      - 예를 들어, 데이터 베이스 연결 상태, 메시지 큐 상태 등

- Spring Initializr

  - Spring Initializr를 통해 프로젝트를 쉽게 시작할 수 있도록 해줌.
  - 프로젝트의 설정을 웹에서 간편하게 구성하고, 필요한 의존성을 선택하여 프로젝트 생성.

<br>
<br>