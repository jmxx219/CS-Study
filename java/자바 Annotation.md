# Java Annotation

## 어노테이션이란

- 어노테이션은 주석이다. 주석처럼 프로그래밍 언어에 영향을 미치지 않고 정보를 제공할 수 있다. 즉, 작동하는(구현된) 코드가 아니라 표시를 해놓은 것이다.
- 어노테이션을 사용하여 프로그램에 필요한 설정 정보를 소스 코드안에 심을 수 있다.

```java
@Test // 이 메서드가 테스트 대상임을 테스트 프로그램에게 알린다.
void method(){

        }
```

<br>

## 등장 배경

- 예전에는 소스 코드에 대한 문서를 따로 관리했고, 소스 코드 변경이 발생할 때마다 관련 문서를 수정해줬어야 했다. 이러한 불편한 점 때문에 소스 코드에 대한 문서를 따로 만들기보다 소스 코드와 문서를 하나의
  파일로 관리할 수 있도록 소스 코드에 주석을 함께 작성하여 문서를 관리했다.


- 위 기능을 응용하여 **소스 코드 안에 프로그램을 위한 정보를 미리 약속된 형식으로 포함시킨 것이 어노테이션이다.** 예전에는 프로그램을 위한 정보를 소스 코드와 분리된 설정 파일(ex: `XML`)을 따로
  관리했었는데, 어노테이션을 사용함으로써 관리를 용이하게 했다.

<br>

## 엘리먼트 규칙

- 어노테이션 내에 선언된 메서드를 엘리먼트라고 한다.


- 각 엘리먼트는 타입과 이름으로 구성되며, 디폴트 값을 가질 수 있다.
    - 타입으로는 `기본형`, `String`, `enum`, `annotation`, `Class` 그리고 이들의 배열을 허용한다.
    - 엘리먼트 이름 뒤에는 `()`를 붙여야 한다. `()`안에 매개변수는 선언할 수 없다.
    - 엘리먼트를 타입 매개변수로 정의할 수 없다. (ex: `ArrayList<T> list();`)
    - 엘리먼트가 하나 뿐이고, 이름이 `value`인 경우, 어노테이션을 적용할 때 엘리먼트 이름을 생략하고 값만 적어도 된다.


- 어노테이션에 값을 전달할 때 런타임 중에 알아내어야 하는 값은 들어갈 수 없다. 컴파일러 수준에서 해석되는 정적인 값만 들어갈 수 있다.


- 엘리먼트에는 예외를 선언할 수 없다. (ex: `int count() throws RuntimeException;`)

<br/>

```java
@interface AnnotationElement {
    int count() default 1;

    String testBy();

    String[] testTools() default {"aaa", "bbb", "ccc"};

    EnumType enumType();
}
```

<br>

## 표준 어노테이션

자바에서 기본적으로 제공해주는 어노테이션이다.

| 어노테이션 이름               | 설명                                 | 장점                                           |
|------------------------|------------------------------------|----------------------------------------------|
| `@Override`            | 컴파일러에게 오버라이딩하는 메서드라는 것을 알린다.       | 조상 메서드의 이름을 잘못 써서 오버라이딩을 하지 못하는 것을 방지할 수 있음. |
| `@Deprecated`          | 앞으로 사용하지 않을 것을 권장하는 대상에 붙인다.       | 더이상 사용되지 않는 것을 삭제하지 않고, 사용하지 말라고 권장할 수 있음    |
| `@SuppressWarnings`    | 컴파일러의 특정 경고메시지가 나타나지 않게 해준다.       | 컴파일러에 의해 생성되는 경고 메시지를 줄일 수 있음                |
| `@SafeVarargs`         | 메서드의 가변인자는 타입 안정성이 있다고 컴파일러에게 알린다. | 타입 안정성 경고 메시지를 줄일 수 있음                       |
| `@FunctionalInterface` | 함수형 인터페이스라는 것을 알린다.                | 함수형 인터페이스를 올바르게 선언했는지 확인함                    |
| `@Native`              | native 메서드에 의해 참조되는 상수 앞에 붙인다.     |                                              |

<br>

## 메타 어노테이션

어노테이션을 정의하기 위한 어노테이션의 어노테이션이다.

| Annotation    | 설명                                                       |
|---------------|----------------------------------------------------------|
| `@Target`     | 어노테이션 적용 가능 대상을 지정 (CLASS, METHOD, FIELD, CONSTRUCTOR 등) |
| `@Retention`  | 어노테이션이 유지되는 범위를 지정 (SOURCE, CLASS, RUNTIME)              |
| `@Documented` | 어노테이션 정보가 `javadoc`으로 작성된 문서에 포함된다.                      |
| `@Inherited`  | 상속받은 클래스에서도 어노테이션이 유지된다.                                 |
| `@Repetable`  | 하나의 대상에 같은 어노테이션을 여러번 붙일 수 있다                            |

