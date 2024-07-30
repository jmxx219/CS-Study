# RxJava(Reactive Programming)

### RXJava란

절차대로 실행되는 프로그램을 명령형(Imperative) 프로그래밍이라 한다.

RX는 Reactive의 줄임말로 데이터의 흐름을 정의하고 데이터가 변경되었을때 연관된 작업이 실행되는 반응형 프로그래밍을 도와주는 라이브러리이다.

<br>
<br>

**RXJava의 기본원리**

기존에는 필요한 데이터를 사용자가 가져와(pull)사용했다면 Reactive방식의 경우 데이터의 변화가 발생한 곳에서 데이터를 전달(push)해준다.

**예시**

명령형 프로그래밍에서 `a=b+c` 라는 코드가 있을때 a에 b+c가 할당된후 b와c가 변경되어도 a값은 변하지 않음
하지만 Reactive Programming에서는 프로그램이 b와 c가 변경될때마다 a의 값이 자동으로 업데이트됌

<br>
<br>

### Reactive Programming 장점

비동기(Asynchronous)

- I/O 작업이나 외부 서비스 호출이 완료되기를 기다리는 동안 다른 작업을 처리할 수 있는 스레드를 확보

non-blocking

- 반응형 시스템에서 컴포넌트는 non-blocking으로 설계되어 리소스를 차단하거나 리소스를 사용할 수 있게 될 때까지 기다리지 않습니다.
  대신 콜백을 등록하거나 이벤트를 구독하여 리소스를 사용할 준비가 되면 알림을 받습니다.

Scalable

- non-blocking 및 이벤트 중심 특성 덕분에 리소스를 효율적으로 관리할 수 있어 애플리케이션이 최소한의 리소스로 많은 수의 동시 연결 또는 요청을 처리할 수 있습니다.

<br>
<br>

### Reactive Programming에서의 Data Stream

### Observables and Observers

<img src="https://github.com/user-attachments/assets/cf8354ba-7dc9-4025-96f3-79a3009e9941" width="350">

1. Observable이 데이터 스트림을 처리하고, 완료되면 데이터를 발행
2. 데이터를 발행할 때마다 구독하고 있는 모든 Observer가 알림을 받음
3. Observer는 수신한 데이터를 가지고 작업처리

> Stream API 와의 차이점
>
>Sync blocking이며 Stream API는 pull model을 사용합니다.
> 다음 요소를 pull 해와서 Exception, 결과 반환 하는 Return 등의 데이터 처리를 반복합니다.
>
>Reactive Programming은 push 방식을 사용합니다
>
>Asynch non blocking이며 observable은 별도의 스레드에서 실행된 다음 onNext, onError 및 onComplete와 같은 이벤트를 사용하여 결과를 메인 스레드(클라이언트)로 push
> 합니다.

<br>
<br>

### Operators

Operators는 스트림의 데이터를 변환하거나 필터링하는 함수입니다.

개발자는 연산자를 사용하여 다양한 방식으로 observables을 조작하고 결합할 수 있습니다.

**RxJava Operators 분류**

| 카테고리 | 연산자               | 설명                                         |
|------|-------------------|--------------------------------------------|
| 생성   | `just()`          | 고정된 개수의 항목을 발행                             |
|      | `fromArray()`     | 배열의 항목을 발행                                 |
|      | `fromIterable()`  | Iterable의 항목을 발행                           |
|      | `create()`        | ObservableOnSubscribe를 사용하여 Observable을 생성 |
|      | `interval()`      | 일정 시간 간격으로 항목을 발행                          |
| 변환   | `map()`           | 각 항목을 다른 항목으로 변환                           |
|      | `flatMap()`       | 각 항목을 Observable로 변환하고 병합                  |
|      | `concatMap()`     | 각 항목을 Observable로 변환하고 순서대로 병합             |
|      | `switchMap()`     | 가장 최근의 Observable로 전환                      |
|      | `reduce()`        | 각 항목을 결합하여 단일 결과 생성                        |
|      | `scan()`          | 각 항목을 결합하여 중간 결과를 생성                       |
| 제어   | `filter()`        | 조건을 만족하는 항목만 통과                            |
|      | `take()`          | 처음 N개의 항목을 발행                              |
|      | `skip()`          | 처음 N개의 항목을 건너뜀                             |
| 결합   | `zip()`           | 여러 Observable의 항목을 결합하여 단일 Observable 생성   |
|      | `combineLatest()` | 가장 최근의 항목을 결합하여 발행                         |

### 사용예시

**Observable.create()**
1. Observable.create()를 사용하여 직접 Observable을 생성 
2. ObservableOnSubscribe를 통해 항목을 발행합니다.
```java
import io.reactivex.rxjava3.core.Observable;

public class CreateObservableExample {
    public static void main(String[] args) {
        Observable<Integer> observable = Observable.create(emitter -> {
            emitter.onNext(1);
            emitter.onNext(2);
            emitter.onNext(3);
            emitter.onComplete(); // 완료를 알림
        });

        observable.subscribe(
                item -> System.out.println("Received: " + item),
                error -> System.err.println("Error: " + error),
                () -> System.out.println("Completed")
        );
    }
}

```

<br>

**Observable.interval()**
- Observable.interval()을 사용하여 주기적으로 항목을 발행하는 Observable을 생성합니다. 이 예제는 일정 시간 간격으로 숫자를 발행합니다.
```java
import io.reactivex.rxjava3.core.Observable;
import java.util.concurrent.TimeUnit;

public class IntervalObservableExample {
    public static void main(String[] args) {
        Observable<Long> observable = Observable.interval(1, TimeUnit.SECONDS);

        observable.subscribe(
                item -> System.out.println("Received: " + item),
                error -> System.err.println("Error: " + error),
                () -> System.out.println("Completed")
        );

        // 잠시 대기 (예제를 위해)
        try {
            Thread.sleep(5000); // 5초 대기
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

```

<br>
<br>

### Subscription and Unsubscription

Subscriptions은 observers가 observables이 내보내는 이벤트를 수신할 수 있는 메커니즘입니다.

observers가 더 이상 이벤트를 수신할 필요가 없는 경우, observable의 구독을 취소하여 리소스를 효과적으로 정리할 수 있습니다.