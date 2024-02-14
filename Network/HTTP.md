# HTTP

<br/>

HTTP(Hypertext Transfer Protocol)은 텍스트 기반의 통신 규약으로 **인터넷에서 데이터를 주고받을수 있는 프로토콜**입니다.

클라이언트-서버 구조를 따르며 TCP/IP위에서 작동합니다.

<br/>

### 클라이언트 - 서버 구조**

<img width="500px" src="https://github.com/jmxx219/CS-Study/assets/74135929/05aba080-4045-4a4c-9c51-edaec50118960">

클라이언트(e.g 브라우저)가 HTTP메시지를 통해 서버에 요청하면 서버가 요청에 대한 결과를 만들어 클라이언트로 응답하는 구조를 의미합니다..

- 클라이언트와 서버는 개별적인 메시지 교환에 의해 통신
- 요청(requests) : 클라이언트에 의해 전송되는 메시지
- 응답(responses) : 요청에 대해 서버에서 응답으로 전송되는 메시지

<!-- - 특징
    - 간단함
        - HTTP 메시지 사람이 읽을 수 있음 (HTTP/2 이전)
        - HTTP 메시지를 프레임별로 캡슐화해 간결함 유지 (HTTP/2)
    - **확장 가능한 프로토콜**
        - HTTP헤더를 통해 언제든지 새로운 기능 추가 가능
    - 상태는 없지만 세션은 있음
        - HTTP는 상태를 저장하지 않음(stateless)
        - 하지만 쿠키를 통해 상태가 있는 세션을 만들도록 함
    - 어플리케이션 계층의 프로토콜
        - 이론상 신뢰 가능한 전송 프로토콜이란면 무엇이든 사용 가능
        - 실제로는 TCP 혹은 TLS(암호화된 TCP 연결)를 통한 전송

- 약점
    - 암호화 되지 않은 평문 통신으로 도청 가능
    - 통신하려는 상대를 확인하지 않음 -->
<br/>

### TCP/IP
TCP/IP는 OSI 7 Layers에서 Layer3, Layer4를 다루는 프로토콜입니다.

