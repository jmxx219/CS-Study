# AOP(Aspect-Oriented Programming)


## OOP와 AOP

### 객체지향 프로그래밍(OOP)

> [OOP 참고](https://github.com/jmxx219/CS-Study/blob/main/java/OOP.md)

<img src="https://blog.kakaocdn.net/dn/boVFtI/btr2D7WxMbX/PzqKQG3JGmcUBrKNBoYhN0/img.png" width="400"/>

- 모듈화의 핵심 단위는 비즈니스 로직
- 객체마다 핵심 기능을 수행하기 위한 로직과 부가 기능 코드를 작성하기 때문에 하나의 부가 기능을 여러 곳에서 동일하게 사용하게 됨
    - 이 경우, 기존 프로젝트에 기능을 추가하거나 수정하게 되면 적용된 모든 곳에서 추가와 수정이 필요하다는 문제점이 발생함

<br>

### 관점 지향 프로그래밍(AOP)

<img src="https://blog.kakaocdn.net/dn/ohpau/btr2tDbEl2t/77zkpEAHPL4O8wIJbJ3Zik/img.png" width="400" />

- 객체지향 프로그래밍 패러다임을 보완하는 기술로, 메소드나 객체의 기능을 `핵심 관심사(Core Concern)`와 `공통 관심사(Cross-cutting Concern)`로 나누어 프로그래밍하는 것을 말함
- 어떤 로직을 기준으로 핵심 관심사(핵심적인 관점)와 공통 관심사(부가적인 관점)으로 나누고, 각각을 하나의 관점으로 보고 모듈화하겠다는 의미
    - `핵심 관심사(core concern)`: 비즈니스 로직을 구현하는 과정에서 비즈니스 로직이 처리하려는 목적 기능
    - `공통 관심사(cross-cutting concern)`: 각각의 모듈들의 주 목적 외에 필요한 부가적인 기능들(로깅, 동기화, 오류 검사 등)로, 공통적으로 사용되는 기능

<br>

#### AOP의 장점

- 공통 관심 사항을 핵심 관심 사항으로부터 분리시켜 핵심 로직을 깔끔하게 유지할 수 있음
    - 코드의 가독성과 유지보수성 등을 높일 수 있음
- 각각의 모듈에 수정이 필요하면 다른 모듈의 수정 없이 해당 로직만 변경하면 됨
- 공통 로직을 적용할 대상을 선택할 수 있음


<br>

### Spring의 AOP

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FS0SBh%2Fbtsgu7Wj6pW%2Fz0vQxOA9C7j1iU7gf7QWJk%2Fimg.png" width="400" />

- 스프링 프레임워크에서 제공하는 기능 중 하나로, 관점지향 프로그래밍을 지원하는 기술
- 로깅, 보안, 트랜잭션 관리 등과 같은 공통 관심 사항을 모듈화하여 중복을 줄이고 유지보수성을 향상하는데 도움을 줌


<br>
<br>


## AOP 용어 정리

<img src="https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F0c9aec3e-f216-43aa-b040-9196b9c4e950%2FUntitled.png&blockId=0feac740-7690-4d29-bcdb-48abc9b40fbe" width="600" />

- `Joinpoint`
    - 추상적인 개념으로 Advice가 적용될 수 있는 위치로, AOP를 적용할 수 있는 모든 지점을 말함
        - ex) 객체 생성 시점, 메서드 실행 시점, 생성자 호출 시점, 필드 값 접근 시점 등등
    - 스프링 AOP는 프록시 방식을 사용하므로 조인 포인트는 항상 메서드 실행 지점으로 제한됨
- `Pointcut`
    - Joinpoint 중에서 실제 Advice가 적용될 위치를 선별하는 기능
    - 스프링 AOP는 프록시 기반이기 때문에 Joinpoint가 메서드 실행 시점 밖에 없고 Pointcut도 메서드 실행 시점만 가능
- `Advice`
    - 실질적인 부가 기능 로직을 정의하는 곳
        - Aspect를 언제 핵심 코드에 적용할지를 정의함
    - 공통 관심사와 횡단 관점에 해당함
    - Joinpoint에 삽입되어 실행되어야 하는 코드, 메서드
