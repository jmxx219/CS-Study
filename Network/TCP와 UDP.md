# TCP와 UDP

---

TCP와 UDP는 OSI 7계층 중 전송 계층에서 사용되는 프로토콜로 
포트 번호로 패킷을 전달하는 애플리케이션을 식별한다.  

<br/>

## TCP

**TCP**(Transmission Control Protocol)는 데이터를 세그먼트(Segment) 단위로 쪼개어
신뢰성을 기반한 통신을 제공한다.  

<br/>

### TCP의 헤더

TCP의 헤더 크기는 최저 20바이트로, 송수신지의 번호 뿐만 아니라 데이터 검증 및 순서 확인을 위한 정보등을 포함하고 있다.

<br>

<img alt="TCP 헤더" height="300" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/8e0823c5-4f2f-436b-a055-ea63d0902792" width="600"/>

<br/>



* 송신지/수신지 포트 번호  
  UDP의 포트 번호와 마찬가지로 애플리케이션의 식별에 사용되는 숫자이다.


* 시퀀스 번호  
  시퀀스 번호는 TCP 세그먼트를 올바른 순서로 정렬하기 위해 사용되는 필드이다. 송신 측 단말은 애플리케이션에서 받은 데이터의 각 바이트에
  대해 **초기 시퀀스 번호**(ISN, Initial Sequence Number)에서 연번을 부여한다.
 

* 확인 응답 번호  
  확인 응답 번호 (ACK: Acknowledge)는 다음 전송할 데이터의 위치를 알려준다.


* 데이터 오프셋   
  데이터 오프셋(data offset)은 TC 헤더의 길이를 나타내는 필드이다.


* 컨트롤 비트   
  컨트롤 비트는 커넥션의 상태를 나타내는 필드이다.

| 비트     | 플래그 이름 | 설명                               | 개요                                   |
|--------|--------|----------------------------------|--------------------------------------|
| 1번째 비트 | CWR    | Congestion Window Reduced        | ECN-Echo에 따라, 혼잡 윈도우가 줄어든 것을 알리는 플래그 |
| 2번째 비트 | ECE    | ECN-Echo                         | 혼잡이 발생한 것을 통신 상대에게 알리는 플래그           |
| 3번째 비트 | URG    | Urgent Pointer field significant | 긴급을 나타내는 플래그                         |
| 4번째 비트 | ACK    | Acknowledgment field significant | 확인 응답을 나타내는 플래그                      |
| 5번째 비트 | PSH    | Push Function                    | 빠르게 애플리케이션에 데이터를 전달하는 플래그            |
| 6번째 비트 | RST    | Reset the connection             | 커넥션을 강제로 끊는 플래그                      |
| 7번째 비트 | SYN    | Synchronize sequence numbers     | 커넥션을 여는 플래그                          |
| 8번째 비트 | FIN    | No more data from sender         | 커넥션을 닫는 플래그                          |

<br>

* 윈도우 크기   
  확인 응답을 기다리지 않고 받을 수 있는 데이터 크기를 알린다. 필드의 길이가 16비트이므로 윈도우의 최대 크기는 65,545 바이트이다. 


* 체크섬   
  체크섬은 송신 중 발생될 수 있는 비트 오류를 검출하기 위해 사용된다. 헤더 및 데이터를 16비트 단위로 분할하여 비트 합을 구한 뒤, 이에 대한 1의 보수를 취함으로써 계산된다. 그리고 만약 carry가 발생한다면 wrap around를 적용한다. 


* 긴급 포인터   
  긴급 포인터(urgent pointer)는 컨트롤 비트의 URG 플래그가 '1'로 설정됐을때 유효한 필드이다. 긴급 데이터가 있을 때 긴급 데이터를 나타내는 가장 마지막 바이트의
  시퀀스 번호가 설정된다.


* 옵션  
  옵션은 TCP에 관련된 확장 기능을 알리기 위해 사용한다.   


<br/>

### 흐름 제어 & 혼잡 제어

* 흐름 제어
* * 송신측과 수신측의 데이터 처리 속도 차이를 해결하기 위한 기법
* * Flow control은 receiver가 packet을 지나치게 많이 받지 않도록 조절하는 것
* * 기본 개념은 receiver가 sender에게 현재 자신의 상태를 feedback 한다는 점
* 혼잡 제어
* * 송신측의 데이터 전달과 네트워크의 데이터 처리 속도 차이를 해결하기 위한 기법


<br/>

### **흐름 제어 (Flow Control)**


