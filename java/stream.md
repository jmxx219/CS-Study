# 스트림(Stream)과 람다(Lambda)

<br>

## 람다(Lambda)

- 람다식은 메서드를 하나의 식으로 표현한 것
- 람다식을 적용하면 메서드의 이름과 반환값이 없어지기 때문에 **익명 함수**라고도 부름
- 사용 예시
    ```java
    // 람다 구조
    (인자)->{로직}
    ```
    ```java
    // 기존 메서드 구조
    void action(){
        System.out.println("we are running");
    }
    
    // 람다 구조
    ()->{System.out.println("we are running");}
    ```

> 자바8 이전의 메서드는 클래스에 포함되어 있어야 하기때문에, 정의를 위한 클래스를 만들어야했고, 호출을 위해 불필요한 객체생성을 해야했다.

<br>

### 람다의 내부 구조

> 람다식의 실체는 메서드가 아닌, 익명클래스의 객체이다.

- 다음 람다식은
    ```java
    (int a,int b) -> a > b ? a : b;
    ```
- 실제로는 아래와 같이 생겼다.
    ```java
    new Object(){
        int max(int a,int b){
            return a>b ? a: b;
        }
    }
    ```

<br>
<br>

## 스트림(Stream)

- 스트림은 연속적인 데이터의 흐름을 의미하며, 자바 스트림 API또한 원하는 작업들을 연속적으로 처리해줌
    - 선언형 : 더 간결하고 가독성이 좋아진다.
    - 조립할 수 있음 : 유연성이 좋아진다.
    - 병렬화 : 성능이 좋아진다.
- 스트림의 특징
    - 스트림은 데이터소스를 읽기만 할뿐 변경하지 않는다.
    - 스트림은 일회용이다. 스트림을 한번 사용하면 닫혀서 다시 사용할 수 없다. 필요하다면 다시 스트림을 생성해야 한다.
    - 스트림은 작업을 내부 반복으로 처리한다.

<br>

### 스트림과 컬렉션

- 컬렉션은 `반복자(for, while)`를 이용해서 사용자가 직접 요소를 반복해야 한다. 이 반복을 `외부 반복`이라고 부른다.
  
    - 외부반복의 경우, 반복자가 존재함
      
        ```java
        List<String> names = new ArrayList<>();
        for(Dish dish: menu) {
            names.add(dish.getName());
        }
        ```
- 스트림은 반복을 알아서 처리해주고 결과 스트림 값을 어딘가에 저장해주는 방식을 사용한다. 이를 `내부 반복`이라고 한다.
  
    - 내부반복의 경우, 반복자 없음
      
        ```java
        List<String> names = menu.stream().map(Dish::getName).collect(toList());
        ```
    - 내부 반복의 장점
        1. 반복자 없음 : 개발자가 직접 반복문을 처리해주지 않아도 됨.
        2. 병렬 처리 : 외부 반복을 사용하면서 병렬처리를 위해서는 스레드간의 공유자원에 대한 동기화 처리를 따로 해줘야 한다. 하지만 내부 반복은 이를 관리할 필요가 없음

<br/>

### 스트림에서 사용하는 함수형 인터페이스

1. `Predicate<T>`
    
    참 거짓을 판단하는 인터페이스. 매개 변수를 받아서 결과값을 반환
    
    ```java
    List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
    
            List<Integer> evenNumbers = numbers.stream()
                    .filter(n -> n % 2 == 0)
                    .collect(Collectors.toList());
    ```
    

2. `Function<T,R>`
    
    일반적인 함수. 매개변수를 받아서 결과를 반환
    
    ```java
    List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
    
            List<Integer> squares = numbers.stream()
                    .map(n -> n * n) 
                    .collect(Collectors.toList());
    ```
    

3. `Cunsumer<T>` 
    
    매개변수만 있고 반환값은 없는 상태
    
    ```java
    List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
    
            numbers.forEach(number -> System.out.println(number));
    ```
    

4. `Supplier<T>`
    
    매개변수는 없고 반환값만 있는 상태
    
    ```java
    Supplier<Integer> randomNumberSupplier = () -> new Random().nextInt();
    
            Stream<Integer> randomStream = Stream.generate(randomNumberSupplier)
                    .limit(5);
    ```
    