- `Aspect`
    - Advice + Pointcut을 모듈화 한 것
        - 실제로 동작 코드를 의미하는 Advice와 작성한 Advice가 실제로 적용되는 메소드인 Pointcut을 합친 개념
    - 부가기능(로깅, 보안, 트랜잭션 등)을 나타내는 공통 관심사에 대한 추상적인 명칭
- `Advisor`
    - 스프링 AOP에서만 사용되는 용어로 하나의 Advice와 하나의 Pointcut 한 쌍으로 구성
- `Target`
    - Advice를 삽입할 대상이 되는 객체
        - 비즈니스 로직을 수행하는 클래스가 될 수도 있고, 프록시 객체가 될 수도 있음
    - Pointcut으로 결정
- `Weaving`
    - AOP에서 Joinpoint들을 Advice로 감싸는 과정
    - 작성한 Advice(공통 코드)를 핵심 로직 코드에 삽입하는 것으로 적용 시점에 따라 3가지로 나뉨
        - 컴파일 시 위빙, 클래스 로딩 시 위빙, 런타임 시 위빙
- `AOP 프록시`
    - AOP 기능을 구현하기 위해 만든 프록시 객체
    - 스프링에서 AOP 프록시는 `JDK 동적 프록시`와 `CGLIB 프록시`가 있고, CGLIB 프록시가 기본값


<br>



### Advice

> 스프링에서는 Advice에 관련된 5가지 애노테이션을 제공하고, 포인트컷에 지정된 대상 메서드에서 Advice가 실행되는 시점을 정할 수 있음

- Before Advice(`@Before`)
    - 대상 객체의 메소드 호출 전에 공통 기능을 실행함
- Around Advice(`@Around`)
    - 대상 객체의 메소드 실행 전, 후 또는 익셉션 발생 시점에 공통 기능을 실행하는데 사용됨
    - 다양한 시점에 원하는 기능을 삽입할 수 있기 때문에 Advice 중 가장 널리 사용됨
- After Advice(`@After`)
    - 익셉션 발생 여부에 상관없이 대상 객체의 메소드 실행 후 공통 기능을 실행함
    - `try/catch/finally`의 finally 블록과 비슷함
- After Returning Advice(`@AfterRunning`)
    - 대상 객체의 메소드가 익셉션 없이 정상적으로 실행된 이후에 공통 기능을 실행함
- After Throwing Advice(`@AfterThrowning`)
    - 대상 객체의 메소드를 실행하는 도중 익셉션이 발생한 경우에 공통 기능을 실행함



<br>
<br>


## AOP 적용 방식

> Aspect와와 실제 코드를 연결하는 과정(weaving)

### 1. 컴파일 시점

- `AspectJ 컴파일러`가 일반 `.java` 파일을 컴파일 할 때 `.class` 파일을 만드는 시점에 부가 기능을 넣어서 컴파일하는 방식
    - AspectJ 컴파일러가 Aspect를 확인해서 해당 클래스가 적용 대상인지 먼저 확인하고, 적용 대상인 경우에 부가 기능 로직을 적용함
    - 부가 기능 코드가 핵심 기능이 있는 컴파일된 코드 주변에 실제로 추가된다고 생각하면 됨
- 모든 지점에 적용 가능
- 하지만 AspectJ가 제공하는 특별한 컴파일러를 사용해야 하고, 복잡하다는 단점이 존재함

<br>


### 2. 클래스 로딩 시점

- JVM 내부의 클래스로더에 `.class` 파일을 올리는 시점에 **바이트 코드**를 조작해서 부가 기능 로직을 추가하는 방식
    - 자바의 경우 `.class` 파일을 JVM 내부의 클래스 로더에 보관하고, 이때 중간에서 `.class` 파일을 조작한 후에 JVM에 올릴 수 있음
- 모든 지점에 적용 가능
- 특별한 옵션을 통해 클래스 로더 조작기를 지정해야 하므로 운영하기 어려운 단점 존재

<br>


