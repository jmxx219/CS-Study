# Synchronized
<br/>

## 멀티 스레드(Multi-Thread)

### 멀티 태스킹
- 두 가지 이상의 작업을 동시에 처리하는 것
    - 멀티 스레드를 이용하여 한 프로세스 내에서 멀티 태스킹을 할 수 있음
- 멀티 프로세스: 독립적으로 프로그램들을 실행하고 여러가지 작업 처리(`애플리케이션 단위의 멀티 태스킹`)
- 멀티 스레드: 한 개의 프로그램을 실행하고 내부적으로 여러가지 작업 처린(`애플리케이션 내부에서의 멀티 태스킹`)

<br/>

### 메인 스레드
- 모든 자바 프로그램은 메인 스레드가 `main()` 메소드를 실행하면서 시작함
    - main() 메소드의 첫 코드부터 아래로 순차적으로 실행함
    - main() 메소드의 마지막 코드를 실행하거나, return 문을 만나면 실행이 종료됨
- main 스레드(JVM이 생성)는 작업 스레드를 만들어서 병렬로 코드를 실행할 수 있음
    - 멀티 스레드를 생성해서 멀티 태스킹을 수행함
- 프로세스의 종료
    - 싱글 스레드: 메인 스레드가 종료하면 프로세스도 종료
    - 멀티 스레드: 실행 중인 스레드가 하나라도 있다면, 프로세스는 종료되지 않음
 
<br/>

### 멀티스레드 문제

- 싱글 스레드 프로그램에서는 한 개의 스레드가 객체를 독차지해서 사용하면 됨
- 멀티 스레드 프로그램에서 스레드들이 객체를 공유해서 작업해야 하는 경우, 하나의 객체를 공유함에 따라 오류가 발생할 수 았음
    - 스레드 A를 사용하던 객체가 스레드 B에 의해 상태가 변경될 수 있기 때문에 의도한 것과 다른 결과가 출력될 수 있음
- `ShareThread`
    ```java
    // 공유 객체
    public class ShareThread {
        private int value = 0;
        
        public void setValue( int value ) {
            this.value = value;
            try {
                Thread.sleep(2000);
            } catch (Exception e) { }
            System.out.println(Thread.currentThread().getName() + "의 Value 값은 " + this.value +"입니다.");
            
        }
    }
    ```
- `Main`
    ```java
    public static void main(String args[]){
        ShareThread shareTread = new ShareThread();
        Thread thred1 = new Thread(()->{
            shareTread.setValue(100);
            
        });
        
        Thread thred2 = new Thread(()->{
            shareTread.setValue(10);
        });

        thred1.setName("스레드 1");
        thred2.setName("스레드 2");
        thred1.start(); 
        thred2.start();
    }
    ```
- 결과
    ```java
    // 결과 
    스레드 2의 Value 값은 10입니다.
    스레드 1의 Value 값은 10입니다.
    ```
    - 스레드가 사용중인 객체를 다른 스레드가 변경할 수 없도록 하려면, 스레드 작업이 끝날 때까지 객체레 잠금을 걸어 다른 스레드가 사용할 수 없도록 해야함


<br/>

## Java의 스레드 동기화

`스레드 동기화`: 멀티스레드 환경에서 여러 스레드가 하나의 공유 자원에 동시에 접근하지 못하도록 막는 것(동기화 순서를 보장하지 않음)

<br/>
  
### 1) 동기화 메소드와 동기화 블록 - Synchronized

- `임계 영역`: 멀티 스레드 프로그램에서 단 하나의 스레드만 실행할 수 있는 코드 영역
- 자바는 임계 영역을 지정하기 위해 동기화 메서드와 동기화 블록을 제공함
    - 스레드가 객체 내부의 동기화 메소드나 동기화 블록에 들어가면, 즉시 객체에 잠금(`Lock`)을 걸어 다른 스레드가 임계 영역 코드를 실행하지 못하도록 함