TCP/IP를 사용하겠다는 것은 IP주소 체계를 따르고 TCP의 특성을 이용해 신뢰성을 확보하겠다는것을 의미합니다. ( [OSI Layers 참고](https://github.com/jmxx219/CS-Study/blob/main/Network/OSI%207%EA%B3%84%EC%B8%B5.md), [3-Way Handshake참고](https://github.com/jmxx219/CS-Study/blob/main/Network/3-way%20handshake.md), [4-Way Handshake참고](https://github.com/jmxx219/CS-Study/blob/main/Network/OSI%207%EA%B3%84%EC%B8%B5.md) )

### **HTTP의 흐름(1.1버전기준)**

1. **TCP 연결 열기**
    1. 클라이언트는 새 연결 열기, 기존 열결 재사용, 서버에 대한 여러 TCP연결을 열 수 있음
    
<br/>

2. **HTTP 메시지 전송 (요청)**
    
    ```jsx
    GET / HTTP/1.1
    Host: developer.mozilla.org
    Accept-Language: fr
    ```
    
    요청의 구성
    
    - Method : `GET`
        - 클라이언트가 수행하고자 하는 동작
    - Path : `/`
        - 가져오려는 리소스의 경로
    - Version of the protocal : `HTTP/1.1`
        - HTTP 프로토콜의 버전
    - Headers
        
        `Host: developer.mozilla.org
        Accept-Language: fr` 
        
        - 서버에 대한 추가 정보 전달하는 선택적 헤더들
    
<br/>

3. **서버에 의해 전송된 응답 읽기**
    
    ```jsx
    HTTP/1.1 200 OK
    Date: Sat, 09 Oct 2010 14:28:02 GMT
    Server: Apache
    Last-Modified: Tue, 01 Dec 2009 20:18:22 GMT
    ETag: "51142bc1-7449-479b075b2891b"
    Accept-Ranges: bytes
    Content-Length: 29769
    Content-Type: text/html
    
    <!DOCTYPE html... (here comes the 29769 bytes of the requested web page)
    ```
    
    응답의 구성
    
    - Version of the protocol : `HTTP/1.1`
        - HTTP 프로토콜의 버전
    - Status code : `200`
        - 요청의 성공 여부과 그 이유를 나타내는 상태코드
    - Status message : `OK`
        - 상태코드의 짧은 설명을 나타내는 상태 메시지
    - Headers : `Date: 부터 끝까지`

<br/>
<br/>

## HTTPS
HTTPS(Hypertext Transfer Protocol Secure)은 서버에서 브라우저로 전송되는 정보가 암호화되지 않는 HTTP의 문제점을 SSL을 사용항 암호화한 프로토콜이다.

**HTTPS : HTTP 프로토콜의 보안버전**

<img width="500px" src="https://github.com/jmxx219/CS-Study/assets/74135929/38fe0400-2c5a-4bfb-82e4-ca452249ecc0">

- HTTPS는  HTTP(응용계층 프로토콜)와 TCP(전송 계층 프로토콜) 사이에 SSL/TLS 프로토콜을 거치도록 설계
- 데이터 전송, 수신 시
    - (HTTP/TCP)가 SSL에게 데이터 전송 시 SSL이 데이터를 (암호화/복호화)하여 (TCP/HTTP)에게 전달
- 안전한 데이터의 전달이 브라우저와 웹 서버 사이 전달 구간에서 이뤄짐
    - 보안채널, 전송 보안이라고도 불림
- HTTPS 통신
  
    대칭키 방식과 공개키 방식 함께 사용
    
    - 대칭키 방식
        - 실제 데이터 암호화 시 사용
    - 공개키 방식
        - 서버에 대한 인증과 안전한 대칭키 전달 시 사용

<br/>

**SSL/TLS : 컴퓨터 네트워크 상에서 보안 통신을 제공하기 위해 설계된 프로토콜**

- TLS는 SSL의 취약점들을 개선한 다음 버전의 프로토콜
- 특정 네트워크 계층에 속하는 프로토콜이 아닌, 독자적으로 존재하는 프로토콜
    - 응용계층과 전송계층 사이에 위치하여 보안과정 수행 

<br/>

**SSL/TLS 인증서**
: SSL/TLS 기술을 수행하기 위해 웹 서버에 설치하는 것

- CA에서 검증된 서버에 대해 발급한 인증서
- SSL 인증서에는 주로 서버의 공개키가 들어가 있으며, 이는 나중에 데이터 교환을 위한 대칭키 전달에 사용
- 탑재된 보안기술
    - 대칭키/비대칭키 암호화 방식
    - 통신 대상을 서로가 확인하는 신분확인
    - 믿을 수 있는 SSL 인증서를 위한 디지털 서명 및 인증기관의 확인
    - 안전한 공개키 전달,공유를 위한 프로토콜
    - 암호화된 메시지의 변조 여부 확인하는 메시지 무결성 알고리즘

<br/>
<br/>

## 대칭 키 암호화와 비대칭 키 암호화

HTTPS는 대칭키 암호화, 비대칭키 암호화 방식을 모두 사용하고 있습니다.

<br/>

### [ 대칭 키 암호화 ]

똑같은 개인 키를 송・수신자가 공유하여 정보를 암호화・복호화 하는 것

**대칭 키 : 어떤 정보를 암호화・복호화 할 때 사용하는 키가 동일한 경우**

- 암호화 된 정보의 전달, 확인을 위해선 송・수신자 둘 다 같은 키를 가져야 함
    - 어떤 정보가 대칭 키를 통해 암호화 되었을 시, 똑같은 키를 갖고 있는 사용자가 아닐 경우 해당 정보 확인 불가
- 키의 안전한 교환이 대칭 키 암호화 방식의 가장 중요한 부분

- 장점
    - 암・복호화 과정이 단순
    - 암・복호화 과정의 속도가 빠름
        ⇒ 송신자와 수신자가 동일한 키를 가지고 있기 때문
        
- 단점
    - 송・수신자 간 키 교환이 이뤄져야함
    - 키 교환 과정 중 키 노출 위험
    - 송・수신자가 늘어날수록 관리해야할 키의 증가로 관리의 어려움

<br/>

### [비대칭 키 암호화]  

1개의 쌍으로 구성된 공개키/개인키로 암호화・복호화 하는 것 방식

**비대칭 키 : 어떤 정보를 암호화・복호화 할 때 사용하는 키가 서로 다른 경우**

- 암호화 방식
    - 개인 키 암호화 방식 : 개인 키를 통해 암호화하여, 공개 키로만 복호화 가능
        = 공개키는 모두에게 공개되어 있으므로, 인증된 정보임을 알려 신뢰성을 보장할수 있다.
        - 정보를 생산(송신)한 사람의 신원 정보 필요 시 사용
        - 데이터 제공자의 신원이 보장되는 ‘전자서명’ 등의 공인인증체계의 기본
    
    - 공개 키 암호화 방식 : 공개키를 통해 암호화하여 개인키로만 복호화 가능
        - 정보 자체에 대한 암호화가 필요 시 사용
        - 대칭 키 암호화 방식에서 키 값 교환에 따른 문제를 해결한 방법

- 장점
    - 단 하나의 공개 키를 사용함으로 모든 수신자와 개별 키를 만들 필요 없음
    - 데이터 송신과정 중 키를 탈취 당하더라도 해당 키로 복호화가 불가능하기에 공개키 방식보다 안전
- 단점
    - 암・복호화에 서로 다른 키를 사용하므로 대칭키와 비교해 느린 속도

<br/>
<br/>

## SSL Handshake 과정 (TLS Handshake)

<img src="https://github.com/jmxx219/CS-Study/assets/74135929/fbb28c54-b26a-4d78-a9b3-51676eb3de37"  width="500" height="300"/>

<br/>

기존의 TCP/IP 3Way Handshake 과정에서 보안을 담당하는 SSL과정이 추가되어 보안이 진행된다.

<br/>

### **클라이언트: (1) Client Hello 패킷전송**

클라이언트가 서버에 연결을 시도하며 **Client Hello** 패킷 전송

**Client Hello 패킷 정보**

<img src="https://github.com/jmxx219/CS-Study/assets/74135929/d9bba651-3db8-4a47-9bc1-825b4f92ff9e" width="500" height="300">

<br/>

  - 브라우저가 사용하는 SSL/TLS 버전 정보
  - 브라우저가 지원하는 암호화 방식 모음(cipher suite)
      - 아래 내용을 패키지 형태로 묶어놓은 것
          - 안전한 키 교환, 전달 대상 인증, 암호화 알고리즘, 메시지 무결성 확인 알고리즘 방식
  - 브라우저가 순간적으로 생성한 임의의 난수
  - 이전에 SSL핸드셰이크가 완료된 상태일 경우, 그때 생성된 세션 아이디

<br/>


### **서버: (2) Server Hello 패킷 전송**

클라이언트로부터 패킷을 받아 Cipher Suite중 하나를 선택해 Server Hello에 담아 전송

**Server Hello 패킷정보**

<img src="https://github.com/jmxx219/CS-Study/assets/74135929/bbe89dc2-3bac-4226-8576-c04ec0269014" width="500" height="300">

<br/>

- 브라우저의 암호화 방식 정보 중 서버가 지원하고 선택한 암호화 방식(이전과 달리 하나의 Cipher Suite가 포함됨을 볼수 있다.)
- 서버의 공개캐가 담긴 SSL 인증서
- 서버가 순간적으로 생성한 임의의 난수
- 클라이언트 인증서 요청(선택사항)

<br/>

### **서버: (2) Certificate**

자신의 SSL인증서가 포함된 Certificate패킷 전송

**Certificate 패킷 정보**

- Server가 발행한 공개키(개인키는 서버가 소유)
- 클라이언트는 서버가 보낸 CA(인증기관)의 개인키로 암호화된 SSL인증서를 모두에게 공개된 공개키를 사용하여 복호화한다.
  - 정상적 복호화 → CA발급 증명
  - 등록된 CA 아님 or 가짜 인증서 → 브라우저에게 경고 보냄

<br/>

### **서버: (2) Server Key Exchange / ServerHello Done**

서버의 공개키가 SSL인증서 내부에 없을경우, 서버가 직접 전달한다는 뜻이다. 공개키가 SSL인증서 내부에 있다면 해당 패킷은 생략된다.

<br/>

### **클라이언트: (3) Client Key Exchange**

클라이언트는 데이터 암호화에 사용할 대칭키를 생성한 후 SSL인증서 내부에서 추출한 서버의 공개키를 이용해 암호화한 후 서버에게 전달한다. 여기서 전달된 대칭키가 SSL HandShake의 목적이자 가장 중요한 수단인 데이터를 실제로 암호화할 대칭키다. 이제 키를 통해 클라이언트와 서버가 실제로 교화하고자 하는 데이터를 암호화한다.

<br/>

### **클라이언트: (3) ChangeCipherSpec / Finished**

ChangeCipherSpec패킷은 클라이언트와 서버 모두가 서로에게 보내는 패킷으로 교환할 정보를 모두 교환한 뒤 통신할 준비가 다 되었음을 알리는 패킷이다. Finished패킷을 보내어 SSL Handshake를 종료하게 된다.

<br/>

### **서버: (4) ChangeCipherSpec / Finished**

위와 동일

<br/>
<br/>

---
### Ref
https://steady-coding.tistory.com/512
