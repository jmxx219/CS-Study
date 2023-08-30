# 4-way Handshake

TCP 프로토콜이 3 way handshake를 통해 연결을 수립하였다면 
연결 해제는 4 way handshake를 사용한다.  

---

## 4-way Handshake 동작 과정

종료를 위한 총 4번의 통신과정이 이루어진다.  

<br>

1. Client -> `FIN` -> Server
2. Client <- `ACK` <- Server
3. Client <- `FIN + ACK` <- Server
4. Client -> `ACK` -> Server

<br>

### Half-Close

종료 신호를 보낼때 FIN과 ACK 을 같이 보내는데 이는 **Half-Close** 기법이다.  
종료 요청자가 바로 완전히 종료하는 것이 아니라 처음 보내는 FIN 패킷에 현재까지 처리된 승인 번호를 담고, 
이 승인 번호 이후에도 데이터가 있다면 더 받는다는 것이다.  수신자가 남은 데이터를 다 보내고 FIN 까지 보내고나면
종료 요청자는 완전히 종료함으로써 조금 더 안전한 연결 종료를 할 수 있다.  


## 4-way Handshake 상태

각각의 과정을 조금 더 자세하게 확인해보자.  

<br>

<img alt="image" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/60910e9b-560d-464d-9aae-8fa2f0867e23" width="604"/>

<br>

1. 클라이언트는 서버와 통신을 종료하기 위해 FIN 플래그를 1로 설정하고 서버로 전송한다. `FIN-WAIT1`
2. FIN 패킷을 받은 서버는 ACK 패킷을 전송하고 애플리케이션의 클로즈 요청을 기다린다. `CLOSE-WAIT`
3. ACK 패킷을 받은 클라이언트는 서버의 FIN 패킷을 기다린다. `FIN-WAIT2`
4. 애플리케이션의 클로즈 요청이 왔다면 클라이언트한테 FIN 패킷을 전송한다. `LAST-ACK`
5. 서버한테 FIN 패킷을 받으면 그에 대한 ACK 패킷을 전송하고 **TIME_WAIT** 상태가 된다. `TIME-WAIT`
6. ACK 패킷을 받은 서버는 **CLOSED** 상태로 이동하고, 커넥션을 삭제한다. `CLOSED`

<br>

한 가지 주의할 점은 클라이언트와 서버의 지칭이다. 반드시 서버만 `CLOSE_WAIT` 상태를 갖는 것이 아니다.  
만약 서버가 먼저 종류하겠다고 클라이언트에게 **FIN**을 보내게 된다면 서버측이 `FIN_WAIT1`를 갖게 된다.  
때문에 명칭을 클라이언트와 서버가 아닌 **Active Close**(또는 Initiator, 기존 클라이언트)와 **Passive Close**(또는 Receiver, 기존 서버)로 
구분하는게 올바르다.  

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
