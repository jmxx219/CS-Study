## 자바에서 Thread
- 자바에는 프로세스가 존재하지 않고 스레드만 존재하며, 자바 스레드는 JVM에 의해 스케줄되는 실행 단위 코드 블록이다.
- 일반 스레드와 거의 차이가 없으며, JVM가 운영체제의 역할을 한다.
- 즉, 개발자는 자바 스레드로 작동할 스레드 코드를 작성하고, 스레드 코드가 생명을 가지고 실행을 시작하도록 JVM에 요청하는 일 뿐이다.

### Thread(스레드)란?
동작하고 있는 프로그램을 프로세스(process)라고 한다. 보통 한 개의 프로세스는 한 가지의 일을 하지만, 스레드(thread)를 이용하면 한 프로세스 내에서 두 가지 또는 그 이상의 일을 동시에 할 수 있다.

## 자바에서의 Thread 활용
1. Thread상속 받기
2. Runnable implements

- 보통 두번째 방법을 선호함.
- Java의 extends의 상속 방식은 하나의 Class만 가능하므로, Thread를 사용하기 위해 한번의 상속을 사용한다면 상속 기능이 제한된다.
- 단, Runnable을 구현하여 스레드를 생성하는 경우, 객체 참조변수를 인자값으로 하는 Thread를 생성하여 사용해야 된다.
- 반면, java.lang.Thread 클래스를 상속받아 사용하는 경우 실행 스레드로 자신의 콜 스택을 가진 독립적인 프로세스가 된다.



## Thread 구현

1. Thread 클래스 상속

```java
public class MyThread extends Thread {
    @Override
    public void run() {
        // 수행 코드
    }
}
```
2. Runnable 인터페이스 구현
```java
public class MyThread implements Runnable {
    @Override
    public void run() {
        // 수행 코드
    }
}
```

## Thread 생성
1. Thread 클래스 상속
```java
public class ThreadTest implements Runnable {
    public ThreadTest() {}
    
    public ThreadTest(String name){
        Thread t = new Thread(this, name);
        t.start();
    }
    
    @Override
    public void run() {
        for(int i = 0; i <= 50; i++) {
            System.out.print(i + ":" + Thread.currentThread().getName() + " ");
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```
- 상속받은 클래스 자체를 스레드로 사용 가능
- 스레드의 클래스 메소드(getName())을 바로 사용 가능


2. Runnable 인터페이스 생성
```java
public static void main(String[] args) {
    Runnable r = new MyThread();
    Thread t = new Thread(r, "mythread");
}
```
- 해당 클래스를 인스턴스화해서 Thread 생성자에게 argument로 넘겨줘야함
- run()을 호출시, Runnable 인터페이스에 구현한 run()이 호출되므로 따로 오버라이딩을 안해도 된다.


## Thread 실행

> run() 호출이 아닌, `start()` 호출을 해야함!

- run()메소드의 사용은 main()의 콜 스택 하나를 이용하는 것으로 스레드 활용이 아니다.
    - 스레드를 사용한다는 것은, JVM이 다수의 콜 스택을 번갈아가면서 일처리를 하는 것이기 때문이다.
- start() 메소드를 호출하는 것은, 스레드가 작업을 실행하는데 필요한 콜 스택을 생성 후, run()을 호출하여 그 안에 run()을 저장할 수 있도록 한다.
    - JVM이 스레드를 위한 콜 스택을 만들어 준 후 context switching을 통해 동작하도록 해준다. 새로운 콜 스택을 만들어 작업해야 스레드 일처리가 되는 것이므로 start()메소드를 사용해야한다.


## 단일 스레드 VS 멀티 스레드
1. 단일 스레드(single Thread)
- 하나의 프로세스 내에서 하나의 스레드만 실행되는 것(순차 실행). 프로그램이 하나의 작업만 처리할 수 있다는 의미이며, 다른 작업이 실행되기 전에 현재 작업이 완료되어야 한다.

- 멀티 태스킹을 지원하지 않고 하나의 태스크만 처리할 수 있으므로 처리량이 낮아진다.

2. 멀티 스레드(Multi Thread)
- 하나의 프로세스 내에서 여러 스레드를 동시에 실행시키는 방법(병행 실행). 스레드가 동시에 여러 작업을 처리할 수 있기 때문에 시스템의 성능을 향상할 수 있다.

- 프로그램의 작업을 분할하여 처리하기 때문에 다양한 작업을 동시에 처리하여 빠르고 효율적으로 실행할 수 있지만, 스레드 간의 동기화 같은 부작용으로 이를 고려하여 프로그래밍해야 한다.

