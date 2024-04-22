# 4-way Handshake

TCP 프로토콜이 3 way handshake를 통해 연결을 수립하였다면 연결 해제는 4 way handshake를 사용한다.  

### 4-way Handshake 과정

<br>

<img alt="image" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/60910e9b-560d-464d-9aae-8fa2f0867e23" width="604"/>

<br>

**[Step1]**

- 클라이언트가 연결 해제 요청 FIN을 서버로 전송한다.
- 클라이언트 상태: ESTABLISHED -> FIN_WAIT_1

**[Step2]**

- 서버는 연결 해제 요청을 승인한다는 의미로 클라이언트에 ACK을 전송한다.
- 연결 해제 요청에 대한 승인으로, 바로 해제 되는 것이 아닌, `CLOSE_WAIT` 상태로 진입한 후 연결 해제 준비를 한다.
- 서버 상태: ESTABLISHED -> CLOSE_WAIT
- 클라이언트 상태: FIN_WAIT_1 -> FIN_WAIT_2

> `Close-Wait`상태 후 애플리케이션이 종료됬다면 다음 단계로 넘어간다.

  
**[Step3]**

- 서버는 연결 해제 준비가 완료되었다는 FIN을 클라이언트로 전송한다.
- 서버 상태: CLOSE_WAIT -> LAST_ACK
- 클라이언트 상태: FIN_WAIT_2 -> TIME_WAIT

**[Step4]**

- 클라이언트는 TIME_WAIT 시간 만큼 대기 후 서버로 ACK을 전송함으로써 마침내 연결이 해제된다.대개의 경우 2MSL(maximum segment lifetime - 1분~4분) 동안 TIME_WAIT 상태로 대기한다.
- 클라이언트는 연결을 해제하겠다는 ACK을 바로 서버로 보내는 대신 2MSL 동안 TIMW_WAIT 상태로 대기함으로써 서버는 아직 도착하지 못한 패킷들을 받을 수 있는 여유 시간이 생긴다.
- 클라이언트 상태: TIME_WAIT -> CLOSED
- 서버 상태: LAST_ACK -> CLOSED

<br>

### TIME-WAIT

TIME-WAIT 상태가 필요한 이유를 살펴보자.  

만약 TIME-WAIT 가 짧다면 아래의 문제점들이 발생한다.  

* 지연패킷 발생
   * 세션이 종료된 후에도 도착하지 못한 잔여 패킷이 있는 경우
   * 만약 이미 다른 연결로 진행되었다면 해당 패킷이 문제를 일으킬 수도 있다.

<br>

<img alt="image" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/d12f7ae9-4a90-458c-9569-88d164cd1819" width="404"/>

<br>

* 원격 종단의 연결 확인 불가
   * 만약 마지막 **ACK** 유실시 상대방은 **LAST_ACK** 상태에 빠지게 된다. 
   * 새로운 **SYN** 패킷 전달시 **LAST_ACK** 상태의 Passive Close는 **RST** 를 리턴한다. (RST는 아래 참조)
   * 새로운 연결은 오류를 내며 실패한다.  

<br>

<img alt="image" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/06f1e932-d178-46df-a4f8-e26ce22136dd" width="404"/>

<br>


따라서 반드시 `TIME_WAIT`이 일정 시간 남아 패킷의 오작동을 막아야 한다.  
RFC 793 에는 `TIME_WAIT`을 2 MSL로 규정했다. 여기서 MSL은 *maximum segment lifetime*의 약자로 IP datagram이 네트워크에 존재할 수 있는
시간이다.  
TCP를 구현할 때 반드시 MSL값을 정해줘야하는데 RFC1122에 나온 추천은 2분이다. 그렇지만 Berkeley-derived implementation에서는 30초라서 보통 이 2MSL은 1분에서 4분정도 된다.

<br>

### 컨트롤 비트

TCP 헤더에는 커넥션의 상태를 나타내는 필드가 있다.
8비트로 구성되어 있고 각각의 비트는 의미하는 바가 다르다.  

<br>

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


이 컨트롤 비트를 사용해 해당 패킷이 ACK, SYN, FIN 인지 확인 할 수 있다.

<br>

만약 TCP 커넥션을 4-way Handshake를 할 여유 없이 빨리 종료해야 하는 경우 RST 플래그를 1로 설정하면 된다.  
RST 플래그가 사용되는 경우는 아래와 같다.   

* 존재하지 않는 TCP 연결에 대해 비SYN 세그먼트가 수신된 경우
* 열린 커넥션에서 일부 TCP 구현은 잘못된 헤더가 있는 세그먼트가 수신된 경우
* 일부 구현에서 기존 TCP 연결을 종료해야 하는 경우
* 원격 호스트에 연결할 수 없고 응답이 중지된 경우  

<br>

이렇게 갑작스럽게 연결이 해제된 것을 **Abrupt connection release** 라고 부르고, 양쪽 커넥션이 서로 커넥션을 닫을 때까지 연결되어 있는 정상적인 
종료를 **Graceful connection release**이라고 부른다.   

<br>


---

#### ref

[[네트워크] TCP/UDP와 3 -Way Handshake & 4 -Way Handshake](https://velog.io/@averycode/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-TCPUDP%EC%99%80-3-Way-Handshake4-Way-Handshake)  

[[Network]OSI-7-Layers](https://github.com/reddevilmidzy/Computer-Science/blob/main/Network/%5BNetwork%5DOSI-7-Layers.md#tcp)  

[CLOSE_WAIT 문제 해결](https://docs.likejazz.com/close-wait/)  

[TIME_WAIT 상태란 무엇인가](https://docs.likejazz.com/time-wait/)  

[TransPort Layer(2)](https://lazymankook.tistory.com/6)  