* 수신측이 송신측보다 데이터 처리 속도가 빠르면 문제 없지만, 송신측의 속도가 빠를 경우 문제가 발생
* 수신측에서 제한된 저장 용량을 초과한 이후에 도착하는 데이터는 손실될 수 있으며, 만약 손실된다면 불필요한 응답과 데이터 전송이 발생

<br>

**해결 방법**

* **Stop and Wait**: 매번 전송한 패킷에 대해 확인 응답을 받아야만 그 다음 패킷을 전송하는 방법

<img alt="Stop and Wait" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/155dc699-96b4-4e41-9da7-0910757e943f" width="300" height="400">


<br/>

* **Sliding Window** (Go Back N ARQ): 수신측에서 설정한 윈도우 크기만큼 송신측에서 확인 응답없이 세그먼트를 전송할 수 있게 하여 데이터 흐름을 동적으로 조절하는 제어기법

<img alt="Sliding Window" height="300" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/210fbc44-4365-4c47-853c-40344b52f399" width="600"/>

<br/>

* **Selective Repeat ARQ** (선택적 재전송): 송신 순서에 영향을 받지 않아 에러가 발생한 해당 프레임만 재전송 받을 수 있는 제어기법

<img alt="Selective Repeat ARQ" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/4ecf0391-ec3c-49bc-a37b-7250f02511f6"/>

<br>


<br/>

### **혼잡 제어 (Congestion Control**

* 송신측의 데이터는 지역망이나 인터넷으로 연결된 대형 네트워크를 통해 전달된다. 만약 한 라우터에 데이터가 몰리게 되면 자신에게 온 데이터를 처리할 수 없게 된다.
이런 경우 호스트들은 재전송하게 되고 결국 혼잡만 가중시켜 오버플로우나 데이터 손실이 발생된다. 
* 이러한 네트워크의 혼잡을 피하기 위해 송신측에서 보내는 데이터의 전송속도를 강제로 줄이게 되는데 이러한 작업이 혼잡제어이다.

<br/>

**해결 방법**

<br/>


