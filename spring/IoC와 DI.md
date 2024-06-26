# Ioc와 DI

<br/>

## 객체지향 설계와 스프링

- 스프링을 사용하기 전
  - 순수하게 자바로 OCP, DIP 원칙을 지키면서 개발해보면, 결국 스프링 프레임워크를 만들게 됨(정확히는 DI 컨테이너)


- 스프링은 `다형성`을 극대화해서 역할과 구현을 편리하게 다룰 수 있도록 지원함
  - 의존관계 주입(`DI`), `DI 컨테이너` 기술로 `OCP`와 `DIP`를 가능하게 지원함
  - 클라이언트 코드의 변경 없이 기능 확장 가능

<br/>

## 스프링 컨테이너와 스프링 빈

```
Spring Container: 스프링에서 자바 객체들을 관리하는 공간
Spring Bean: 스프링에 의해 생성되고 관리되는 자바 객체
```

<br/>

### 스프링 컨테이너
<img src="https://github.com/sejong-bucket/sejong-bucket-v0/assets/74135929/3957a3a9-13f0-49e9-8dfc-c417a1fe887b" width=350>

스프링 컨테이너 내부에는 스프링 Bean객체를 key-value형식으로 저장할수 있는 공간이 존재합니다.

해당 공간에 빈이름(key)과 빈 객체(value)를 등록해 DI와 IoC에 사용되게 됩니다.

<br>

#### 빈 등록

@Configuration이 붙은 클래스가 빈 등록을 위한 설정파일이 되어, 클래스 내의 @Bean이 붙은 메서드가 스프링 빈이 되어 등록됩니다.

<br>

#### 등록방법

스프링 프로젝트를 생성하면 기본적으로 Application클래스가 존재합니다. 

클래스에는 `@SpringBootApplication`가 붙어있고, 해당 어노테이션의 메타어노테이션으로 `@ComponentScan`이 붙어 `@Component`를 자동으로 컨테이너에 등록해줍니다.
>`@ComponentScan`의 경우 `@Component`가 붙은 객체를 자동으로 스프링 빈으로 등록해주는 역할을 합니다.

하지만 @Configuration이 붙는 설정파일의 경우 @Component가 없는데 어떻게 등록이 될까요? 

@Configuration의 메타데이터에 @Component가 붙어 자동 등록됩니다.



<img src="https://github.com/sejong-bucket/sejong-bucket-v0/assets/74135929/4904ad0b-f177-4e1b-ab92-9ac965cf4ddc" width=350>

<br>
<br>
<br>

### 빈 생명주기(Bean LifeCycle)

<img src="https://github.com/sejong-bucket/sejong-bucket-v0/assets/74135929/3bd9e0c9-2efe-4e08-a39f-6859584b2cdd" width="350">

- 스프링 빈 이벤트 라이프 사이클
  - `스프링 IoC 컨테이너 생성 → 스프링 빈 생성 → 의존관계 주입 → 초기화 콜백 메소드 호출 → 사용 → 소멸 전 콜백 메소드 호출 → 스프링 종료`

<details>
<summary>NetworkClient</summary>
<div markdown="1">

```java
public class NetworkClient {
    private String url;

    public NetworkClient() {
        System.out.println("생성자 호출 , url=" + url);
        connect();
        call("초기화 연결 메세지");
    }

    public void call(String msg) {
        System.out.println("call= " + url + " message= " + msg);
    }

    public void connect() {
        System.out.println("connect= " + url);
    }

    public void disconnect() {
        System.out.println("close= " + url);
    }

    public void setUrl(String url) {
        this.url = url;
    }
}
```

</div>
</details>

<details>
<summary>BeanLifeCycleTest</summary>
<div markdown="1">

```java
public class BeanLifeCycleTest {

  @Test
  public void lifeCycleTest() {
    ConfigurableApplicationContext ac = new AnnotationConfigApplicationContext(LifeCycleConfig.class);
    NetworkClient client = ac.getBean(NetworkClient.class);
    ac.close();
  }

  @Configuration
  static class LifeCycleConfig {
    @Bean
    public NetworkClient networkClient() {
      NetworkClient networkClient = new NetworkClient();
      networkClient.setUrl("http://hello-spring.dev");

      return networkClient;
    }
  }
}
```

</div>
</details>

<br>

**실행결과**

<img src="https://github.com/sejong-bucket/sejong-bucket-v0/assets/74135929/bd64281b-afaf-46f2-8052-4f5ecd9d281c" width=350>

실행결과는 url이 모두 null이다. 객체생성시점에 url이 입렫되지 않고, 생성완료후 setter를 통해 url을 넣었기 때문이다.

<br>

**빈 생명주기 콜백의 필요성**

- 스프링 빈은 객체 생성후 의존관계를 주입한 뒤 사용할 준비가 완료된다. 스프링 빈에서 초기화 작업을 하고 싶다면 의존관계가 모두 주입된 다음 호출해야한다.