**동기화 메소드(Synchronized Method)**
- 메서드 선언에 `synchronized` 키워드를 붙임
    ```java
    public synchronized void setValue(int value) {
        // 임계 영역: 하나의 스레드만 실행
        this.value = value;
        try {
            Thread.sleep(2000);
        } catch (Exception e) {
        }
        System.out.println(Thread.currentThread().getName() + "의 Value 값은 " + this.value +"입니다.");  
    }
    ```
    - 클래스의 인스턴스 단위로 Lock을 걸게 됨
        - 동일한 인스턴스 내 `synchronized`가 적용된 메서드끼리는 일괄적으로 Lock을 공유함
        - 각각의 스레드가 서로 다른 인스턴스에 접근할 경우, Lock은 공유되지 않음
    - 동기화 메서드는 메소드 전체 내용이 임계 영역
        - 동기화 메소드를 실행하는 즉시, 포함하는 모든 객체에 잠금이 일어나고 스레드가 동기화 메소드의 실행을 종료하면 잠금이 풀림
    - 메서드 전체 내용이 아닌, 일부 내용만 임계 영역으로 만들고 싶은 경우에는 동기화 블록을 생성함
- `static synchronized method`
    - 인스턴스가 아닌 클래스 단위로 Lock이 발생
        - 다른 인스턴스에 접근하더라도, Lock을 공유함
    - 인스턴스 단위의 Lock과 클래스 단위의 Lock은 공유되지 않음


**동기화 블록(Synchronized Block)**
- `synchronized(공유 객체) { 임계 영역 }`
    - synchronized의 인자 값(`공유 객체`)에 Lock을 걸 대상을 지정
    ```java
    public  void setValue(int value) {
        // 여러 스레드가 실행가능한 영역
        synchronized ( this ) {
            // 암계 영역
            this.value = value;
            try {
                Thread.sleep(2000);
            } catch (Exception e) { }
            System.out.println(Thread.currentThread().getName() + "의 Value 값은 " + this.value +"입니다.");
        }
        // 여러 스레드가 실행가능한 영역
    }
    ```
    - 클래스 인스턴스의 block 단위로 Lock을 걸게 됨
        - 특정 객체를 명시하면 해당 객체에만 Lock을 걸어, 해당 객체에 Lock을 거는 다른 block들끼리 Lock을 공유힘
        - 인자 값이 `this`인 경우, 참조 변수(this) 객체의 Lock을 사용하기 때문에 동기화 메소드와 같이 인스턴스 단위로 Lock이 걸리게 됨
    - 동기화 블록의 외부 코드들은 여러 스레드가 동시에 실행할 수 잇음
    - 동기화 블록의 내부 코드는 임계 영역이므로 한 번에 하나의 스레드만 실행 가능함
        - 동기화 블록이 여러 개 있을 경우, 스레드가 이들 중 하나를 실행할 때, 다른 스레드는 해당 메소드는 물론이고 다른 동기화 메소드 및 블록도 실행할 수 없음(일반 메소드는 실행 가능)
- `static synchronized block`
    - static method 안에 synchronized block을 지정
    - 클래스 단위로 Lock이 발생
    - block의 인자로 정적 인스턴스나 클래스만 사용함

**Synchronized 문제점**
  
- blocking을 사용하여 멀티스레드 환경에서 공유 객체를 동기화함
- blocking을 사용하면 성능 이슈가 발생할 수 있음
    - 특정 스레드가 해당 블럭 전체에 lock을 걸면, 해당 lock에 접근하는 스레드들은 블로킹 상태에 들어가기 때문에 아무 작업도 하지 못한채 자원을 낭비하게 됨
    - blocking 상태의 스레드를 준비 혹은 실행 상태로 변경하기 위해 시스템 자원을 사용하는데 이는 성능 저하로 이어짐

> `Blocking`: 자신의 수행 결과가 끝날 때가지 제어권을 갖고 있는 것  
> `Non-Blocking`: 자신이 호출되었을 때, 제어권을 바로 자신을 호출한 쪽으로 넘기고 자신을 호출한 쪽에서 다른 작업을 할 수 있도록 하는 것

