# 가비지 컬렉션 Garbage Collection(GC)

---

## 가비지 컬렉션이란?

* Heaq 영역에서 동적으로 할당했던 메모리 중 사용하지 않는 메모리 객체(garbage)를 모아 주기적으로 제거하는 프로세스
* C / C++ 언어에서는 가비지 컬렉션이 없어 프로그래머가 수동으로 메모리 할당과 해제를 해야한다.
* 자바는 JVM에 탑재된 가비지 컬렉터가 메모리 관리를 대행하기에 개발자 입장에서 메모리 관리와, 메모리 누수(Memory Leak) 문제에 대해 완벽히 관리하지 않아도 된다.
* 특정 개체가 garbage 인지 판단하기 위해서 도달능력(Reachability) 이라는 개념을 사용한다
* 객체에 레퍼런스가 있으면 **Reachable**로 구분되고, 객체에 유효한 레퍼런스가 없으면 **Unreachable**로 구분하고 가비지 컬렉션의 대상이 된다.


<br>

## 가비지 컬렉션 과정

### stop-the-world

* GC을 실행하기 위해 JVM이 애플리케이션 실행을 멈추는 것
* stop-the-world가 발생하면 GC를 실행하는 스레드를 제외한 나머지 스레드는 모두 작업을 멈춘다.
* 어떤 GC 알고리즘을 사용하더라도 stop-the-world 는 발생하고, GC 튜닝이란 이 stop-the-world 시간을 줄이는 것

<br>

### weak generational hypothesis

자바 가비지 컬렉터는 두 가지 가설(전제 조건)하에 만들어졌다.  

* 대부분의 객체는 금방 접근 불가능 상태(unreachable)가 된다.
* 오래된 객체에서 젊은 객체로의 참조는 아주 적게 존재한다.

이 가설의 장점을 최대한 살리기 위해서 HotSpot VM 에서는 크게 2개로 물리적 공간을 나누었다.

* `Young Generation 영역`
  * 새롭게 생성한 객체의 대부분이 여기에 위치한다.
  * 대부분의 객체가 금방 접근 불가능 상태가 되기 때문에 많은 객체가 Young 영역에 생성되었다가 사라진다.
  * 이 영역에서 객체가 사라질 때 **minor GC**가 발생한다고 한다.
* `Old Generation 영역`
  * 접근 불가능 상태로 되지 않아 Young 영역에서 살아남은 객체가 여기로 복사된다.
  * 대부분 Young 영역보다 크게 할당하며, 크기가 큰 만큼 Young 영역보다 GC는 적게 발생한다.
  * 이 영역에서 객체가 사라질 때 **Major GC** 혹은 **Full GC**가 발생한다고 한다.

<br>

## YOUNG 영역의 구성

Young 영역은 3개의 영역이 있다.

* Eden 영역
* Survivor 영역 (2개)

각 영역의 처리 절차 순서는 아래와 같다.

* 새로 생성한 대부분의 객체는 Eden 영역에 위치한다.
* Eden 영역에서 GC가 한 번 발생한 후 살아남은 객체는 Survivor 영역 중 하나로 이동된다.
* Eden 영역에서 GC가 발생하면 이미 살아남은 객체가 존재하는 Survivor 영역으로 객체가 계속 쌓인다.
* 하나의 Survivor 영역이 가득 차게 되면 그 중에서 살아남은 객체를 다른 Survivor 영역으로 이동한다.
* 그리고 가득찬 Survivor 영역은 아무 데이터도 없는 상태가 된다.
* 이 과정을 반복하다가 계속해서 살아남아 있는 객체는 Old 영역으로 이동하게 된다.

위 과정을 **minor GC** 라고 한다.  

정리하면, Eden 영역에는 new를 통해 새로 생성된 객체가 위치하고, Survivor 영역에는 최소 1번의 GC 이상 살아남은 객체가 존재한다.  
그리고 Survivor 영역 둘중에
하나는 반드시 비어 있는 상태로 남아 있어야 한다. 만약 Survivor 영역에 모두 데이터가 존재하거나, 두 영역 모두 사용량이 0이라면 시스템은 정상적인 상황이 아니다.

<br>

<img alt="Young Generation과 Old Generation" height="230" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/408f8cf3-1fce-4a14-883b-204a83eb76a0" width="700"/>


<br>

## Old 영역에 대한 GC (Major GC)

* Survivor 영역 객체의 age(객체가 살아남은 횟수) 값이 임계값에 다다르면 Old 영역으로 객체가 넘어오고 이 과정을 프로모션(Promotion) 이라고 한다.
* Major GC는 객체들이 계속 프로모션 되어 Old 영역의 메모리가 부족해지면 발생한다.
* stop-the-world 가 발생하고, Mark and Sweep 작업을 한다.

<br>

<img alt="img1 daumcdn" height="230" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/63e087b4-86b2-4542-a69c-a35ab4012584" width="700"/>

<br>

<br>


GC 방식은 JDK 7을 기준으로 5가지 방식이 있다.


* Serial GC
* Parallel GC
* Parallel Old GC(Parallel Compacting GC)
* Concurrent Mark and Sweep(CMS)
* G1(Garbage First) GC

<br>


### **Serial GC** 