- 개발자 입장에서 의존관계가 모두 주입되는 시점을 알기 위해서 스프링에서는 스프링 빈이 의존관계 주입이 완료되면 콜백 메서드를 통해 초기화 시점을 알려주는 기능을 제공한다.


- 더하여 스프링 컨테이너의 소멸 직전 소멸 콜백을 주어 스프링 컨테이너가 종료되기 전 로직을 수행할수 있다.

<br>

**스프링의 빈 생명주기 콜백 함수 지원**
  - 스프링은 의존관계 주입이 완료되면 스프링 빈에게 콜백 메서드를 통해서 초기화 시점을 알려주는 다양한 기능을 제공함

  
  - 스프링 컨테이너가 종료되기 직전에도 소멸 콜백을 주어 안전하게 종료작업을 진행할 수 있음
  

  - 빈 생명주기 콜백 함수
    1. 인터페이스(`InitializingBean`, `DisposableBean`)
    2. 설정 정보에 초기화 메소드, 종료 메소드 지정(`@Bean(initMethod = "init", destroyMethod = "close")`)
    3. `@PostConstruct`, `@PreDestroy` 어노테이션 지원


<br/>

## 스프링 빈 스코프

- 빈 스코프는 빈이 존재할 수 있는 범위를 뜻함
  - 스프링은 다양한 스코프를 지원함


- 싱글톤(Singleton) : 기본 스코프, 스프링 컨테이너의 시작과 종료까지 유지되는 가장 넓은 범위의 스코프이다.


- 프로토타입(Prototype) : 스프링 컨테이너는 프로토타입 빈의 생성과 의존관계 주입까지만 관여하고 더는 관리하지 않는 매우 짧은 범위의 스코프이다.


- 웹 관련 스코프
  - request : 웹 요청이 들어오고 나갈 때까지 유지되는 스코프
  - session : 웹 세션이 생성되고 종료될 때까지 유지되는 스코프
  - application : 웹의 서블릿 컨텍스트와 같은 범위로 유지되는 스코프

**싱글톤**

<img src="https://github.com/sejong-bucket/sejong-bucket-v0/assets/74135929/c1d3f053-dad1-4c07-8195-2b59cc4ef1cc" width=350>

- 싱글톤 스코프의 빈을 조회하면 스프링 컨테이너는 항상 같은 인스턴스의 빈을 반환한다.

<br>

**프로토 타입**

<img src="https://github.com/sejong-bucket/sejong-bucket-v0/assets/74135929/9455ab05-245f-4dc8-ba34-ab9ce016ef45" width="350">

- 클라이언트가 프로토타입의 빈을 조회요청
- 스프링 컨테이너는 요청시점에 프로토타입 빈을 생성하고, 의존관계 주입
- 생성된 프로토타입 빈을 반환

클라이언트에게 빈을 반환한 이후에는 생성된 프로토타입 빈을 관리하지 않아 @PreDestory와 같은 종료 콜백 메서드가 호출되지 않는다.

<br>

**웹 스코프**
- 웹 환경에서만 동작하는 스코프
- 스프링이 해당 스코프의 종료시점까지 관리하며, 종료 메서드도 호출된다.
>
> - request : HTTP요청이 들어오고 나갈때까지 유지되는 스코프로 각각의 요청마다 별도의 빈 인스턴스가 생성 및 관리된다.
> - session : HTTP Session과 동일한 생명주기를 가진다
> - application : 서블릿 컨텍스트(ServletContext)와 같은 생명주기를 가지는 스코프
> - websocket : 웹소켓과 동일한 생명주기를 가지는 스코프

<br>
<br>

### 의존관계 주입 `DI`(Dependency Injection)
- 애플리케이션 **실행 시점(런타임)** 에 **스프링컨테이너**에서 객체를 생성하여 의존관계가 맺는 과정
  - 객체 인스턴스를 생성하고, 그 참조 값을 전달해서 연결하는 과정

  
만약 DI가 없다면 직접 의존하고 싶은 클래스의 인스턴스를 생성하여 사용해야 합니다.
```java
//Sport는 인터페이스, Basketball은 Sport의 구현체이다.

public class GroundService{
	private final Sport sport=new Basketball();
}
```

따라서 만약 Basketball를 다른 클래스로 교체하고 싶다면 GroundService의 코드를 수정해야 한다는 문제점이 생깁니다.

따라서 스프링은 애플리케이션 실행시점에 스프링 컨텍스트에 객체(빈)를 싱글톤 패턴으로 미리 생성하여 필요에 따라 하나의 객체만 생성하여 주입해주는 DI를 3가지 방식으로 지원해줍니다
>참고 : @Autowired는 스프링 컨텍스트에서 해당되는 타입의 빈을 찾아 주입해주는 어노테이션입니다!

<br>

### 스프링의 DI 방식

- Setter Injection (Setter 주입)
- Field Injection (필드 주입)
- Contruct Injection (생성자 주입)
  
