# 리플렉션(Reflection)

<br/>

- 개념
  - 구체적인 클래스 타입을 알지 못하더라도 그 클래스의 메서드, 타입, 변수들에 접근할 수 있도록 해주는 자바 API
  - 컴파일 시간이 아닌 실행 시간에 동적으로 특정 클래스의 정보를 추출할 수 있는 프로그래밍 기법
- JVM과 리플렉션
  - JVM은 클래스 정보를 클래스 로더를 통해 읽어와서 해당 정보를 JVM 메모리에 저장함
  - 리플렉션은 이 JVM의 메모리 영역에 저장된 클래스의 정보(멤버 변수, 메서드, 생성자)를 런타임 시에 꺼내와서 사용하는 기술로, 클래스의 구조와 동작을 동적으로 탐색하고 제어할 수 있게함
    - 결국 클래스의 정보를 리플렉션이라는 기능을 통해 다시 가져올 수 있음
  - 하지만 성능상의 오버헤드와 보안상의 이슈가 있으므로 신중하게 사용해야 함

<br/>

### 사용하는 이유

- 런타임에 동적으로 클래스 정보에 접근하여 클래스를 사용할 때 필요함
  - 작성 시점에는 어떠한 클래스를 사용해야 할 지 모르지만, 런타임 시점에서 클래스를 가져와서 실행해야 하는 경우
- private 접근 제어자로 선언한 필드나 메서드까지 조작이 가능함
  - 하지만 이 때문에 캡슐화가 깨진다는 문제점 존재
- 프레임워크나 IDE에서 이러한 동적 바인딩을 이용한 기능을 제공함
  - IDE의 자동완성 기능
    - 인텔리제이와 같은 IDE에서 etter, Setter를 자동으로 생성해주는 기능도 리플렉션을 사용하여 필드 정보를 가져와 구현함
  - 보통 프레임워크나 라이브러리에서 많이 사용함
    - 이 둘은 컴파일 시점까지 개발자가 구현한 객체들의 타입을 모르기 때문에 이러한 문제를 동적으로 해결하기 위해 사용함
    - 대표적으로 Spring 프레임워크의 애노테이션 같은 기능들이 리플렉션을 이용하여 프로그램 실행 도중에 동적으로 클래스의 정보를 가져와서 사용함

<br/>

### 장단점

