# IPC (Inter Process Communication)

<br>

**IPC**는 Inter Process Communication의 약자로 프로세스 간의 통신을 의미한다.  

프로세스는 각각 독립적으로 실행되기 때문에 이들간의 통신은 커널 영역에서 IPC 설비를 이용해야 한다.  
반대로 스레드는 다른 스레드들과 메모리공간과 자원을 공유하기에 프로세스 통신보다 구현이 쉽다.  

<br>

## IPC 종류

<br>

### 파이프 Pipe

* 초기 UNIX 시스템의 IPC 기법으로 메시지 전달 방식의 일종
* 파일 I/O를 통해 프로세스 간 데이터를 주고 받는다.
* FIFO 구조를 갖는다.
* 파이프 생성 시 두 개의 파일 디스크립터(read, write) 가 return 된다.
* 파이프는 두 개의 프로세스에 대해 단방향 통신을 지원한다.
* 통신을 위한 메모리공간(버퍼)를 생성하여 프로세스가 데이터를 주고 받는다.
* 양쪽 모두 송/수신을 해야 하는 상황이라면 2 개의 파이프를 생성해야 한다.
  * 때문에 전이중 통신을 고려해야 한다면 파이프는 좋은 선택이 아니다.

#### PIPE의 장단점

<br>

#### 익명 파이프 Anonymous Pipe

* 익명의 파이프를 통해서 동일한 PPID를 가진 프로세스들 간의 단방향 통신을 지원한다.
* 부모 - 자식 또는 형제 프로세스 간 통신에 사용된다.
* pipe 함수로 생성한다.

<br>