<br/>

### 2) Volatile

- 가시성 문제를 동기화시켜 줌
    - 멀티 스레드나 멀티 코어 환경에서 각 CPU는 메인 메모리 변수 값을 참조하지 않고, 각 CPU의 캐시 영역에서 메모리를 참고함
    - 멀티 스레드 환경에서 메인 메모리와 CPU 캐시의 값이 다른 경우 발생할 수 있는 문제를 가시성 문제라고 함
- `non-volatil` 변수는 메인 메모리로부터 CPU 캐시에 값을 복사하여 작업을 수행함
    - 여러 스레드가 각각 `non-volatil` 값을 읽을 때 CPU 캐시에 저장된 값이 다를 경우, 값의 불일치 발생(가시성 문제)
- `volatile` 키워드가 붙은 자원은 read, write 작업이 CPU Cache Memory가 아닌 Main Memory에서 이뤄짐
    - 메인 메모리에 저장하고 읽어오기 때문에 값의 불일치(가시성 문제)를 해결할 수 있음
- 여러 스레드에서 메인 메모리에 있는 공유 자원에 동시에 접근할 수 있으므로, 여러 스레드에서 수정하게 되면 동시 접근 문제를 해결할 수 없음
    
<br/>


### 3) Atomic Type

- 자바의 concurrency API에서 제공(`java.util.concurrent.atomic`)
- Synchronized와 다르게 `blocking`이 아닌 `non-blocking` 방법을 사용하며 원자성을 보장함
- 사용시 내부적으로 CAS(Compare-And-Swap) 알고리즘을 사용해 Lock 없이 동기화를 처리할 수 있음
    ```java
    public class AtomicInteger extends Number implements java.io.Serializable { 
        private volatile int value; 

        public final int incrementAndGet() { 
            int current; 
            int next; 
            do { 
                current = get(); next = current + 1; 
            } 
            while (!compareAndSet(current, next)); 
            
            return next; 
        } 
            
        public final boolean compareAndSet(int expect, int update) { 
            return unsafe.compareAndSwapInt(this, valueOffset, expect, update); 
        } 
    }
    ```
    - Atomic Type은 내부에 `Volatile` 변수로 선언되어 있음
- 단일 변수에 대해 Atomic Operations을 지원함
    - 원시 타입과 참조 타입 두 종류의 변수에 모두 적용 가능
    - 변수를 선언할 때 `Atomic` 타입으로 선언하여 사용
- CAS 알고리즘
    - CPU가 메인 메모리의 자원을 CPU 캐시 메모리로 가져와 연산을 수행하는 동안, 다른 스레드에서 연산이 수행되어 메인 메모리의 자원 값이 바뀌었을 경우, 기존 연산을 실패처리하고 새로 바뀐 메인 메모리 값으로 재수행하는 방식 
    - 메인 메모리와 캐시 메모리의 값이 일치하면 새로운 값으로 교체하고, 그렇지 않다면 기존 교체를 실패시키고 계속 재시도함
- Atomic Type의 `compareAndSet(expect, update)` 메소드
    - CAS를 이용하여, 메모리에 저장된 값과 스레드가 현재 가지고 있는 값을 비교하여 동일한 경우에만 해당 메모리 위치의 내용을 새로운 지정된 값으로 수정하는 역할
    - lock-free 방식으로 루프를 돌기 때문에 스레드의 상태를 변경하는 작업(block <-> unblock)이 발생하지 않아 비용을 더 절감할 수 있음

<br/>

## ThreadLocal

- 스레드 단위로 로컬 변수를 사용할 수 있도록하는 스레드 지역변수
    - 각각의 스레드가 변수에 대해 독립적으로 접근할 수 있음
    - 스레드가 실행하는 모든 코드에서 그 스레드에 설정된 변수 값을 사용할 수 있음
- 스레드의 정보를 Key로 하여 값을 저장하는 Map 구조를 가짐

<br/>