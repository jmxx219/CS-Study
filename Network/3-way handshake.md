# 3-way handshake


TCP는 애플리케이션 프로세스가 데이터를 다른 프로세스에게 보내기 전에, 두 프로세스가 서로 핸드세이크를 먼저 한다. (연결 지향성)

<br>


- TCP/IP 프로토콜을 이용해서 통신하는 응용프로그램이 데이터를 전송하기 전에 정확한 전송을 보장하기 위해 상대방 컴퓨터와 사전에 세션을 수립하는 과정
- 신뢰성을 위해 3번의 핸드쉐이킹을 거쳐 연결을 맺는 것

![1](https://github.com/ensk26/CS-Study/assets/70767115/eb216165-4d1e-4e36-8366-47df1a270209)

<br>

## State

- CLOSED: 포트가 닫힌 상태
- LISTEN: 포트가 열린 상태로 연결 요청 대기 중
- SYN_RECV: SYNC 요청을 받고 상대방의 응답을 기다리는 중
- ESTABLISHED: 포트 연결 상태

<br>

## Flag

- SYN:
    - 연결 설정, Sequence Number를 랜덤으로 설정하여 세션을 연결하는 데 사용하며, 초기에 Sequence Number를 전송한다.
    - Connection을 생성할때 사용
- ACK:
    - 응답 확인, 패킷을 받았다는 것을 의미하는 flag
    - Acknowledgement Number 필드가 유효한지를 나타낸다.
    - 양단 프로세스가 쉬지 않고 데이터를 전송한다고 가정하면 최초 연결 설정 과정에서 전송되는 첫 번째 세그먼트를 제외한 모든 세그먼트의 ACK 비트는 1로 지정된다고 생각할 수 있다.

<br>

## Sequence Number:

- Squence Number는 TCP 세그먼트의 연속된 데이터 번호

<br>

**ISN(Initial Sequence Number)이 0부터 시작하지 않고 난수부터 시작하는지?**

- connect를 맺을 때 사용하는 포트는 유한 범위 내에서 사용하고 시간이 지남에 따라 재사용된다.
- 그래서 두 통신 호스트가 과거에 사용된 포트 번호 쌍을 사용하는 가능성이 존재한다.
- Server 측에서 패킷의 SYN을 보고 패킷을 구분하게 되므 난수로 설정한다.
- Sequence Number가 노출되면 공격자가 위조 패킷을 보낼 수 있어 보안을 위해 랜덤 값으로 설정한다.

<br>

## Acknowledgement Number:

- 상대방으로부터 받아야 하는 다음 TCP 세그먼트 데이터 번호

<br>

## 동작방식

1. Client → SYN → Server
    1. Client가 Server에게 접속을 요청하는 SYN 플래그를 보낸다.
    
2. Server → SYN+ACK → Client
    1. Server는 Listen상태에서 SYN이 들어온 것을 확인한다.
    2.  SYN_RECV 상태로 바뀌어 SYN + ACK 플래그를 Client에게 전송한다. 
    3. Server는 다시 ACK 플래그를 받기 위해 대기 상태로 변경된다.
    
3. Client → ACK → Server
    1. SYN + ACK 상태를 확인한 Client는 서버에서 ACK를 보내고 연결 성립이 된다.

<br>

## 문제점

1. ISN 동기화가 이루어지지 않는다 (Client가 Server의 ISN을 알 수 없다).
    - Server가 전송한 Segment의 순서를 구분할 수 없다.
        - Pkt Loss나 Delay를 탐지할 수 없다.
        - 작업 순서가 달라져 오류가 발생한다.
        - 작업의 중복 수행이 발생할 수 있다.
2. half open connection 상황이 발생할 수 있다.
    - 한 쪽 Host만 연결된 상태를 의미한다.
    - 연결된 Host는 계속 자원을 낭비하게 된다.
    - SYN Flooding(DoS의 일부)에 취약하다.

<br>

## 2-Way Handshaking 안되는 이유

마지막 3번째 동작 (Client → ACK → Server)이 필요한 이유

- Client가 Server에게 연결 요청이 딜레이가 많이 되어 다시 연결 요청을 하는 상황이면, Server는 과거의 연결 요청에 대한 Sequence Number로 응답한다.
- Client는 잘못된 Sequence Number의 패킷이 왔기 때문에 해당 패킷은 버린다.
- 두 방향 핸드쉐이크만 있다면, Server는 Client가 응답을 제대로 받았는지 확인할 수 없으므로 마지막 3번째 동작이 필요하다.

→ TCP의 connection은 양방향성 connection이다. 양쪽에서 연결이 제대로 되었는지 알 수 없으므로, 신뢰성을 보장할 수 없다. 그래서 3-Way Handshake로 서로 연결을 보장해 줘야 한다.


<br>

## SYN Flooding

- SYN Flood, 또는 TCP SYN Flood는 DoS (Denial-of-Service) 또는 DDoS(Distributed Denial-of-Service) 공격의 일종으로, 대량의 SYN request를 보내 열린 Connection으로 서버를 과부한다.
- Server의 SYN-ACK에 응답하지 않고, Flood Attack에서 SYN 요청만 마구잡이로 보내는 네트워크 계층 공격
- 많은 수의 열린 TCP Connection으로 인해 서버의 리소스가 과도하게 소모되어 정상 트래픽의 처리를 어렵게 하면서 (열린 Connection은 서버의 TCP Connection Table에서 관리되고 서버의 Connection Table을 모두 채우게 되면서), 새로운 Connection을 생성할 수 없다.
- 그리고 이미 연결된 사용자의 Connection의 경우에도 서버가 올바르게 작동하기 어렵게 된다.

<br>

## **Simultaneous Open (동시 개방)**

- 양쪽 프로세스 모두가 능동적 개방을 요구할 때 발생
- 서로의 지역 포트를 알고 있는 두 피어 사이에 일어난다.
- 이 경우 양쪽은 상대방에게 SYN+ACK 세그먼트를 전달해 하나의 단일 연결을 상호 간에 설정한다.

![2](https://github.com/ensk26/CS-Study/assets/70767115/31830cc9-f33e-4f7b-a825-4e24658505da)


1. 각자 서로 SYN Segment를 보낸다. - SYN-SENT
2. SYN Segment를 받으면 ACK Segment를 보낸다. - SYN-RCVD
3. ACK Segment가 도착하면 각자 Established 된다.

<br>

https://jeongkyun-it.tistory.com/180

https://studyandwrite.tistory.com/404

[https://velog.io/@averycode/네트워크-TCPUDP와-3-Way-Handshake4-Way-Handshake](https://velog.io/@averycode/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-TCPUDP%EC%99%80-3-Way-Handshake4-Way-Handshake)

https://www.f5.com/ko_kr/glossary/syn-flood-attack

https://hojunking.tistory.com/106

https://sostarzia.tistory.com/133