![혼잡 제어](https://github.com/reddevilmidzy/CS-Study/assets/78539407/565fc390-c3f7-4de7-a314-0a34ae304daa)

<br/>

- **AIMD(Additive Increase / Multiplicative Decrease)**
  - 처음에 패킷을 하나씩 보내고 이것이 문제없이 도착하면 window 크기(단위 시간 내에 보내는 패킷의 수)를 1씩 증가시켜가며 전송하는 방법
  - 패킷 전송에 실패하거나 일정 시간을 넘으면 패킷의 보내는 속도를 절반으로 줄인다.
  - 공평한 방식으로, 여러 호스트가 한 네트워크를 공유하고 있으면 나중에 진입하는 쪽이 처음에는 불리하지만, 시간이 흐르면 평형상태로 수렴하게 되는 특징이 있다.
  - 문제점은 초기에 네트워크의 높은 대역폭을 사용하지 못하여 오랜 시간이 걸리게 되고, 네트워크가 혼잡해지는 상황을 미리 감지하지 못한다. 즉, 네트워크가 혼잡해지고 나서야 대역폭을 줄이는 방식이다.

<br>

- **Slow Start (느린 시작)**
  - AIMD 방식이 네트워크의 수용량 주변에서는 효율적으로 작동하지만, 처음에 전송 속도를 올리는데 시간이 오래 걸리는 단점이 존재했다.
  - Slow Start 방식은 AIMD와 마찬가지로 패킷을 하나씩 보내면서 시작하고, 패킷이 문제없이 도착하면 각각의 ACK 패킷마다 window size를 1씩 늘려준다. 즉, 한 주기가 지나면 window size가 2배로 된다.
  - 전송속도는 AIMD에 반해 지수 함수 꼴로 증가한다. 대신에 혼잡 현상이 발생하면 window size를 1로 떨어뜨리게 된다.
  - 처음에는 네트워크의 수용량을 예상할 수 있는 정보가 없지만, 한번 혼잡 현상이 발생하고 나면 네트워크의 수용량을 어느 정도 예상할 수 있다.
  - 그러므로 혼잡 현상이 발생하였던 window size의 절반까지는 이전처럼 지수 함수 꼴로 창 크기를 증가시키고 그 이후부터는 완만하게 1씩 증가시킨다.

<br>


- **Fast Retransmit (빠른 재전송)**
  - 빠른 재전송은 TCP의 혼잡 조절에 추가된 정책이다.
  - 패킷을 받는 쪽에서 먼저 도착해야할 패킷이 도착하지 않고 다음 패킷이 도착한 경우에도 ACK 패킷을 보내게 된다.
  - 단, 순서대로 잘 도착한 마지막 패킷의 다음 패킷의 순번을 ACK 패킷에 실어서 보내게 되므로, 중간에 하나가 손실되게 되면 송신 측에서는 순번이 중복된 ACK 패킷을 받게 된다. 이것을 감지하는 순간 문제가 되는 순번의 패킷을 재전송 해줄 수 있다.
  - 중복된 순번의 패킷을 3개 받으면 재전송을 하게 된다. 약간 혼잡한 상황이 일어난 것이므로 혼잡을 감지하고 window size를 줄이게 된다.

<br>

- **Fast Recovery (빠른 회복)**
  - 혼잡한 상태가 되면 window size를 1로 줄이지 않고 반으로 줄이고 선형증가시키는 방법이다. 이 정책까지 적용하면 혼잡 상황을 한번 겪고 나서부터는 순수한 AIMD 방식으로 동작하게 된다.


<br>

## UDP

**UDP**(User Datagram Protocol)은 비신뢰성 통신을 제공하는 대신 빠른 속도로 통신을 할 수 있다.  

<br/>

### UDP의 헤더

UDP의 헤더는 TCP와 비교했을 때 훨씬 더 작은 크기의 헤더를 갖고 있다.   

<br>

<img alt="UDP 헤더" width="600" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/f59652c9-eda8-4d77-bf4a-ce0bde8bd611" height="200"/>

<br/>

* 송신지/수신지 포트 번호
* UDP 데이터그램 길이
* 체크섬   

<br/>

### QUIC

앞서 말했듯이 TCP와 비교했을 때 UDP 헤더 포멧은 거의 백지와 마찬가지다. User Datagram 이라는 의미도 사용자가 정의해서 사용하라는 의미이다.  
즉 여기에 원하는 기능을 얼마든지 얹으면 되고 서버와 클라이언트의 구현에 따라 그 퍼포먼스는 크게 달라질 수 있다.  
TCP 처럼 신뢰성 있는 전송을 하고싶으면 시퀀스 번호 등을 정의하여 서버와 클라이언트 간에 패킷 유실에 대한 대처방법도 상호 정의한다면
UDP 기반의 신뢰성 있는 프로토콜을 만들 수 있다.  

이러한 개념에서 나온 것이 **QUIC** 이다. 

**QUIC**(Quick UDP Internet Connections) 프로토콜은 Google을 통해 개발이 진행되었고, 현재 모든 Google 관련 제품의 기본 프로토콜로 사용된다.
UDP 보다 안전하고 TCP 보다 빠르다는 장점을 갖고 있다. 

<br/>


TCP는 클라이언트와 서버 연결 과정에서 핸드셰이크를 사용하여 커넥션을 만든다음 통신한다.

<img alt="HTTP-request-over-TCP-@3x" height="500" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/35cba870-bb38-4022-a161-38e0549ab648" width="400"/>

반면에 QUIC은 사전에 핸드셰이크를 완료할 필요 없이 연결 시작시에 클라이언트가 바로 서버에 데이터를 보낼 수 있도록 한다.

<img alt="request-over-quic-0-RTT@3x" height="500" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/03f54ce1-3d2e-4141-9b5c-fd14a438d1ba" width="400"/>

---

### TCP와 UDP 비교

| *              | UDP        | TCP       |
|----------------|------------|-----------|
| IP 헤더의 프로토콜 번호 | 17         | 6         |
| 타입             | 커넥션리스 타입   | 커넥션 타입    | 
| 신뢰성            | 낮음         | 높음        |
| 즉시성(실시간성)      | 빠름         | 느림        |
| 패킷 교환 방식       | 데이터그램 패킷교환 | 가상화선 패킷교환 |
| 순서 보장          | X          | O         |



---

### ref

[TCP/IP (흐름제어/혼잡제어)](https://gyoogle.dev/blog/computer-science/network/%ED%9D%90%EB%A6%84%EC%A0%9C%EC%96%B4%20&%20%ED%98%BC%EC%9E%A1%EC%A0%9C%EC%96%B4.html)

[Sliding Window Protocol | Set 3 (Selective Repeat)](https://www.geeksforgeeks.org/sliding-window-protocol-set-3-selective-repeat/)

[QUIC과 HTTP/3 - 1. UDP기반 전송 프로토콜의 대두](https://www.saturnsoft.net/network/2019/03/21/quic-http3-1/)

[QUIC 0-RTT 재시작으로 더 빨리 연결하기](https://blog.cloudflare.com/ko-kr/even-faster-connection-establishment-with-quic-0-rtt-resumption-ko-kr/)