- 장점
  - 유연성
    - 구체적은 클래스를 알지 못해도 런타임 시점에 클래스를 만들어서 의존관계를 맺어줄 수 있음
    - 개발 규모가 큰 스프링의 경우, 리플렉션을 이용한 [Dynamic proxy](https://dahye-jeong.gitbook.io/spring/spring/2020-04-10-aop-dynamicproxy)를 통해서 DI 어노테이션(`@AutoWired`, `@Service`, `@Controller` 등)을 활용함
  - 접근 제한 상관없이 테스트 가능
    - 접근 제어자와 관계 없이 필드와 메소드에 접근하여 필요한 작업을 수행할 수 있기 때문에 private 메서드도 테스트가 가능함
- 단점
  - 캡슐화 저해
    - private이나 protected로 선언된 멤버 변수, 메서드에도 접근할 수 있음
    - 따라서 일반적인 접근 제어 규칙을 우회할 수 있는 가능성이 있으며, 클래스의 내부 구현 및 민감한 정보가 노출될 수 있음
  - 디버깅의 어려움
    - 런타임 시점에서 인스턴스를 생성하므로 컴파일 시점에서 해당 타입을 체크할 수 없고, 구체적인 동작 흐름을 파악하기 어려움
  - 성능 이슈
    - 단순히 필드 및 메소드를 접근할 때는 컴파일 시점에 분석된 클래스를 사용하지만, 리플렉션은 런타임에 클래스를 분석하기 때문에 성능저하가 발생함
  
<br/>

> 보통 일반적인 웹 애플리케이션 개발자는 리플렉션을 사용할 일이 거의 없다. 보통 라이브러리나 프레임워크를 개발할 때 사용된다. 따라서 리플렉션은 꼭 필요한 경우에만 한정적으로 사용해야 한다. 

<br/>
<br/>

## Class 클래스

- 리플렉션의 가장 핵심은 `Class` 클래스로, 리플렉션을 사용하라면 `Class<T>` 타입을 가져와야 함
- `Class<T>` 타입을 통해 `Constructor`와 `Method` 그리고 `Field` 정보를 가져올 수 있음

<br/>

### Class 객체 획득 방법

```Java
Class<Member> aClass = Member.class; // (1) {클래스 타입}.class

Member member1 = new Member();
Class<? extends Member> bClass = member1.getClass(); // (2) {인스턴스}.getClass()

Class<?> cClass = Class.forName("org.example.Member"); // (3) Class.forName("{전체 도메인 네임}")
```

- `{클래스 타입}.class`: 클래스의 `class` 프로퍼티를 통해 획득하는 방법
- `{인스턴스}.getClass()`: 인스턴스 변수의 메서드인 `getClass()` 사용
- `Class.forName("{전체 도메인 네임}")`: `Class` 클래스의 `forName()` 정적 메소드에 **FQCN**를 전달하여 해당 경로와 대응하는 클래스에 대한 `Class` 클래스의 인스턴스를 얻는 방법
  - **FQCN(Fully Qualified Class Name)**: 해당 클래스가 속한 패키지명을 모두 포함한 이름


<br/>

#### getXXX() vs getDelcaredXXX()

- Class 객체의 메서드 중에서 `getFields()`, `getMethods()`와 같은 형태와 `getDeclaredFields()`, `getDeclaredMethods()`와 같은 형태로 정의된 메서들이 존쟂함
- `getXXX()`: 상속받은 클래스와 인터페이스를 포함하여 모든 public인 요소들을 가져옴
- `getDelcaredXXX()`: 상속받은 클래스와 인터페이스를 제외하고 해당 클레스에 직접 정의된 메서드들을 모두 가져옴
  - 접근 제어자와 상관없이 요소에 접근할 수 있음

<br/>

### Constructor 획득 방법

- `Constructor`는 `java.lang.reflect` 패키지에서 제공하는 클래스이며, 클래스 생성자에 대한 정보와 접근을 제공함
  ```Java
  Constructor<?> constructor = aClass.getDeclaredConstructor(); // 생성자 가져오기
  Object object = constructor.newInstance(); // 인스턴스 생성
  Member member = (Member) constructor.newInstance(); // 타입 캐스팅
  
  // 파라미터가 존재하는 생성자의 경우
  Constructor<?> allArgsConstructor = getDeclaredConstructor(String.class, int.class);
  noArgsConstructor.setAccessible(true);
  Member member = (Member) noArgsConstructor.newInstance();
  ```

<br/>

### Method 획득 방법

- 리플렉션을 사용하여 Method 타입의 오브젝트를 획득하여 객체 메소드에 직접 접근할 수 있음
  ```Java
  Class<Member> aClass = Member.class;
  Member member = new Member("길동", 25);
  
  Method sayMyName = aClass.getDeclaredMethod("sayMyName");
  sayMyName.invoke(member);
  // 내 이름은 길동
  ```
  - `Method` 타입의 `invoke()`를 사용하여 메소드를 직접 호출할 수 있음


<br/>

### Field 획득 방법

- 리플렉션을 사용하여 Field 타입의 오브젝트를 획득하여 객체 필드에 직접 접근할 수 있음
  ```Java
  Class<Member> aClass = Member.class;
  Member member = new Member("길동", 25);
  
  for (Field field : aClass.getDeclaredFields()) {
      field.setAccessible(true);
      String fieldInfo = field.getType() + ", " + field.getName() + " = " + field.get(member);
      System.out.println(fieldInfo);
  }
  /*
      class java.lang.String, name = 길동
      int, age = 25
  */
  ```

<br/>
<br/>

### + 리플렉션은 무조건 기본 생성자가 필요할까?

- 모든 클래스에 기본 생성자가 필요한 이유는 Java Reflection API로 가져올 수 없는 정보 중 하나가 바로 생성자의 인자 정보이기 때문이다.
  - 따라서 기본 생성자 없이 파라미터가 있는 생성자만 존재한다면 Reflection이 객체를 생성할 수 없게 되는 것이다.
- 하지만 지금은 생성자의 파라미터 정보를 가져올 수 있다. Java7까지는 파라미터 정보를 가져올 수 없었지만, java8에서 리플렉션의 Parameter가 추가되었다.
- 그럼에도 불구하고 기본생성자를 요구하는 이유는 기본 생성자로 객체를 생성하고 필드를 통해 값을 넣어주는 것이 가장 간단한 방법이기 때문이다.
  - 다음 글을 참고해보자. [기본 생성자가 필요한 '진짜' 이유 (리플렉션 오해 바로 잡기!!!)](https://colour-my-memories-blue.tistory.com/16)


<br/>
<br/>


### Ref
- [자바 리플렉션 (Reflection) 기초](https://hudi.blog/java-reflection/)
- [리플렉션 (Reflection) - 리플렉션의 개념 및 사용법](https://tjdtls690.github.io/studycontents/java/2023-01-27-reflection01/)

<br/>