![img1 daumcdn](https://github.com/reddevilmidzy/CS-Study/assets/78539407/d8bdc9a7-3c5d-45c0-a994-fb11f536ac64)

<br>

#### 네임드 파이프 Named Pipe

* 이름을 가진 파이프를 통해서 프로세스 간에 양 방향 통신을 지원한다.
  * 기본적으로 단방향을 많이 사용한다. 
  * 양방향 통신을 위해서는 O_RDWR mode 로 열어야 한다.
* 연관이 전혀 없는 프로세스 간 통신에 사용된다. 
* mkfifo or mknod 함수로 생성한다.

<br>

![img1 daumcdn](https://github.com/reddevilmidzy/CS-Study/assets/78539407/88f3eca4-7e32-4139-bdaf-71fd9a169ea8)

<br>

### 메시지 큐 Message Queue

* 메모리를 사용한 파이프이다.
* 구조체 기반으로 통신을 한다. 
* 프로세스간 다양한 통신을 할 때 사용할 수 있다.
  * 사용할 데이터에 번호를 붙여 여러 프로세스가 동시에 데이터를 쉽게 다룰 수 있다.
* msgtype에 따라 다른 구조체를 가져올 수 있다. 
* 커널에서 제공하는 Message queue 이기 때문에 EnQueue 하는데 제한이 존재 한다.

<br>

![img1 daumcdn](https://github.com/reddevilmidzy/CS-Study/assets/78539407/4b08e7b5-e579-45c5-af17-d468ae66c85b)

<br>

### 공유 메모리 Shared Memory

* 파이프, 메시지 큐가 통신을 위한 설비라면, 공유 메모리는 데이터 자체를 공유하도록 지원하는 설비이다.
* 즉 공유 메모리는 시스템 상의 공유 메모리를 통해 통신한다.
* 일정한 크기의 메모리를 프로세스 간에 공유 하는 구조이다.
* 공유 메모리는 커널에서 관리된다.
* 단순히 공유 메모리를 point 함으로써 프로세스에서 사용되는 메모리가 증가되지는 않는다.
* 중개자 없이 곧바로 메모리에 접근이 가능하여 IPC 중에서 가장 빠르게 작동한다.
* 프로세스 간 read, write를 모두 필요로 할 때 사용한다.
* 주의할 점은 프로세스간의 상요할려면 메모리 크키가 동일 해야 한다.

<br>

![img1 daumcdn](https://github.com/reddevilmidzy/CS-Study/assets/78539407/901e3570-0040-4030-a6a8-64dc3657fd53)

<br>


### 메모리 맵 Memory Map

* 메모리를 공유한다.
* 열린 파일을 메모리에 매핑시켜 공유하는 방식
  * 공유 매개체 => 파일 + 메모리
* 주로 파일로 대용량 데이터를 공유해야 할 때 사용한다.
* 메모리 맵 파일은 파일의 크기를 바꿀 수는 없으며 메모리 맵 파일을 사용하기 이전, 또는 이후에만 파일의 크기를 바꿀 수 있다.

<br>

![img1 daumcdn](https://github.com/reddevilmidzy/CS-Study/assets/78539407/56033361-db0d-42ad-98f6-b484b46ebbb1)

<br>

### 소켓 Socket

* 네트워크 소켓 통신을 통해 데이터를 공유한다.
* 클라이언트와 서버가 소켓을 통해서 통신하는 구조이다.
* 원격에서 프로세스 간 데이터를 공유할 때 사용한다.
* 소켓을 사용하려면 소켓을 생성하고, domain, type 그리고 protocol을 지정해야 한다.
* 서버단에서는 bind, listen, accept를 하여 소켓 연결을 위한 준비를 한다.
* 클라이언트단에서는 connect를 통해 서버에 요청한다.
* 연결 수립 후 send를 통해 데이터를 주고받고, 종료는 close()를 사용한다.

<br>

![img1 daumcdn](https://github.com/reddevilmidzy/CS-Study/assets/78539407/bdc6349a-8b79-43cc-a77b-185ce56e1dc6)

<br>

### RPC

- RPC(Remote Procedure Call)는 마치 함수를 호출하는 것처럼 원격 컴퓨터의 함수를 호출 할 수 있도록 하는 기술입니다.
- Socket과의 주요한 차이점은 `RPC`는 `네트워크 통신에 대한 세부적인 내용을 알지 않고도 원격 함수를 호출`할 수 있지만 `소켓방식`은 소켓 연결, 데이터 전송, 에러 처리 등을 직접 코딩해야 한다는 `추상화 레벨에서의 차이점`이 있습니다.


### 세마포어 Semaphore

프로세스 간의 데이터 통신 및 공유 시에 데이터 불일치가 발생하지 않도록 [세마포어](https://github.com/jmxx219/CS-Study/blob/main/OperatingSystem/%EB%AE%A4%ED%85%8D%EC%8A%A4%EC%99%80%20%EC%84%B8%EB%A7%88%ED%8F%AC%EC%96%B4.md#%EC%84%B8%EB%A7%88%ED%8F%AC%EC%96%B4semaphore)가 사용된다.

<br>

---


| IPC 종류   | 익명 파이프          | 네임드 파이프          | 메시지 큐            | 공유 메모리           | 메모리 맵            | 소켓              |
|----------|-----------------|------------------|------------------|------------------|------------------|-----------------|
| 사용 시기    | 부모 자식 간 단방향 통신시 | 다른 프로세스와 단방향 통신시 | 다른 프로세스와 단방향 통신시 | 다른 프로세스와 양방향 통신시 | 다른 프로세스와 양방향 통신시 | 다른 시스템간 양방향 통신시 |
| 공유 매개체   | 파일              | 파일               | 메모리              | 메모리              | 파일 + 메모리         | 소켓              |
| 통신 단위    | 스트림             | 스트림              | 구조체              | 구조체              | 페이지              | 스트림             |
| 통신 방향    | 단방향             | 양방향              | 단방향              | 양방향              | 양방향              | 양방향             | 
| 통신 가능 범위 | 동일 시스템          | 동일 시스템           | 동일 시스템           | 동일 시스템           | 동일 시스템           | 동일 + 외부 시스템     |



---

#### ref

[[프로세스간 통신] IPC(inter process communication) 종류 (Linux)](https://doitnow-man.tistory.com/110)  
[IPC(Inter Process Communication)](https://gyoogle.dev/blog/computer-science/operating-system/IPC.html)  
[IPC의 종류와 특징](https://jwprogramming.tistory.com/54)  
[[메모리] share memory 사용법](https://doitnow-man.tistory.com/68)