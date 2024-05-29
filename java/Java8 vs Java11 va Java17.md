# Java 8

### 람다 표현식

- **함수형 프로그래밍** 스타일을 자바에 도입
- 예시
    
    ```java
    // 기존 익명 클래스 방식
    new Thread(new Runnable() {
        @Override
        public void run() {
            System.out.println("Hello, world!");
        }
    }).start();
    
    // 람다 표현식 방식
    new Thread(() -> System.out.println("Hello, world!")).start();
    ```
    

### 스트림 API

- 컬렉션 데이터를 함수형 스타일로 처리하는 도구
- 주로 Filtering, Mapping, Sorting 등의 작업을 쉽게 수행할 수 있게 해줌
- 예시
    
    ```java
    List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "Dave");
    List<String> filteredNames = names.stream()
                                      .filter(name -> name.startsWith("A"))
                                      .collect(Collectors.toList());
    System.out.println(filteredNames); // [Alice]
    ```
    

### 신규 날짜와 시간 API

- java.time package
- java.util.Date와 java.util.Calendar의 복잡함과 불편함을 해결
- 예시
    
    ```java
    LocalDate today = LocalDate.now();
    LocalDate birthday = LocalDate.of(1990, Month.JANUARY, 1);
    Period age = Period.between(birthday, today);
    System.out.println("Age: " + age.getYears());
    ```
    

### 디폴트 메서드

- 인터페이스에서 메서드 구현을 제공
- 예시
    
    ```java
    interface MyInterface {
        void existingMethod();
        
        default void newMethod() {
            System.out.println("New default method");
        }
    }
    
    class MyClass implements MyInterface {
        @Override
        public void existingMethod() {
            System.out.println("Existing method implementation");
        }
    }
    
    MyClass myClass = new MyClass();
    myClass.existingMethod(); 
    myClass.newMethod();
    ```
    

# Java 11

### HttpClient 표준화

- Java 9에서 소개되었던 `HttpClient` API가 Java 11에서 표준으로 채택 (기존에는 `RestTemplate`)
- HTTP/1.1, HTTP/2, WebSocket 지원

### `var` 키워드 사용 확장

- 자바 10에서 소개되었던 `var` 키워드를 사용하여 지연 변수 타입을 추론할 수 있음.

### 여러 메서드 추가

- `String` 클래스
    - **`isBlank()`**: 문자열이 공백으로만 이루어져 있는지 확인.
    - **`lines()`**: 문자열을 줄 단위로 분할하여 스트림으로 반환.
    - **`strip()`**, **`stripLeading()`**, **`stripTrailing()`**: 문자열의 앞뒤 공백을 제거.
    - **`repeat(int times)`**: 문자열을 주어진 횟수만큼 반복.
    - 예시
        
        ```java
        String str = " Hello World ";
        System.out.println(str.isBlank()); // false
        System.out.println(str.strip()); // "Hello World"
        System.out.println("Java".repeat(3)); // "JavaJavaJava"
        ```
        
- `Optional` 클래스
    - **`isEmpty()`**: 값이 비어있는지 확인.
    - 예시
        
        ```java
        Optional<String> optional = Optional.empty();
        System.out.println(optional.isEmpty()); // true
        ```
        

### 단일 파일 소스 코드 프로그램 실행

- Java 11부터는 단일 파일로 작성된 소스 코드를 컴파일 없이 바로 실행할 수 있음.
    
    ```bash
    $ java HelloWorld.java
    ```
    

### 가비지 컬렉터 개선

- ZGC
    - 매우 낮은 Latency를 목표로 설계됨 - GC 중 쓰레드가 멈추는 시간을 짧게 유지
    - 대규모 Heap 지원 - 최대 수백 GB까지 메모리를 효율적으로 관리할 수 있음
    - 사용법
        
        ```bash
        java -XX:+UseZGC -jar myapp.jar
        ```
        
- Epsilon GC
    - 실험적으로 등장

# Java 17

### Record Class

- 간결하고 명료한 방식으로 데이터 클래스를 정의할 수 있는 새로운 클래스 유형
- 멤버 변수는 `private final`
- 필드별 getter, equal, hashCode, toString 자동 생성
- 예시
    
    ```java
    // 기존
    public class Person {
        private final String name;
        private final int age;
    
        public Person(String name, int age) {
            this.name = name;
            this.age = age;
        }
    
        public String getName() {
            return name;
        }
    
        public int getAge() {
            return age;
        }
    
        @Override
        public boolean equals(Object o) {
            ...
        }
    
        @Override
        public int hashCode() {
            ...
        }
    
        @Override
        public String toString() {
            ...
        }
    }
    
    // record
    public record Person(String name, int age) { }
    
    ```
    

### Sealed Class

- 봉인된 클래스를 사용하여 계층 구조를 더 염격하게 제어
- 예시
    
    ```java
    public abstract sealed class Shape
        permits Circle, Square { }
    
    public final class Circle extends Shape { }
    public final class Square extends Shape { }
    ```
    

### Pattern Matching 스위치문

- `switch`문에 패턴을 적용하여 유연한 매칭이 가능해짐
- 예시
    
    ```java
    static String formatterPatternSwitch(Object obj) {
        return switch (obj) {
            case Integer i -> String.format("int %d", i);
            case Long l    -> String.format("long %d", l);
            case Double d  -> String.format("double %f", d);
            case String s  -> String.format("String %s", s);
            default        -> obj.toString();
        };
    }
    ```
    

### Switch Expression

- 기존의 Switch문의 중복을 제거한 방식

```java
// 기존
public static void printDayOfWeek(String dayOfWeek) {
    switch (dayOfWeek) {
        case "Monday":
        case "Tuesday":
        case "Wednesday":
        case "Thursday":
        case "Friday":
            System.out.println("평일입니다.");
            break;
        case "Saturday":
        case "Sunday":
            System.out.println("주말입니다.");
            break;
    }
    
// Java 17
public static void printDayOfWeek(String dayOfWeek) {
    switch (dayOfWeek) {
        case "Monday", "Tuesday", "Wednesday", "Thursday", "Friday" 
            -> System.out.println("평일입니다.");
        case "Saturday", "Sunday"
            -> System.out.println("주말입니다.");
    }
```