<br>

**Setter Injection (Setter 주입)**
```java
public class GroundService{
	private Sport sport;
    @Autowired
    public void setSport(Sport sport){
    	this.sport=sport;
    }
}
```
set메서드를 사용하여 메서드를 public으로 열어둬야하기 때문에 의존성이 어디서든 변경될 가능성이 있습니다. 물론 의존성을 변경해야하는 상황도 존재하지만 거의 존재하지 않기때문에 지양합니다.

<br>

**Field Injection (필드 주입)**
```java
public class GroundService{
@Autowired
private Sport sport;
}
```
필드 주입을 사용하면 IDE상에서 Field injection is not recommended라는 에러가 뜨는것처럼 사용을 지양하고 있습니다.

필드에 @Autowired만 붙히면 자동으로 의존성이 주입되어 깔끔하다는 장점이 있지만 의존성에 접근이 불가하다는 단점이 있습니다.

<br>

**Contruct Injection (생성자 주입)**
```java
public class GroundService{
    private final Sport sport;
    @Autowired
	public GroundService(Sport sport){
		this.sport=sport;
	}
    public void play(){
    }
}
```

스프링에서 가장 권장하는 방법입니다. 생성자 주입의 장점은 다음과 같습니다.
1. 객체의 불변성
- 생성자를 final로 선언할수 있고, 이로인해 setter주입과 같이 런타임시점에 의존성 주입받는 객체가 변경될 일이 없어집니다(객체의 불변성).

2. POJO를 사용하여 테스트 코드 작성
- 테스트코드를 작성할때 @SpringBootTest, @DataJpaTest와 같이 스프링을 이용하여 테스트 코드를 짤수도 있지만 Java만으로 테스트를 작성할 수 있다는 장점이 있습니다.

<br>

### 주입한다는건 알았는데, 어떻게 주입한거지?
이제 DI를 통해 의존성 주입을 하는 방법에 대해 알았는데, 그럼 개발자가 직접 주입할 객체를 지정한것도 아닌데, 해당 객체를 판단하여 주입할수 있는것일까요? 

비밀은 스프링의 IoC에 숨겨져 있습니다.!

<br>

### 제어의 역전 `IoC`(Inversion of Control)
<img src="https://github.com/sejong-bucket/sejong-bucket-v0/assets/74135929/8306a9c1-e92d-44f2-9026-202a9e918b88" width="350">

- xml파일, 어노테이션(@Bean)으로 스프링 컨테이너에 객체를 등록하면 스프링 컨테이너에서 객체의 생명주기를 관리
- 즉, 객체의 제어권이 컨테이너(스프링)으로 바뀌기 때문에 제어의 역전(IoC)라 부름
>IoC가 없다면 개발자가 직접 객체를 제어해야함.
>  - 객체 생성, 의존성 설정 등등

<br>


### 스프링 빈을 스프링 컨테이너에서 꺼내오는 과정
BeanFactory
스프링은 BeanFactory클래스를 통해 빈을 관리하게 됩니다.

```java
public interface BeanFactory {
	...
	Object getBean(String name) throws BeansException;
	...
}
```

해당 클래스를 살펴보면 DI 해야하는 객체를 인스턴스화 할수 있도록 getBean() 메서드를 지원하는 것을 볼 수 있습니다. 해당 메서드 외에도 다양한 방식으로 빈을 찾아 반환하는 것을 볼 수 있습니다.

ApplicationContext
그렇다면 프로젝트의 어디에서 BeanFactory를 사용하게 될까요? 프로젝트를 생성하면 기본으로 존재하는 (프로젝트명)Application 클래스의 SpringApplication.*run*(프로젝트명Application.class, args); 안에 비밀이 존재합니다.


run메서드를 타고 들어가보면 다음과 같은 클래스가 존재합니다.

<img src="https://github.com/sejong-bucket/sejong-bucket-v0/assets/74135929/cf0e6a4d-5b20-4bab-a2c5-3bb743b5c762" width=350>

즉 run메서드를 실행하면 ConfigurableApplicationContext 가 반환되는 것을 볼수 있고, 해당 인터페이스의 관계를 다이어그램으로 표현해보면 다음과 같습니다.

<br>


<img src="https://github.com/sejong-bucket/sejong-bucket-v0/assets/74135929/157e8a9b-1b97-4a2c-9e38-c21fd8c1f7fe" width=350>

많은 인터페이스들을 상속하고 있는것을 볼수 있습니다. 여기서 BeanFactory 의 경우 최상단에 위치하여 있고, 해당 인터페이스외 다양한 부가기능들을 상속받는 ApplicationContext 가 보입니다.

해당 인터페이스는 BeanFactory를 상속받아 BeanFactory외의 부가 기능을 가진 인터페이스  구동되는 시점에 등록된 Bean 객체들을 스캔하여 객체화하여, 실제 개발에서 주로 사용하게 됩니다.

<br/>




