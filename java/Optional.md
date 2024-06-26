# Optional

- Java 8에서 소개 
- Null을 처리하기 위해서 새로운 방법을 제공하는 클래스 
- Reference type은 모두 Null을 가질 수 있지만 이를 좀 더 명시적으로 다루기 위해 Optional을 사용

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

## Optional 메서드의 사용 방법

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






