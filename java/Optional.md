# Optional

Java 8에서 소개된 Optional 클래스는 NullPointerException 문제를 해결하고, null을 명시적으로 처리할 수 있는 새로운 방법을 제공

<br>

## Optional 도입 배경

- Java 개발자들이 자주 겪는 문제 중 하나는 NullPointerException
  - 이는 참조 변수가 null인 상태에서 메서드나 필드에 접근하려고 할 때 발생하는 예외
- Null 값을 다루는 기존 방식은 코드가 복잡하고 가독성이 떨어질 수 있음
  - Optional을 도입함으로써, 메서드의 반환값이 null일 수 있음을 명시적으로 표현
  - 이를 통해 null 처리에 대한 책임을 호출자에게 명확히 전달

<br> 

## Optional의 의미 

- Optional을 사용하면 메서드의 반환값이 항상 값을 가질 것으로 가정하는 대신, 값이 없을 가능성도 고려하게 됨
- Optional의 메서드를 사용하면 null 값을 직접 다루지 않고도 안전하게 작업을 수행할 수 있음 
  - 값에 대해서 접근할 때는 get() 메서드를 사용해야 하므로 get()을 사용하기 전 Idea에서 경고를 표시해줌
  -  orElse, orElseGet, orElseThrow 등의 메서드를 통해 null일 경우의 대체 동작을 명시적으로 정의할 수 있음
- Optional은 map, flatMap, filter와 같은 **함수형 메서드를 제공**

<br>

## Optional 내부 구조 

- Reference type을 감싸고 있는 Wrapper 클래스
- Primitive type은 사용할 수 없음
- Optional Class 내부의 value 필드에 Reference type을 저장

```java
@jdk.internal.ValueBased
public final class Optional<T> {
    /**
     * If non-null, the value; if null, indicates no value is present
     */
    private final T value;
}
```

- value를 가져오기 위해서는 **.get()** 메서드를 사용

```java
public T get() {
    if (value == null) {
        throw new NoSuchElementException("No value present");
    }
    return value;
}
```

<br>
<br>

## Optional 객체 생성

- Optional.empty() : 빈 Optional 객체 생성

```java 
Optional emptyOptional = Optional.empty();
```

<br>

- Optional.of() : Null이 아닌 Optional 객체 생성

```java
String name = "Hello";
Optional<String> opt = Optional.of(name);
```

<br>

- Optional.ofNullable() : Null이 될 수 있는 Optional 객체 생성

```java
String name = null;
Op tional<String> opt = Optional.ofNullable(name);
```

<br>

### Optional.of와 Optional.ofNullable의 차이

<br>

```java
public static <T> Optional<T> of(T value) {
    return new Optional<>(Objects.requireNonNull(value));
}
```

Optional.of 오브젝트는 위와 같이 생겼으며 requiredNonNull의 메서드 내부 구조를 보면 아래와 같이 Null을 체크하고 Null이면 NullPointerException을 발생 시킴

```java
@ForceInline
public static <T> T requireNonNull(T obj) {
    if (obj == null)
        throw new NullPointerException();
    return obj;
}
```

반면에 Optional.ofNullable은 Null을 체크하고 Null이면 빈 EMPTY 객체를 반환하거나 Optional로 객체를 감싸서 반환함.

```java
public static <T> Optional<T> ofNullable(T value) {
    return value == null ? (Optional<T>) EMPTY // EMPTY는 Optional 내부에 선언된 static final 객체
                          : new Optional<>(value);
}
```

#### Optional.of()를 사용하는 상황

- 값이 명확히 보장이 될 때 
  - 값이 null이 아님을 보장할 수 있는 경우에 Optional.of()를 사용해서 **null 확인을 생략**할 수 있음
- 외부 메서드가 null을 반환하지 않을 때 
  - 외부 라이브러리나 API의 메서드가 null을 반환하지 않는다는 것을 알고 있고 null이 만약에 반환된다면 API의 문제로 간주해야하는 상황에 사용

<br>
<br>

## Optional을 확인하는 메서드 

### isPresent() 

Optional 객체가 비어있지 않은 경우에만 true를 반환  

- 내부 구현

```java
public boolean isPresent() {
    return value != null;
}
```

- 사용 예시

```java
Optional<String> userName = Optional.ofNullable(null);
if(!userName.isPresent()) {
  throw new IllegalArgumentException("Username is null");
}
userName.get(); // NoSuchElementException 발생하지 않음
```

<br>

### isEmpty()

Optional 객체가 비어있는 경우에만 true를 반환

- 내부 구현

```java
public boolean isEmpty() {
    return value == null;
}
```

- 사용 예시 

```java
Date data = cacheRepository.findById(1L);
if(chache.isEmpty()) {
  data = jpaRepository.findById(1L);
}
```

<br>

### orElse(T other)

Optional 객체가 비어있는 경우에 대체할 값을 지정할 수 있음

- 내부 구현 

```java
public T orElse(T other) {
    return value != null ? value : other;
}
```

- 사용 예시 

```java
User convertToEntity(UserRequest request) {
  User user = new User();
  user.setName(request.getName().orElse("홍길동"));
  user.setAge(request.getAge().orElse(20));
  return user;
}
```

<br>

### orElseThrow(Supplier<? extends X> exceptionSupplier)

Optional 객체가 비어있는 경우에 예외를 발생시킬 수 있음

- 내부 구현 

```java
public T orElseThrow() {
    if (value == null) {
        throw new NoSuchElementException("No value present");
    }
    return value;
}
```

- 사용 예시 

```java
User user = userRepository.findById(1L)
  .orElseThrow(
    () -> new ApiException("User not found")
);
```

<br>

### filter(Predicate<? super T> predicate)

```java
String name = null;
name.startWith("abc");
```

위 코드는 NullPointException이 발생할 수 있는 코드이지만 filter 함수를 사용하면 NullPointException을 방지할 수 있음

```java
Optional<String> name = Optional.ofNullable(null);
name.filter(n -> n.startWith("abc"));
```

<br>

### map(Function<? super T, ? extends U> mapper)

- map 메소드는 Optional 객체가 비어있지 않다면 해당 객체의 값을 주어진 함수에 적용한 결과를 새로운 Optional 객체로 반환
- 이 메소드는 Optional의 값을 다른 Optional 타입으로 변환할 때 유용

```java
Optional<String> name = Optional.of("John");
Optional<Integer> nameLength = name.map(String::length);
```

<br>