<br/>

`예시: @Target`

```java

@Documented
@Retention(RetentionPolicy.RUNTIME) // 런타임까지 어노테이션 유지됨
@Target(ElementType.ANNOTATION_TYPE) // 적용 대상은 어노테이션
public @interface Target {
    ElementType[] value();
}

```

<br/>

## Spring Annotation

<br/>

| Annotation           | 설명                                                                                   |
|----------------------|--------------------------------------------------------------------------------------|
| `@Component`         | 스프링 컨테이너가 클래스를 스프링 빈 자동 등록할 때 사용                                                     |
| `@Bean`              | 클래스 생성을 직접 제어하여 수동으로 스프링 빈으로 등록할 때 사용                                                |
| `@Autowired`         | 자동으로 의존성을 클래스에 주입받을때 사용됨                                                             |
| `@Configuration`     | 설정 클래스임을 명시                                                                          |
| `@Qualifier("name")` | 동일한 타입의 빈이 여러개일 경우 이름으로 식별할 때 사용                                                     |
| `@Controller`        | Presentation Layer 클래스임을 명시                                                          |
| `@Service`           | Service Layer 클래스임을 명시                                                               |
| `@Repository`        | Persistence Layer 클래스임을 명시                                                           |
| `@ComponentScan`     | 스프링 컨텍스트에 등록될 클래스(`@Component`, `@Bean`, `@Configuration` 등이 붙은 )를 지정된 범위에서 스캔할 때 사용 |

<br/>

상기 어노테이션 말고도 스프링에서 사용되는 어노테이션은 엄청나게 많은데, 스프링에서 많은 어노테이션을 사용하는 이유가 뭘까?

어노테이션이 개발된 이유와 동일하다. 스프링 설정을 위한 정보를 소스 코드에 포함시키기 위해서 어노테이션을 사용한다. 또한 어노테이션을 통해 클래스나 메서드의 역할을 바로 알 수 있기 때문에 가독성을 향상시킨다.

<br/>

## Lombok

반복적인 코드 작성을 줄이기 위해서 사용하는 라이브러리이다.

| Annotation | 설명 |
|---|---|
| `@NoArgsConstructor` | 파라미터 없는 기본 생성자를 자동으로 생성합니다. |
| `@AllArgsConstructor` | 모든 필드를 파라미터로 받는 생성자를 자동으로 생성합니다. |
| `@RequiredArgsConstructor` | `final` 또는 `@NonNull`로 선언된 필드만을 파라미터로 받는 생성자를 자동으로 생성합니다. |
| `@Getter` | 클래스의 모든 필드에 대한 getter 메서드를 자동으로 생성합니다. 필드별로 어노테이션을 적용할 수도 있습니다. |
| `@Setter` | 클래스의 모든 필드에 대한 setter 메서드를 자동으로 생성합니다. 필드별로 어노테이션을 적용할 수도 있습니다. |
| `@ToString` | 클래스의 `toString()` 메서드를 오버라이드하여 필드 정보를 문자열로 반환하는 메서드를 자동으로 생성합니다. |
| `@EqualsAndHashCode` | `equals()`와 `hashCode()` 메서드를 자동으로 생성하여, 객체의 동등성 비교와 해시 코드 생성 로직을 제공합니다. |
| `@Builder` | 빌더 패턴을 구현하는 코드를 자동으로 생성하여, 객체의 점진적인 생성과정을 단순화시킵니다. |
| `@Data`  | `@Getter`, `@Setter`, `@ToString`, `@EqualsAndHashCode`와 필요에 따라 `@RequiredArgsConstructor`를 포함하여 적용합니다. |

### 주의점

`@AllArgsConstructor`와 `@RequiredArgsConstructor`를 사용할 경우, 같은 타입의 필드의 순서가 바뀌어도 알아차기 어렵다.

```java
@AllArgsConstructor
public class Product {
    private int discountPrice; // 할인 가
    private int price;         // 정가
}
```

서비스 레이어에서 이렇게 사용하고 있다고 가정해보자

```java
@Service
public class ProductService {
    public void test() {
        new Product(5_000, 10_000);
    }
}
```

어떠한 이유로 `Product` 필드 순서를 바꾼다고 가정해보자

```java
@AllArgsConstructor
public class Product {
    private int price;
    private int discountPrice;
}
```

이렇게 변경되었을 때, 해당 클래스를 생성하는 다른 클래스는 변경을 알아차리기 어렵다. 동일한 문제가 `@RequiredArgsConstructor`에서도 발생한다

`@Data`, `@Value`, `@Builder` 어노테이션은 모두 내부적으로 `@AllArgsConstructor`, `@RequiredArgsConstructor` 어노테이션을 사용한다.