### 3. 런타임 시점(프록시 사용)

> 스프링이 사용하는 방식

- 컴파일이 끝나고 클래스 로더에 이미 다 올라가 자바가 실행된 다음에 동작하는 런타임 방식
    - 이미 `main` 메서드가 실행 중이기 때문에 코드를 조작하기 어려워 스프링, 컨테이너, DI, 빈 등 여러 개념과 기능을 총동원하여 **프록시를 통해 부가 기능을 적용**하는 방식
    - 프록시는 메서드 실행 시점에서만 다음 타겟을 호출할 수 있기 때문에, 런타임 시점에 부가 기능을 적용하는 방식은 **메서드의 실행 지점으로 제한**됨
- **스프링 빈에만 AOP를 적용할 수 있음**
- 특별한 컴파일러나, 복잡한 옵션, 클래스 로더 조작기를 사용하지 않아도 스프링만 있으면 AOP를 적용할 수 있기 때문에 스프링 AOP는 런타임 방식을 사용함


<br>
<br>


## Spring AOP와 Proxy

> 스프링은 원본 객체에 대한 코드 수정 없이, 런타임 시점에서 프록시 객체를 통해 부가적인 처리를 추가하기 위해 프록시 객체를 이용하여 AOP를 구현함

<br>

### 기존 프록시의 문제점