* 서버의 CPU 코어가 1개일 때 사용하기 위해 개발된 가장 단순한 GC
* GC를 처리하는 스레드가 1개이기에 stop-the-world 시간이 가장 길다.
* mark-sweep-compact 알고리즘 사용
  * 이 알고리즘의 첫 단계는 Old 영역에 살아 있는 객체를 식별(Mark)하는 것이다.
  * 그 다음 heap의 앞 부분부터 확인하여 살아 있는 것만 남긴다.(Sweep)
  * 마지막 단계에서 각 객체들이 연속되게 쌓이도록 힙의 가장 앞 부분부터 채워서 객체가 존재하는 부분과 객체가 없는 부분으로 나눈다.(Compaction)


**Serial GC 실행 명령어**

* 자바 프로그램을 실행할때 -XX:+UseSerialGC GC 옵션을 지정하여 해당 가비지 컬렉션 알고리즘으로 힙 메모리를 관리하도록 실행할 수 있다.

```shell
java -XX:+UseSerialGC -jar Application.java
```

<br>

### **Parallel GC**

* Java 8의 디폴트 GC
* Serial GC와 기본적인 알고리즘은 같지만, Young 영역의 Minor GC를 멀티 스레드로 수행 (Old 영역은 여전히 싱글 스레드)
* Serial GC에 비해 stop-the-world 시간 감소

Serial GC와 Parallel GC의 스레드 비교한 그림

<br>

<img alt="serial GC and Parallel GC" height="200" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/dc22e355-ef7a-4fc7-8adf-6d1b22bd14e9" width="500"/>

<br>

**Parallel GC 실행 명령어**

* GC 스레드는 기본적으로 cpu 개수만큼 할당된다.
* 옵션을 통해 GC를 수행할 스레드의 갯수 등을 설정해줄 수 있다.

```shell
java -XX:+UseParallelGC -jar Application.java
# -XX:ParallelGCThreads=N : 사용할 스레드의 갯수
```


<br>

### **Parallel Old GC**

* Parallel GC를 개선한 버전
* Young 영역 뿐만 아니라 Old 영역도 멀티 스레드를 사용한다.
* Mark-Summary-Compaction 사용
  * Summary 단계는 앞서 GC를 수행한 영역에 대해서 살아 있는 객체를 식별한다는 점에서 Mark-Sweep-Compaction 알고리즘의 Sweep와 다르며 더 복잡한 단계를 거친다.


**Parallel Old GC 실행 명령어**

```shell
java -XX:+UseParallelOldGC -jar Application.java
# -XX:ParallelGCThreads=N : 사용할 스레드의 갯수
```

<br>

### **CMS GC**

아래의 그림은 Serial GC와 CMS GC의 절차를 비교한 그림이다.

<br>

<img alt="helloworld-1329-5" height="200" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/10f94c30-0af2-4723-8a81-a16468e119fe" width="300"/>

<br>

* Initial Mark 단계에서 클래스 로더에서 가장 가까운 객체 중 살아 있는 객체만 찾는 것으로 끝낸다.
  * 따라서 멈추는 시간이 매우 짧다.
* Concurrent Mark 단계에서는 방금 살아있다고 확인한 객체에서 참조하고 있는 객체들을 따라가면서 확인한다.
  * 이 단계의 특징은 다른 스레드가 실행중인 상테에서 동시에 진행한다는 것이다.
* Remark 단계에서는 Concurrent Mark 단계에서 새로 추가되거나 참조가 끊긴 객체를 확인한다.
* Concurrent Sweep 단계에서는 가비지를 정리하는 작업을 실행한다.
  * 이 작업도 다른 스레드가 실행되고 있는 상황에서 진행한다.


**CMS GC의 장단점**

* 장점
  * 이런 단계로 진행되는 GC 방식이기에 stop-the-world 시간이 매우 짧다.  
  * 모든 애플리케이션의 응답 속도가 매우 중요할 때 CMS GC를 사용한다.  
* 단점
  * 다른 GC 방식보다 메모리와 CPU를 더 많이 사용한다.
  * Compaction 단계가 기본적으로 제공되지 않는다.


**CMS GC 실행 명령어**

```shell
# deprecated in java9 and finally dropped in java14
java -XX:+UseConcMarkSweepGC -jar Application.java
```

<br>

### **G1 GC**

* CMS GC를 대체하기 위해 jdk 7 버전에서 최초로 release된 GC
* ava 9+ 버전의 디폴트 GC로 지정
* 구조는 Young 영역과 Old 영역과는 사뭇 다른 Region 이라는 개념을 사용한다.
* 전체 Heap 영역을 Region이라는 영역으로 체스같이 분할하여 상황에 따라 Eden, Survivor, Old 등 역할을 고정이 아닌 동적으로 부여
* Garbage로 가득찬 영역을 빠르게 회수하여 빈 공간을 확보하므로, 결국 GC 빈도가 줄어드는 효과를 얻게 되는 원리
* 

<br>

<img alt="img1 daumcdn" height="300" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/fee6b60c-2140-4f06-a2df-d53c5e62c223" width="300"/>

<br>


**G1 GC 실행 명령어**

```shell
java -XX:+UseG1GC -jar Application.java
```


---

#### ref

[Java Garbage Collection](https://d2.naver.com/helloworld/1329)  
[가비지 컬렉션 동작 원리 & GC 종류 💯 총정리](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EA%B0%80%EB%B9%84%EC%A7%80-%EC%BB%AC%EB%A0%89%EC%85%98GC-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC)  
[[10분 테코톡] 🐥엘리의 GC](https://youtu.be/Fe3TVCEJhzo)