<br>

> 반환값의 유뮤에 따라 분류할 수 있는 4개의 함수형 인터페이스 모두 다양한 형태로 스트림에서 활용되고 있다.

<br>

### 스트림 연산

> 스트림의 연산은 다음 두가지로 나뉘게 된다.

- `중간 연산` : 연산 결과가 스트림으로 반환. 스트림에 연속해서 중간 연산을 할 수 있음
    - distinct() : 중복 제거
    - filter() : 조건에 안 맞는 요소 제거
    - limit() : 스트림 일부만 가져옴
    - sorted() : 스트림 요소를 정렬
    - map() : 스트림 요소를 변환
- `최종 연산` : 스트림의 요소를 소모하는 연산으로, 마지막 한 번만 가능
    - forEach() : 각 요소에 지정된 작업 수행
    - count() : 스트림의 요소 개수 반환
    - max(), min() : 최대, 최소값 반환
    - findAny(), findFirst() : 스트림 요소 중 하나 반환
    - toArray() : 스트림의 모든 요소를 배열로 반환
    - collect() : 스트림의 요소를 수집한다. 주로 요소들을 그룹화하거나 컬렉션에 담아 반환
  
  
이렇게 연산을 계속하는 것을 `체이닝`이라 하고 해당 코드가 **무엇**을 할지가 보다 명확해진다.
```java
stream.distinct()
.limit(5)
.sorted()
.forEach(System.out::println)
```

<br/>

### 스트림의 중간 연산 특징

> 스트림의 중간 연산은 `layziess(게으름)` 이라는 중요한 특징을 가지고, 이 특성으로 얻을 수 있는 최적화 효과(`루프 퓨전(loop fusion)`, `쇼트 서킷`)가 존재함

<br/>

#### 지연연산, layziess(게으름)

- 스트림은 **게으르다.** 스트림은 스트림 문장 전체를 인식한 이후에 시작하기 때문에 최종연산이 수행되기 전에는 중간연산이 수행되지 않는다.

<br>

#### 루프 퓨전(loop fusion)

- 중간연산이 서로 다른 연산이지만 한 과정으로 병합되어 수행된다.

- 아래와 같은 클래스가 존재한다.

    ```java
    static class Data {
      private final int value;
      public Data(int value) { 
        this.value = value; 
      }
        
      @Override public String toString() { 
        return " -> " + value; 
      }
    }
    ```
  
   
- 다음과 같은 스트림 연산을 하는 경우를 생각해보자
  
    ```java
    Stream.of(new Data(1), new Data(20), new Data(300))
        .peek(System.out::println) //forEach와 유사한 중간연산
        .peek(System.out::println)
        .forEach(System.out::println);
    ```

    
- 대부분 다음과 같은 결과를 예상할 것이다.
  
    ```text
    -> 1
    -> 20
    -> 300
    -> 1
    -> 20
    -> 300
    -> 1
    -> 20
    -> 300
    ```
 
- 하지만 실제로는 다음과 같은 결과가 나온다.
  
    ```text
    -> 1
    -> 1
    -> 1
    -> 20
    -> 20
    -> 20
    -> 300
    -> 300
    -> 300
    ```

- 예시로 보인 스트림을 for문으로 바꿔보면 다음과 같이 동작한다.

    ```java
    for (Data data : datas) { 
      System.out.println(data); // 첫 번째 peek
      System.out.println(data); // 두 번째 peek
      System.out.println(data); // forEach
    }
    ```
<br>

- 루프를 엮는 루프 퓨전은 원소에 접근하는 횟수를 줄여주는데, 이는 eager한 연산을 사용하여 9번의 원소 접근이 필요한 작업을 3번만에 처리할 수 있게 한다. 
- 하지만, 항상 루프 퓨전이 발생하는 것은 아니다. 중간 연산 중 한정되지 않은 상태를 사용하는 경우에는 루프 퓨전이 발생하지 않을 수도 있다. 
- 예를 들어, sorted 연산은 이전 단계의 결과가 필요하기 때문에 이전 단계의 2개의 peek에서 루프 퓨전을 수행한 후에 sorted 연산을 하고, 나머지 peek 연산을 퓨전한다.
  
    ```java
    Stream.of(new Data(1), new Data(20), new Data(300))
        .peek(System.out::println)
        .peek(System.out::println)
        .sorted(Comparator.comparing(Data::getValue))
        .peek(System.out::println)
        .peek(System.out::println)
        .forEach(System.out::println);
    ```