> [Proxy Pattern 참고](https://github.com/jmxx219/CS-Study/blob/main/java/%EB%94%94%EC%9E%90%EC%9D%B8%20%ED%8C%A8%ED%84%B4.md#-%ED%94%84%EB%A1%9D%EC%8B%9C-%ED%8C%A8%ED%84%B4-proxy-pattern)

- 기존 프록시의 경우 부가적인 기능을 추가할 때마다 프록시를 새롭게 만들어야 한다는 단점이 존재했음
    - 실제 객체가 인터페이스를 구현한다면 프록시 또한 사용하지도 않는 모든 메서드를 `@Override` 해야 함
    - 프록시가 중첩되면 코드가 복잡해지고, 같은 코드의 중복이 발생함
- 이를 해결하기 위해 나온 것이 [리플렉션](https://github.com/jmxx219/CS-Study/blob/main/java/reflection.md)을 사용해서 런타임에 만드는 동적 프록시인 `Dynamic proxy`임
    - 기존 객체를 상속받아 하나하나 프록시 객체를 직접 만드는 것이 아니라, 런타임 시점에 클래스 정보를 이용하여 어노테이션, 메서드에 따라 동적으로 다른 메서드 동작을 실행하도록 프록시를 만들 수 있음


<br>

### Dynamic Proxy

<img src="https://blog.kakaocdn.net/dn/be12EQ/btrci9ln6jF/cA6oKN5Qpezvy40ZCGvo2K/img.png" width="500" />

- 런타임에 특정 인터페이스들을 구현하는 클래스 또는 인스턴스를 만드는 기술
    - [리플렉션](https://github.com/jmxx219/CS-Study/blob/main/java/reflection.md)을 사용해 직접 구현할 수 있음
    - 또한, 자바에서 제공하는 리플렉션 API를 통해 편하게 다이나믹 프록시를 생성할 수 있음
- 다이나믹 프록시를 사용한다는 것은 런타임에 `인터페이스 프록시 인스턴스` 또는 `클래스의 프록시 인스턴스` 또는 `리플렉션을 이용한 클래스 자체`를 만들어 사용하는 프로그래밍 기법을 사용한다는 의미함
    - 이렇게 원하는 타겟 객체에 프록시를 적용시켜, 동적으로 프록시된 객체를 삽입하는 기술을 Runtime Weaving이라고 함

<br>

#### JDK Dynamic Proxy

- 자바에서 제공하는 API(java.lang.reflect.Proxy)를 사용하며 인터페이스 기반으로 Proxy를 생성함
    - 실제 객체와 같은 인터페이스를 사용하는 Proxy 객체를 생성함
    - 프록시 객체에서 해당 객체의 리플렉션 Class<T>를 조작하고 조작된 Class<T>를 이용해서 런타임에 프록시 객체 인스턴스를 만듦
    - 개발자는 리플렉션 Class<T>를 쉽게 조작하기 위해서 Java API에 있는 `InvocationHandler` 객체의 `invoke()` 메서드만 오버라이딩하여 실제 객체의 정보를 받아오고 조작하면 됨
- 이렇게 프록시 객체를 만들면, 특정 메서드나 어노테이션 값에 따라 동적으로 동작이 달라지는 프록시를 만들 수 있고, 이것이 다이나믹 프록시임
- 하지만 자바 API에서 제공하는 다이나믹 프록시의 경우, 인터페이스만 생성할 수 있음
    - 클래스 프록시가 필요하다면 직접 리플렉션으로 구현하거나 리플렉션 라이브러리들을 사용하면 됨

<br>

#### CGLIB Proxy

- CGlib는 바이트코드를 조작하여 프록시 객체를 생성해주는 코드 생성(Code gen) 라이브러리로, 런타임에 동적으로 자바 클래스의 프록시 생성 기능을 제공함
    - `MethodIntercepto`r 객체를 사용하여 특정 메서드만 프록시화 하고, 나머지는 프록시를 거치지 않고 실제 객체 메서드를 호출하도록 만들 수 있음
- CGlib은 바이트 코드로 조작하여 Proxy를 생성해주기 때문에 성능에 대한 부분이 JDK Dynamic Proxy보다 좋음
- 스프링, 하이버네이트에서 사용함


<br>

### Spring Aop와 Proxy

> [Spring AOP의 원리 - CGlib vs Dynamic Proxy](https://huisam.tistory.com/entry/springAOP) 참고

- JDK Dynamic Proxy에서 InvocationHandler, CGlib에서 MethodInterceptor는 Spring AOP에서 JoinPoint라는 개념과 일치함
- 특정 조건에 의해 필터링 하는 MethodMatcher는 Spring AOP에서 PointCut라는 개념과 일치함
- Proxy 로직이 실행되는 JDK Dynamic Proxy에 invoke 메서드, CGlib에서 Intercept 메서드는 Spring AOP에서 Advice라는 개념과 일치함
- 결국 실제 현업에서는 Aspect와 Advice, PointCut 정도만 설정해서 사용하면 됨

<br>
<br>

## AOP 프레임워크(Spring AOP vs AspectJ)


### Spring AOP

- 개발자가 마주한 공통적인 문제를 해결하고자 Spring IoC를 통한 간단한 AOP 구현이 목적
    - 즉, 완전한 AOP를 의도한 것이 아니며 스프링 컨테이너에 의해 관리되는 Bean에만 적용 가능함
- Proxy를 기반으로 한 Runtime Weaving 방식
- JDK Dynamic Proxy와 CGlib Proxy를 통해 프록시화함
- 런타임 시점에 동적으로 변할 수 있는 프록시 객체를 이용하기에 앱 성능에 영향을 끼칠 수 있음

<br>


### AspectJ

-  자바에서 완전한 AOP 솔루션 제공을 목표로하는 기술로, Spring AOP보다 강력하지만 복잡함
- 런타임 시점에는 영향끼치지 않음
    - 즉, 컴파일이 완료된 이후에는 앱 성능에 영향이 없음
- 내부 구조가 굉장히 복잡함


<br>
<br>

### Ref

- [[Java] Spring Boot AOP(Aspect-Oriented Programming) 이해하고 설정하기](https://adjh54.tistory.com/133)
- [AOP(Aspect Oriented Programming)](https://catsbi.oopy.io/fb62f86a-44d2-48e7-bb9d-8b937577c86c)
- [[Spring] AOP는 어떻게 적용되는걸까?](https://hyuuny.tistory.com/97)
- [바이트코드 조작(리플렉션, 다이나믹 프록시, 애노테이션 프로세서)](https://jiwondev.tistory.com/151#head11)
- [JDK Dynamic Proxy와 CGLIB의 차이점은 무엇일까?](https://gmoon92.github.io/spring/aop/2019/04/20/jdk-dynamic-proxy-and-cglib.html)
