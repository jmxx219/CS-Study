# OSI 7계층



## OSI 7계층이란?

OSI 7 계층은 네트워크 통신을 구성하는 요소들을 7개의 계층으로 표준화한 것이다.

통신의 과정을 단계별로 파악할수 있기 때문에, 오류가 생긴 부분만을 접근할수 있다는 장점이 있습니다.


![img](https://blog.kakaocdn.net/dn/bqbCLk/btryyf3MHwI/E20STGOkRn5Y1Lkc88oDP1/img.png)

| 계층                         | **설명**                                                     |
| ---------------------------- | ------------------------------------------------------------ |
| **7계층 (응용 계층)**        | - 사용자와 직접 상호작용하는 응용 프로그램들이 포함된 계층<br />- HTTP, FTP, SMTP, Telnet 등과 같은 프로토콜이 대표적이다. |
| 6계층 (표현 계층)            | -  데이터의 형식을 정의하는 계층 (데이터 변환, 압축, 암호화 등) |
| 5계층(세션 계층)             | - 통신 시스템 사용자 간의 연결을 유지 및 설정하는 계층 (API, Socket)<br />- 세션 설정, 유지, 종료, 전송 중단 시 복구 제공<br />- **전이중 통신과 반이중 통신** 방식이 존재한다. |
| **4계층 (전송 계층)**        | - 종단 간 신뢰성 있고 정확한 데이터 전송을 담당하는 계층<br />- 송신자와 수신자 간의 신뢰성있고 효율적인 데이터를 전송하기 위하여 오류검출 및 복구, 흐름제어와 중복검사 등을 수행한다. <br />- 데이터 전송을 위해 **포트 번호**를 사용하며 대표적인 프로토콜은 **TCP와 UDP**가 있다. <br> - 전송 단위 : 세그먼트|
| **3계층 (네트워크 계층)**    | - IP를 기반으로 데이터(**패킷**) 전송 경로를 결정하는 계층(라우팅) <br />- 라우팅, 흐름제어, 세그멘테이션, 오류 제어 등을 수행한다. <br> - 목적지까지 안전하고 빠르게 데이터를 보내는 기능으로 최적의 경로를 설정해야한다. <br> - 전송 단위 : 패킷|
| **2계층 (데이터 링크 계층)** | - **MAC 주소**를 사용하여 통신한다. <br />- 데이터의 물리적인 전송과 에러 검출, 흐름 제어를 담당하는 계층 (브리지, 스위치 ) <br> - 전송 단위 : Frame|
| 1계층 (물리 계층)            | - 데이터를 전기 신호로 바꾸어주는 계층 (통신 케이블, 리피터, 허브) <br> - 전송 단위 : Bit|


<br/>

## OSI상의 통신 흐름

![image](https://github.com/goormthon-Univ/2024_BEOTKKOTTHON_TEAM_3_BE/assets/74135929/4e049f29-6d6f-49d0-a7af-8226f51244dd)
1. 발신 측에서 응용 계층(7 Layer)부터 각 계층마다 헤더를 붙여 캡슐화를 진행
2. 수신 측에서 물리 계층(1 Layer)부터 올라가며 헤더를 뗴어 해당하는 데이터를 추출하는 디캡슐레이션을 진행
    
    ex) 데이터가 목적지로 이동할 때, 네트워크 계층(3 Layer)에서 IP헤더에 있는 프로토콜 정보를 이용해 데이터가 TCP인지 UDP인지 식별후 그에 따른 처리를 전송 계층(4 Layer)에서 수행한다.


## TCP/IP 4계층

- 네트워크 전송시 데이터 표준을 정리한것이 OSI 7계층이라면, 실제 해당 이론을 사용하는 표준은 TCP/IP 4계층이다.
- OSI 7계층을 4-5계층으로 분류하여 적용한 것

<br>


![img](https://velog.velcdn.com/images%2Fcgotjh%2Fpost%2F52907c8c-c149-4943-ad21-3996f44f912f%2F995EFF355B74179035.jpg)

| 계층                             | **설명**                                                     |
| -------------------------------- | ------------------------------------------------------------ |
| 4계층 (응용 계층)                | - 사용자와 가장 가까운 계층으로, 사용자-소프트웨어 간 소통을 담당하는 계층<br />- 대표적인 프로토콜 : HTTP, FTP, DNS 등<br /> |
| **3계층 (전송 계층)**            | - 통신 노드 간 신뢰성 있는 데이터 전송을 보장하는 계층<br />- **IP와 포트번호**를 이용하여 프로세스와 통신<br />- 대표적인 프로토콜 : TCP, UDP<br />- 데이터 단위 : 데이터 그램 |
| **2계층 (인터넷 계층)**          | - 통신 노드 간의 **IP 패킷 전송** 및 **라우팅**을 담당하여 서로 다른 네트워크 간의 통신을 가능하게 한다. <br />- 대표적인 프로토콜 : IP, ARP, RARP<br />- 데이터 단위는 **패킷** |
| 1계층 (네트워크 인터페이스 계층) | - MAC주소를 활용하여 물리적으로 장비간 데이터 전송을 담당하는 계층 (이더넷, 와이파이 등)<br />- 데이터 단위는 **프레임** |