<br>

#### 스트림 쇼트 서킷

- 스트림의 연산을 중간에 끊어주는 행위를 말한다.

- 다음 스트림 연산에서 무한한 원소를 갖는 무한 스트림을 이용하는데, 랜덤한 값중 100개만 뽑아서 정렬하는 것을 볼수 있다.

    ```java
    Stream.generate(() -> new RandomInt())
        .limit(100) 
        .sorted(Comparator.comparingInt(Data::getValue)) 
        .collect(Collectors.toList());
    ```

- 하지만 만약 limit의 위치가 sorted뒤로 가게 되면 무한한 원소를 sorted 연산하게 되어 연산이 끝나지 않게 된다.
  
    ```java
    Stream.generate(() -> new RandomInt())
        .sorted(Comparator.comparingInt(Data::getValue)) 
        .limit(100) 
        .collect(Collectors.toList());
    ```

<br>
<br/>

## 스트림의 병렬 처리

- 한 가지 작업을 여러 서브 작업으로 나누고, 서브 작업들을 분리된 스레드에서 병렬적으로 처리하고, 결과들을 최종 결합하는 방법이다.
  
- 자바는 [ForkJoinPool 프레임워크](https://github.com/jmxx219/CS-Study/blob/main/operating-system/Thread%20Pool%2C%20Fork-Join.md#fork-join-poolfork-join-framework)를 이용해서 병렬 처리를 한다.
    - main 스레드는 스트림을 처리하기 위한 기본 스레드이고 나머지 스레드는 ForkJoinPool 프레임워크를 통해서 생성된다.
    - 해당 프레임워크의 설정해줘서 생성할 스레드 개수를 제어할 수 있다.
    
- 스트림은 병렬처리와 순차처리를 합쳐서 사용할 수 있다.
    - parallel() : 순차 처리 스트림을 병렬처리로 변경
    - sequential() : 병렬처리 스트림을 순차 처리로 변경
    
    ex) `list.stream().limit(100).parallel().reduce(Integer::sum);`  
    위 코드는 limit() 까지는 순차 처리로 진행하고 이후엔 병렬처리로 진행한다.

<br/>    

### 하지만 병렬 처리는 되도록 쓰지말자

- 참조들이 가리키는 실제 객체가 메모리에서 서로 떨어져 있을 수 있는데, 이런 경우엔 주 메모리에서 캐시 메모리로 전송되어 오기를 기다리는 시간이 늘어난다.
    - 따라서 참조 지역성은 다량의 데이터를 처리하는 벌크 연산을 병렬화할 때 중요한 요소로 작용한다. 
- 또한 최종 연산의 동작 방식에 따라서도 병렬처리는 효과가 달라진다.
    - anyMatch(), allMatch() 처럼 조건에 맞으면 바로 반환되는 연산은 병렬화에 적합하지만 collect() 메서드와 같이 합치는 작업은 부담이 크다. 
- 즉, 병렬화를 잘못 사용하면 성능이 나빠지거나, 예상치 못한 동작이 발생할 수 있다.

<br/>
<br/>

## 스트림의 사용

### 아래와 같은 상황에선 스트림을 지양하자.

1. 범위 안의 지역변수를 읽고 수정해야 하는 경우. 
    - 람다에서는 변화하지 않는 상태를 다루는 것이 일반적이므로 부적절

2. return, break, continue등의 세밀한 반복 제어가 필요할 때

3. 검사 예외를 던질 때

4. 다음 단계에서 이전 단계의 상태 정보가 필요할 떄 (스트림은 데이터를 버린다)

<br/>
 
### 아래와 같은 상황에선 스트림이 적절하다.

1. 원소들의 시퀀스를 일관되게 변환하거나, 필터링 할 때

2. 원소들의 시퀀스를 한가지의 규칙(연산)을 사용해 결합할 때 

3. 원소들의 시퀀스를 특정 컬렉션에 모을떄

4. 원소들의 시퀀스에서 특정 조건을 만족하는 원소를 찾아낼 때
