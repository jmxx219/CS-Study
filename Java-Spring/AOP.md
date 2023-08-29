# AOP

- Spring의 핵심 개념 중 하나인 DI가 애플리케이션 모듈들 간의 결합도를 낮춘다면, 
- **AOP(Aspect-Oriented Programming)는 핵심 로직과 부가 기능을 분리하여 애플리케이션 전체에 걸쳐 사용되는 부가 기능을 모듈화하여 재사용할 수 있도록 지원하는 것** 이다.





![스프링 AOP](https://blog.kakaocdn.net/dn/ZrxxK/btrxoHWDBA4/hs93YWUYWe1vwIkUr2fru1/img.png)

기존의 OOP에서 바라보던 관점을 다르게 하여 부가기능적인 측면에서 봤을 때, 공통된 요소를 추출할 수 있다. 

이 때 가로 영역의 공통된 부분을 잘라냈다고 하여, AOP를 **크로스 컷팅(Cross-Cutting)** 이라고 부르기도 한다. 



## OOP와 AOP



### OOP

![img](https://blog.kakaocdn.net/dn/boVFtI/btr2D7WxMbX/PzqKQG3JGmcUBrKNBoYhN0/img.png)

- 비즈니스 로직의 모듈화
  - 모듈화의 핵심 단위는 비즈니스 로직

- 객체마다 핵심 기능을 수행하기 위한 로직과 부가 기능 코드를 작성
  - 이 때, 부가 기능을 위한 동일한 코드가 작성되는 경우가 발생



### AOP

![img](https://blog.kakaocdn.net/dn/ohpau/btr2tDbEl2t/77zkpEAHPL4O8wIJbJ3Zik/img.png)

- 어떠한 기능을 구현할 때 '핵심 기능' 과 '부가 기능'으로 구분하여 각각을 하나의 관점으로 보는 것
  - 핵심 기능: 비즈니스 로직을 구현하는 과정에서 비즈니스 로직이 처리하려는 목적 기능
  - 부가 기능: 각각의 모듈들의 주 목적 외에 필요한 부가적인 기능들 (로깅, 동기화, 오류 검사 등)
- 공통된 기능을 재사용하는 기법
- OOP를 더욱 잘 사용하기 위한 개념이다. 



### AOP의 장점

- 공통 관심 사항을 핵심 관심사항으로부터 분리시켜 핵심 로직을 깔끔하게 유지할 수 있다.
- 그에 따라 코드의 가독성, 유지보수성 등을 높일 수 있다.
- 각각의 모듈에 수정이 필요하면 다른 모듈의 수정 없이 해당 로직만 변경하면 된다.
- 공통 로직을 적용할 대상을 선택할 수 있다



## AOP 적용 방식

### 컴파일 시점

- **AspectJ 컴파일러** 가 일반 `.java` 파일을 컴파일할 때 부가기능을 넣어서 `.class` 파일로 컴파일해주는 것을 의미한다.
- 에스팩트와 실제 코드를 연결하는 **위빙(weaving)**이라고 부른다.
- 모든 지점에 적용이 가능하다.
- AspectJ가 제공하는 특별한 컴파일러를 사용해야한다는 단점, 복잡하다는 단점이 존재한다. 



### 클래스 로딩 시점

- JVM내 클래스로더에 `.class` 파일을 올리는 시점에 **바이트 코드를 조작**해 부가기능 로직을 추가하는 방식
- 모든 지점에 적용 가능
- 특별한 옵션과 클래스 로더 조작기를 지정해야 하므로 운영에 어려움이 존재한다. 



### 런타임 시점

- **스프링이 사용하는 방식**
- 컴파일이 끝나고 클래스 로더에 이미 다 올라가 자바가 실행된 다음에 동작하는 런타임 방식
- 이미 런타임 중이라 코드를 조작하기 어려워 스프링, 컨테이너, DI, 빈 등 여러 개념과 기능을 총동원하여 **프록시를 통해 부가 기능을 적용**하는 방식이다. 
- 프록시는 메서드 실행 시점에서만 다음 타겟을 호출할 수 있기 때문에, 런타임 시점에 부가기능을 적용하는 방식은 **메서드의 실행 지점으로 제한**된다.
- **스프링 빈에만 AOP를 적용 가능**
- 특별한 컴파일러나, 복잡한 옵션, 클래스 로더 조작기를 사용하지 않아도 스프링만 있으면 AOP를 적용할 수 있기 때문에 스프링 AOP는 런타임 방식을 사용



## AOP 용어 및 개념

![img](https://github.com/backtony/blog-code/blob/master/spring/img/aop/2/2-1.PNG?raw=true)

- **Join point**
  - **추상적인 개념** 으로 **Advice가 적용될 수 있는 모든 위치**를 말한다. 
  - ex) 메서드 실행 시점, 생성자 호출 시점, 필드 값 접근 시점 등등..
  - **스프링 AOP는 프록시 방식을 사용하므로 조인 포인트는 항상 메서드 실행 지점**
- **Pointcut**
  - 조인 포인트 중에서 Advice가 적용될 위치를 선별하는 기능
  - **스프링 AOP는 프록시 기반이기 때문에 조인 포인트가 메서드 실행 시점 뿐이 없고 포인트컷도 메서드 실행 시점만 가능**
- **Target**
  - **Advice의 대상이 되는 객체** (비즈니스 로직을 수행하는 클래스가 될수도 있고, 프록시 객체가 될 수도 있다.)
  - **Pointcut으로 결정**
- **Advice**
  - **Joinpoint에서 실행되어야 하는 코드**, **공통 관심**, 횡단 관점에 해당한다.
  - **실질적인 부가 기능 로직을 정의**하는 곳
  - Aspect를 언제 핵심 코드에 적용할지를 정의한다. 
- **Aspect**
  - **advice + pointcut을 모듈화 한 것**
  - @Aspect와 같은 의미
  - 트랜잭션 기능, 로그 기능, 보안 기능 등..
- **Advisor**
  - 스프링 AOP에서만 사용되는 용어로 advice + pointcut 한 쌍
- **Weaving**
  - AOP에서 **Joinpoint들을 Advice로 감싸는 과정을 Weaving**이라고 한다
- **AOP 프록시**
  - AOP 기능을 구현하기 위해 만든 프록시 객체
  - 스프링에서 AOP 프록시는 JDK 동적 프록시 또는 CGLIB 프록시
  - **스프링 AOP의 기본값은 CGLIB 프록시**



### Advice

Advice는 실질적으로 프록시에서 수행하게 되는 로직을 정의하게 되는 곳이다. 

스프링에서는 Advice에 관련된 5가지 애노테이션을 제공하고, **포인트컷에 지정된 대상 메서드에서 Advice가 실행되는 시점** 을 정할 수 있다. 



- Before Advice (@Before)
  대상 **객체의 메소드 호출 전에 공통 기능을 실행**한다.

- After Advice (@After)
  **익셉션 발생 여부에 상관없이 대상 객체의 메소드 실행 후 공통 기능을 실행**한다.
  *try - catch - finally의 finally 블록과 비슷하다.*

- After Returning Advice (@AfterRunning)
  대상 **객체의 메소드가 익셉션 없이 정상적으로 실행된 이후에 공통 기능을 실행**한다.

- After Throwing Advice (@AfterThrowning)
  대상 **객체의 메소드를 실행하는 도중 익셉션이 발생한 경우에 공통 기능을 실행**한다.

- Around Advice (@Around)
  대상 **객체의 메소드 실행 전, 후 또는 익셉션 발생 시점에 공통 기능을 실행하는데 사용**된다

  

이 중 **Around Advice가 널리 사용**되는데, 대상 객체의 메소드를 실행 하기 전/후, 익셉션 발생 시점 등 **다양한 시점에 원하는 기능을 삽입할 수 있기 때문**이다.



### Pointcut 표현식

Pointcut을 이용하면 Advice 메소드가 적용될 비즈니스 메소드를 정확하게 필터링 할 수 있다.

#### 지시자(PCD, AspectJ pointcut designators)의 종류

`execution`

- 가장 정교한 Pointcut을 만들수 있고, 리턴타입 패키지경로 클래스명 메소드명(매개변수)
- **본적으로 상위 타입을 명시하면 하위 타입에도 적용이 되지만, 하위 타입에만 메서드를 명시하는 경우 매칭이 불가능하다.**

`within` 

- 타입패턴 내에 해당하는 모든 것들을 Pointcut으로 설정
- **상위타입으로 하위타입매칭이 불가능**, 타입이 정확하게 맞아야 한다. 

`bean` 

- bean이름으로 Pointcut

`args`, `@target`, `@within`등의 지시자도 존재



## 스프링의 Proxy

![img](https://velog.velcdn.com/images%2Fgillog%2Fpost%2Fd6adf048-cbb1-4728-a209-95b33648571a%2F99C15C445D1DD0DE28.png)

`Proxy`는 타겟을 감싸서 요청을 대신 받아주는 랩핑 클래스이다.
**Spring에서는** **Proxy를 이용해** 객체지향의 5대원칙 중 하나인 **OCP를 적용**하고 있다.

*Open-Close Principal : 개방폐쇄의 원칙*
*'소프트웨어 개체(클래스, 모듈, 함수 등등)는 확장에 대해 열려 있어야 하고,
수정에 대해서는 닫혀 있어야 한다.'는 프로그래밍 원칙*



스프링에서 말하는 프록시는 리플렉션과 바이트코드 조작을 이용해 실제 타겟의 기능을 대신 수행하면서, [**기능을 확장하거나 추가할 수도 있는(OCP원칙) 다이나믹 프록시 객체**](https://jiwondev.tistory.com/151#head11)를 의미한다.



### JDK Dynamic Proxy

- 자바에서 제공하는 API (java.lang.reflect.Proxy)를 사용하며 인터페이스 기반으로 Proxy를 생성한다

- 개발자는 InvocationHandler 객체의 invoke()메서드를 오버라이딩하여 실제 객체의 정보를 받아오고 조작하면 된다.

![img](https://blog.kakaocdn.net/dn/dioK0M/btrcecvx9RI/1y6fyX0UycC8kidNHiCJk1/img.png)



#### 단점

- 특정 메소드만 사용하고 싶더라도 리플렉션을 통해 통째로 가져와서 조건문을 이용해 프록시 객체를 만들어야 한다. 
- 코드가 복잡해진다. 



### CGLIB Proxy

- CGlib는 바이트코드를 조작하여 프록시 객체를 생성해주는 코드 생성(Code gen)라이브러리이다.
- JDK 프록시는 인터페이스에 적혀있는 모든 메서드에 프록시를 적용시킨다. 그리고 Class<T>정보로 조건문을 만들어 특정 메서드만 추가동작을 하도록 만들 수 있다.
- CGlib는 MethodMatcher 객체를 사용하여 특정 메서드만 프록시화 하고, 나머지는 프록시를 거치지 않고 실제 객체 메서드를 호출하도록 만들 수 있다.

![img](https://blog.kakaocdn.net/dn/be12EQ/btrci9ln6jF/cA6oKN5Qpezvy40ZCGvo2K/img.png)



## AspectJ와 스프링AOP

- AOP는 개념

- AOP를 구현하는 방법에는 여러가지가 있고, 그만큼 다양한 AOP 라이브러리와 도구들이 존재한다
- 대부분 바이트코드 Weaving을 사용하며 대부분 AspectJ와 스프링 AOP를 활용하여 AOP를 구현한다. 



### 스프링 AOP

- 스프링에서 사용할 수 있는 간단한 AOP를 제공
- CGlib 등의 바이트 코드 조작을 이용한 다이나믹 프록시를 이용해 제공한다. 
- 런타임 시점에 동적으로 변할 수 있는 프록시 객체를 이용하기에 앱 성능에 영향을 끼칠 수 있다



### AspectJ

-  자바에서 완벽한 AOP 솔루션 제공을 목표로하는 기술
- 런타임 시점에는 영향끼치지 않는다. 즉 컴파일이 완료된 이후에는 앱 성능에 영향이 없다.
- 내부 구조가 굉장히 복잡하다



![img](https://blog.kakaocdn.net/dn/cXTsBS/btrb9Fkzqgj/zS591JP8PYMFz6MVdu40yk/img.png)




<details>

<summary> 참조 </summary>


스프링 고급 - https://velog.io/@backtony/Spring-AOP-%EC%B4%9D%EC%A0%95%EB%A6%AC#5-pointcut

https://velog.io/@gillog/AOP%EA%B4%80%EC%A0%90-%EC%A7%80%ED%96%A5-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D#weaving

https://jiwondev.tistory.com/152

프록시 팩토리와 빈후처리기 - https://velog.io/@backtony/Spring-%ED%94%84%EB%A1%9D%EC%8B%9C-%ED%8C%A9%ED%86%A0%EB%A6%AC%EC%99%80-%EB%B9%88-%ED%9B%84%EC%B2%98%EB%A6%AC%EA%B8%B0

</details>
