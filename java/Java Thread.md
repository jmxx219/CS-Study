# Java Thread

<br>

## 프로세스와 스레드

> #### [프로세스와 스레드 참고](https://github.com/jmxx219/CS-Study/blob/main/operating-system/process.md)

- 프로세스(Process): cpu에 의해 메모리에 올라가 실행중인 프로그램
  - 자바 [JVM](https://github.com/jmxx219/CS-Study/blob/main/java/JVM.md)은 주로 하나의 프로세스로 실행되며, 동시에 여러 작업을 수행하기 위헤 멀티 스레드를 지원함
- 스레드(Thread): 프로세스 안에서 실질적으로 작업을 실행하는 단위
  - Java에서 스레드는 JVM에 의해 관리됨

<br>

### 단일 스레드 VS 멀티 스레드

- 단일 스레드(single Thread)
   - 하나의 프로세스 내에서 하나의 스레드만 실행되는 것(순차 실행)
   - 프로그램이 하나의 작업만 처리할 수 있다는 의미이며, 다른 작업이 실행되기 전에 현재 작업이 완료되어야 함
   - 멀티 태스킹을 지원하지 않고 하나의 태스크만 처리할 수 있으므로 처리량이 낮아짐
- 멀티 스레드(Multi Thread)
   - 하나의 프로세스 내에서 여러 스레드를 동시에 실행시키는 방법(병행 실행)
   - 스레드가 동시에 여러 작업을 처리할 수 있기 때문에 시스템의 성능을 향상할 수 있음
   - 프로그램의 작업을 분할하여 처리하기 때문에 다양한 작업을 동시에 처리하여 빠르고 효율적으로 실행할 수 있지만, 스레드 간의 동기화 등을 고려하여 프로그래밍해야 함

<br>

#### + 스레드 유형(KLT vs ULT)

> 스레드 유형은 크게 `커널 수준 스레드(Kernel-Level Threads, KLT)`와 `사용자 수준 스레드(User-Level Threads, ULT)`로 분류됨
   
- 커널 수준 스레드(KLT)
  - 운영체제 시스템 내에서 생성되어 동작하는 스레드로, 스레드의 생성과 스케줄링 및 관리를 OS 커널이 직접 담당함
  - 자원 관리 및 멀티프로세싱 환경에서의 스케줄링 측면 장점이 있으나, 스레드 생성 및 컨텍스트 스위칭에 높은 오버헤드가 있을 수 있음
- 사용자 수준 스레드(ULT)
  - 사용자 영역의 라이브러리나 애플리케이션에 의해 생성 및 관리되는 스레드로, 운영 체제의 커널로부터 독립적으로 스케줄링되며 스레드 관리에 필요한 모든 작업을 사용자 영역에서 처리하는 스레드
  - 스레드 생성 및 컨텍스트 스위칭이 빠르다는 장점이 있지만, 일부 리소스를 공유하는 작업에서 커널의 도움이 필요할 수 있음

<br>
<br>

## Java에서 스레드

<img width="400" src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FspfXi%2Fbtr1pXWTbqg%2FfqENAyoE95kMGrwzPKHIi1%2Fimg.png">

- 자바에는 프로세스가 존재하지 않고, 스레드 개념만 존재하여 JVM은 멀티스레딩만 지원함
  - 한 컴퓨터에서 N개의 자바 응용 프로그램이 실행되고 있다면, 그것은 N개의 JVM이 실행되고 있는 것
  - 따라서 각 자바 응용프로그램은 별개의 메모리 영역에서 독립적으로 실행됨
- 모든 자바 애플리케이션은 JVM에서 제공되는 하나의 메인 스레드를 갖고 있고, 메인 스레드가 `main()` 메소드를 실행하면서 애플리케이션이 시작됨
  - 메인 스레드는 필요에 따라 작업 스레드를 만들어 병렬로 코드를 실행할 수 있음
- JVM은 여러 스레드를 한 번에 실행할 수 있고, 이 때 우선순위에 따라 먼저 실행되는 스레드가 결정됨

<br>

### 자바 스레드와 JVM

> [JVM 참고](https://github.com/jmxx219/CS-Study/blob/main/java/JVM.md)  
> 자바에서 스레드를 생성하면, 이는 JVM 위에서 실행되는 스레드 인스턴스로 나타나고, 자바 스레드는 JVM 내부적으로 운영체제의 스레드와 매핑되어 관리됨

<img width="400" src="https://d2.naver.com/content/images/2024/03/0a710dbd-8c39-16f6-818e-1bd4349a6708.png">

- 자바 스레드는 JVM에서 `User Thread(ULT)`를 생성할 때 `Java Native Interface(JNI)`로 커널 영역을 호출하여 생성된 `커널 스레드(KLT)`와 매핑하여 작업을 수행하는 형태
  - 유저 수준 스레드(ULT)이지만, 내부적으로 JVM은 커널 수준 스레드(KLT)를 사용함
  - 커널 스레드 풀에 있는 커널 스레드에서 이용할 수 있도록 위임하여 유저 스레드와 커널 스레드가 `1:1` 매핑을 통해 동작함
- JVM에서 스레드를 생성할 때마다 커널에서 자바 스레드와 대응하는 커널 스레드를 생성함
  - 자바에서는 플랫폼 스레드로 정의되어 있고, OS 플랫폼에 따라 JVM이 사용자 스레드를 OS의 네이티브 스레드에 매핑하여 관리함
  - 따라서 스레드 스케쥴링 역시 OS의 스케쥴링 정책을 그대로 따라감

<br>

#### Native Thread

- 초기 자바에서는 JVM이 사용자 수준 스레드를 생성하고 관리 및 스케줄링을 했었음.
  - 이렇게 자바에서 생성된 스레드를 `Green Thread`라고 부르며, CPU를 하나 밖에 사용하지 못해서 멀티 코어 환경에서 단점으로 작용함
- 이후 [JNI](https://github.com/jmxx219/CS-Study/blob/main/java/JVM.md#-jni---%EB%84%A4%EC%9D%B4%ED%8B%B0%EB%B8%8C-%EB%A9%94%EC%86%8C%EB%93%9C-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4-native-medthod-interface)를 이용하여 커널 수준 스레드를 생성 및 매핑하여 사용할 수 있는 `Native Thread`가 자바의 기본 스레드 모델이 되었음
  - JNI를 이용하여 C++ 언어로 작성된 코드를 실행하여 스레드를 생성하는 방식
- 이 때문에 자바의 `java.lang.Thread` 클래스를 통해 생성하는 java thread는 사용자 수준 스레드지만, 내부적으로 JVM은 커널 수준 스레드를 사용함
  - 자바의 `java.lang.Thread` 클래스를 통해 스레드를 생성하고 `start()` 메소드를 실행하게 되면 `start0()` 네이티브 메소드가 실행됨

<br>
<br>

### 자바에서 Thread 생성 방법

> Java에서 작업 Thread를 생성하는 방법은 크게 두 가지가 존재함  
>   
> 1. `Thread` 클래스를 상속 받아 생성하는 방법(`run()` 메서드 오버라이딩)  
> 2. `Runnable` 인터페이스를 구현하여 생성하는 방법(`run()` 메서드 구현)  

- 보통은 `Runnable` 인터페이스를 구현하는 방법으로 많이 생성하여 사용함
  - Java의 `extends`의 상속 방식은 하나의 Class만 가능하므로, Thread를 사용하기 위해 한 번의 상속을 사용한다면 다른 클래스를 더 이상 상속할 수 없기 때문 상속 기능이 제한된다.
- 단, `Runnable`을 구현하여 스레드를 생성하는 경우, 객체 참조변수를 인자값으로 하는 Thread를 생성하여 사용해야 함
- 반면, `java.lang.Thread` 클래스를 상속받아 사용하는 경우, 실행 스레드로 자신의 콜 스택을 가진 독립적인 프로세스가 됨


<br>

#### 1. java.lang.Thread 

```java
import java.lang.Thread;

public class MyThread extends Thread {
    @Override
    public void run() {
        // 수행 코드
    }
}

public class Test {
    public static void main(String[] args) {
        MyThread thread =  new MyThread();
        thread.start();
    }
}
```
- 상속받은 클래스 자체를 스레드로 사용 가능하며, run() 메서드를 오버라이드하여 작업 스레드가 실행할 코드를 작성함
- MyThread 클래스의 인스턴스를 만들어 `start()` 메서드로 시작시키면 Thread 클래스의 run() 메서드가 호출되어 작업 스레드가 실행됨


<br>

#### 2. Runnable Interface
```java
import java.lang.Thread;

public class MyRunnable implements Runnable {
    @Override
    public void run() {
        // 수행 코드
    }
}

public class Test {
    public static void main(String[] args) {
        MyRunnable myRunnable =  new MyRunnable();
        Thread thread = new Thread(myRunnable);

        thread.start();
    }
}
```

- MyRunnable 클래스의 인스턴스를 Thread 클래스의 생성자에 전달하여 Thread 객체를 생성
- Thread 객체를 `start()` 메서드로 시작시키면 Runnable 인터페이스의 `run()` 메서드가 호출되어 작업 스레드가 실행됨


<br>

### Thread 실행

> run() 호출이 아닌, `start()` 메서드를 호출을 해야함!

```Java
Thread thread = new Thread(() -> System.out.println("Thread running"));

thread.run();   // 잘못된 사용: 새로운 스레드 생성되지 않음
thread.start(); // 올바른 사용: 새로운 스레드에서 run() 메서드 실행됨
```
- `run()` 메소드의 사용은 `main()`의 콜 스택 하나를 이용하는 것으로 스레드 활용이 아님
  - 스레드를 사용한다는 것은, JVM이 다수의 콜 스택을 번갈아가면서 일처리를 하는 것이기 때문
- `start()` 메소드를 호출하는 것은, 스레드가 작업을 실행하는데 필요한 콜 스택을 생성 후, `run()`을 호출하여 그 안에 `run()`을 저장할 수 있도록 함
  - JVM이 스레드를 위한 콜 스택을 만들어 준 후 context switching을 통해 동작하도록 해줌
  - 새로운 콜 스택을 만들어 작업해야 스레드 일처리가 되는 것이므로 `start()` 메소드를 사용해야 함



<br>
<br>

## Java 스레드 동기화

> #### [Java Synchronized 참고](https://github.com/jmxx219/CS-Study/blob/main/java/Synchronized.md)


<br>
<br>

## Java에서 스레드 풀 사용하기

> #### [Thread Pool 참고](https://github.com/jmxx219/CS-Study/blob/main/operating-system/Thread%20Pool%2C%20Fork-Join.md)  

<img width="500" src="https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F42def3b0-ffe2-4c40-a851-adbe8512ac6e%2FUntitled.png?table=block&id=28de5c65-d996-49ba-9460-49630cefdd1a&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2">

- 스레드 풀(Thread Pool)은 미리 정의된 수의 스레드를 생성하고, 작업 큐에 들어오는 작업들을 효율적으로 처리할 수 있도록 관리함  
- Java에서는 스레드 풀을 생성하고 사용할 수 있도록 `java.util.concurrent` 패키지에서 `ExecutorService` 인터페이스와 `Executors` 클래스를 제공함
  - `Executors` 클래스의 다양한 정적 메소드를 통해서 `ExecutorService` 구현체를 만들어서 사용할 수 있고, 이것이 바로 스레드 풀임

<br>

### 1. Executor 인터페이스

```Java
public interface Executor {
    void execute(Runnable command);
}
```
```Java
Executor executor = Executors.newSingleThreadExecutor();
executor.execute(() -> System.out.println("Hello World"));
```
- `execute()` 메서드 딱 한 개 가지고 있음
- 실행 가능한(Runnable) 인스턴스를 받아 실행함


<br>

### 2. ExecutorService 인터페이스

```Java
ExecutorService executorService = Executors.newFixedThreadPool(10);
Future<String> future = executorService.submit(() -> "Hello World");
// some operations
String result = future.get();
```

- `ExecutorService`는 `Executor`를 확장하여 작업의 진행 상황을 제어하고 서비스의 종료를 관리하기 위한 많은 메소드가 포함되어 있음
  - `Executors` 클래스의 메소드 중에 하나를 이용해서 간편하게 생성할 수 있음
- `submit()` 메서드는 작업을 제출하고 `Future` 객체를 반환하여 결과를 얻을 수 있음


<br>

#### Executors 클래스

- `Executors`는 `Executor`, `ExecutorService`, `ScheduledExecutorService` 등을 위한 다양한 형태의 쓰레드 풀을 제공하는 정적 팩토리 메서드를 지원해주는 클래스
  ```Java
  Executors.newSingleThreadExecutor(); // 쓰레드가 1개인 ExecutorService를 리턴, 싱글 스레드에서 동작하는 작업 처리 시 사용함
  Executors.newFixedThreadPool();     // 인자 개수만큼 고정된 스레드풀을 생성
  Executors.newCachedThreadPool();    // 필요할 때 필요한 만큼 쓰레드풀을 생성
  Executors.newScheculedThreadPool(); // 일정 시간 뒤에 실행되는 작업이나, 주기적으로 수행되는 작업이 있다면 스케줄스레드 풀을 활용
  ```
- Executors 클래스 내 메서드들에서 공통적으로 사용하는 매개변수 중 이해할 필요가 있는 중요한 설정 매개변수들
  - `corePoolSize`: 쓰레드 풀의 핵심 크기, 쓰레드 풀이 유지해야 하는 최소한의 쓰레드 개수
  - `maximumPoolSize`: 쓰레드 풀의 최대 크기, 쓰레드 풀이 생성할 수 있는 최대 쓰레드 개수
  - `keepAliveTime`: 작업하지 않고 놀고 있는 쓰레드(=비활성 쓰레드)가 유지될 최대 시간


<br/>

#### ThreadPoolExecutor

```Java
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue) {
  this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue,
       Executors.defaultThreadFactory(), defaultHandler);
}
```

- `Executors` 클래스에 들어있는 newCachedThreadPool, newFixedThreadPool 등과 같은 팩토리 메소드에서 생성해주는 `Executor`에 대한 기본적인 내용이 구현되어 있는 클래스
- 팩토리 메소드를 사용해 만들어진 스레드 풀의 기본 실행 정책이 요구사항에 잘 맞지 않는다면 ThreadPoolExecutor의 생성자를 호출해 스레드 풀을 생성할 수 있
  - 생성자의 파라미터 값을 통해 스레드 풀 설정을 조절할 수 있음

<br>

### 스레드 풀 예시

```Java
// Executors 정적메서드를 통해 최대 스레드 개수가 2인 스레드 풀 생성 
ExecutorService executorService = Executors.newFixedThreadPool(2);

for(int i = 0; i < 10; i++){
    Runnable runnable = new Runnable() {
        
        @Override
        public void run() {
            //스레드에게 시킬 작업 내용
            ThreadPoolExecutor threadPoolExecutor = (ThreadPoolExecutor) executorService;
            
            int poolSize = threadPoolExecutor.getPoolSize(); //스레드 풀 사이즈 얻기
            String threadName = Thread.currentThread().getName(); //스레드 풀에 있는 해당 스레드 이름 얻기
            
            System.out.println("[총 스레드 개수:" + poolSize + "] 작업 스레드 이름: " + threadName);
        }
    };

    //스레드풀에게 작업 처리 요청
    executorService.execute(runnable);
    //executorService.submit(runnable);
    
    //콘솔 출력 시간 텀을 위해 sleep() 사용
    try {
        Thread.sleep(10);
    } catch (InterruptedException e) {
        // TODO Auto-generated catch block
        e.printStackTrace();
    } 
}

//스레드풀 종료
executorService.shutdown();
```

<br>
<br>

## Ref

- [[JAVA] 자바 스레드(Thread) 총정리](https://rebornbb.tistory.com/entry/JAVA-%EB%A9%80%ED%8B%B0%EC%8A%A4%EB%A0%88%EB%93%9C-%EA%B0%9C%EB%85%90)
- [[Java] Thread Pool(스레드 풀)](https://limkydev.tistory.com/55)
- [[JVM] 자바 스레드와 리눅스 스레드, LWP, POSIX Thread](https://jbground.tistory.com/3)
- 
