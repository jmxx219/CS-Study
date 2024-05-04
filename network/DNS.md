# DNS(Domain Name System)

<br/>

### DNS가 생겨난 이유
 
- 인터넷 초기에는 SNI-NIC라는 컴퓨터가 hosts.txt 파일을 가지고 있었고, 클라이언트는 FTP를 이용해 해당 컴퓨터에 접근해서 hosts.txt을 다운로드해서 사용하였음
    - `hosts file`: IP 주소와 hostname을 매칭시켜놓은 텍스트 파일로, 인터넷 초기에는 이 파일을 사용하여 IP 주소를 찾았음
- 이 `hosts file`은 서버가 IP 주소를 바꿀 경우, 업데이트가 늦을 수 있고 이름이 중복 될수도 있다는 문제점이 존재
    - 해결 방법
        - 호스트 이름에 계층 구조를 사용
        - 분산된 데이터베이스 이용(DNS)

<br/>

## DNS란

- 개념
    - 인터넷 전화번호부와 같음
    - 사람이 인식하기 쉬운 도메인 이름을 서버가 인식하는 IP 주소로 변환하는 분산 데이터베이스 시스템
- 필요성
    - 웹 브라우저는 인터넷 프로토콜(IP) 주소를 통해 상호작용하는데, 이 [IP 주소](https://github.com/jmxx219/CS-Study/blob/main/network/IP%20%EC%A3%BC%EC%86%8C.md)를 사람이 기억하기에는 무리가 있음
    - 그래서 DNS는 `naver.com`과 같은 `도메인 이름`을 `IP 주소`로 변환해 브라우저가 인터넷 자원을 로드할 수 있도록 도와줌
        - `도메인 이름`: 체계적인 문자로 표현한 호스트 이름
- DNS는 여러 개의 DNS 서버(혹은 네임 서버)들로 구성된 분산 시스템
    - OSI 7계층 중에 애플리케이션 계층에서 동작하는 프로토콜
    - 기본적으로 `UDP/53`를 기반으로 사용하지만, 특수한 상황에서는 `TCP/53`도 사용함


<br/>

#### DNS의 전송 프로토콜
- UDP port 53
    - 일반 DNS 질의/응답에 사용
- TCP port 53
    - 영역 전송(Zone Transfer) 시, 사용
        - 안정성을 위해 두 개 이상의 DNS 서버를 사용할 경우, Master(기본 서버)와 Slave(보조 서버)간의 동기화가 필요함
        - 이때 기본 서버의 정보를 이용해 보조 서버의 데이터를 최신 상태로 업데이트 하는 작업을 영역 전송이라고 함
    - 용량이 큰 경우(512 Bytes를 초과)에 사용


<br/>

#### DNS에서 UDP를 사용하는 이유

- 빠른 속도(연결의 시작과 끝 설정이 없음)
    - DNS 질의는 신뢰성보다는 속도(빠른 응답)가 더 중요한 서비스이고, DNS가 전송하는 데이터 패킷 사이즈는 매우 작음
    - UDP는 512 bytes를 넘어가지 않는 패킷만 전송이 가능하고, 오버헤드가 없어서 속도가 빠름
- 연결상태 유지 불필요
    - DNS 서버는 도메인네임을 IP로 변경해주는 서비스를 제공하기 때문에 항상 많은 클라이언트를 수용해야 함
    - UDP는 어떤 정보도 기록하지 않고, 유지할 필요가 없어서 TCP보다 UDP에서 동작할 때 더 많은 클라이언트를 수용할 수 있음


<br/>
<br/>

## DNS의 구성요소

- 도메인 네임 스페이스(Domain Name Space): DNS가 저장 관리하는 계층적 구조
    - DNS는 분산시스템으로 도메인 이름을 분산하여 저장하는데 계층적 구조로 저장하고 관리함
- 네임 서버(Name Server): 권한 있는 DNS 서버
    - 해당 도메인의 IP 주소를 찾는 역할을 담당
- 리졸버(Resolver): 권한 없는 DNS 서버
    - 클라이언트의 요청을 네임 서버로 전달하고, 찾은 정보를 클라이언트에게 제공하는 기능을 담당

<br/>

### 도메인 네임 스페이스(도메인 구조)

<img src="https://www.kisa.or.kr/resource/kor/images/sub/ic_compay_04_03_01.png" width="700" height="360"/>

- 인터넷 상에서 사용되는 도메인은 전 세계적으로 고유하게 존재하는 이름
    - 정해진 규칙 및 체계에 따라야 하고, 임의로 변경되거나 생성될 수 없음
    - 도메인의 체계적인 분류와 관리를 위해 도메인 이름은 몇 개의 짧은 영문자를 `.`으로 연결한 계층 구조를 가지고 있음
    - 트리에 연결된 호스트는 자원 레코드로 표현함
        - 자원 레코드: 네임서버가 관리하는 정보를 표현하는 자료구조
- 도메인 구조는 역트리 구조라고 함
    1. 루트 도메인: 인터넷 도메인의 시작점
    2. 1단계 도메인: 최상위 도메인으로, `TLD(Top Level Domain)`라고 함
        - 국가명을 나타내는 국가최상위도메인(`ccTLD, country code TLD`)과 일반적으로 사용되는 일반최상위도메인(`gTLD, generic TLD`)으로 구분됨
    3. 2단계 도메인: 조직의 속성을 구분하는 co(영리 기업), go(정부기관) 등이 있음
    4. 3단계 도메인: 조직이나 서비스의 이름을 나타내는 도메인 이름으로, 사용자가 원하는 문자열을 사용할 수 있음
    5. 호스트: 마지막에 위치함
- 도메인을 표기할 때는 낮은 단계부터 최상위 도메인이 가장 뒤에 나타남
    - ex) `www.korea.go.kr`, `www.naver.com`

<br/>

### 네임 서버(DNS 서버)

- 도메인은 도메인 계층 구조를 반영한 DNS 서버(네임 서버)에 저장 및 관리됨
    - 각 네임 서버는 도메인 계층의 일부 영역을 담당하고, 그 영역에 속한 도메인을 관리함
    - 상위 계층의 네임 서버는 하위 계층의 도메인에 대한 정보를 관리하고, 하위 계층 네임 서버의 IP주소를 가지고 있음
    - 최상위 계층인 루트 네임 서버의 IP 주소는 모든 DNS 서버가 등록하여 관리함
- DNS 서버는 도메인 네임 스페이스를 위한 `권한 있는 DNS 서버`와 `권한이 없는 DNS 서버`로 구분됨
    - 권한 있는 DNS 서버: Root DNS 서버, TLD DNS 서버, Authoritative DNS 서버
        - IP 주소와 도메인 이름을 매핑함
    - 권한 없는 DNS 서버: Resolver 서버
        - 질의를 통해 IP 주소를 알아내거나 캐시함

<br/>

**네임 서버 종류**(DNS 계층은 크게 3가지로 나눌 수 있음)
- `Root DNS 서버`
    - 최상위 네임 서버로, ICANN(DNS 총괄 기구)에서 관리함
    - 리졸버부터 발생한 DNS 요청에 대하여 적절한 TLD DNS 서버의 IP 주소를 제공하는 역할
- `TLD DNS 서버`
    - `.com`, `.net`, `.kr`과 같은 top-level domain 별로 존재하는 DNS 서버로
    - 도메인 등록 기관에서 관리함
    - Authoritative DNS 서버의 IP 주소를 제공하는 역할
- `Authoritative DNS 서버`
    - 실제로 우리가 원하는 도메인에 대한 IP 주소를 매핑하는 서버로, 개인 도메인과 IP 주소의 관계가 저장됨
    - 일반적으로 도메인/호스팅 업체의 네임 서버를 말함
- `권한 없는 DNS 서버`(Local DNS 서버, ISP DNS 서버, Resolver 서버)
    - 일반적으로 DNS 계층에는 포함되지 않지만, DNS가 동작하는데 중요한 역할을 함
    - 클라이언트가 도메인 이름에 대한 IP 주소를 찾고자할 때 가장 먼저 찾아가는 DNS 서버


<br/>

### 리졸버(Resolver)

- Resolver = Recursive DNS 서버 = Local DNS Server(ISP)
- 도메인에 대한 질의를 받은 리졸버는 설정된 DNS 서버로 DNS Query(질의)를 전달하고, DNS 서버로부터 정보(도메인 이름, IP 주소)를 응답 받아 클라이언트에게 제공하는 기능을 수행함


<br/>
<br/>

## DNS 작동 방식

0. DNS 서버로 DNS 쿼리를 보내기 전(브라우저에 URL 입력 직후)
    1. `Local(hosts)`
        - 네트워크를 타기 전에 운영체제는 로컬(hosts 파일)에서 먼저 찾는다.
            - 이 파일은 해당 경로(`C:\Windows\System32\drivers\etc\hosts`)로 직접 열어볼 수 있음
        - DNS 검색 시, hosts 파일을 우선적으로 검색한다.
    3. `DNS cache table`
        - hosts 파일에 정보가 없다면, 운영체제는 DNS 캐시 테이블에서 찾는다.
            - 명령 프롬프트 창에서 `ipconfig/displaydns`를 입력해서 볼 수 있음
    4. `DNS server`
        - 이후에도 IP 정보를 못찾았다면, 운영체제는 로컬 DNS 서버로 질의를 요청한다.


<img src="https://i0.wp.com/hanamon.kr/wp-content/uploads/2022/04/DNS-%E1%84%83%E1%85%A9%E1%86%BC%E1%84%8C%E1%85%A1%E1%86%A8%E1%84%80%E1%85%AA%E1%84%8C%E1%85%A5%E1%86%BC.png?resize=1080%2C485&ssl=1" width="700" height="360"/>

1. 클라이언트는 `google.com` 도메인에 대한 DNS 쿼리 메시지를 로컬 DNS 서버에 보낸다.
    - 이때 로컬 DNS 서버에 `google.com` 도메인에 대한 IP 주소가 캐시되어있다면 바로 클라이언트에세 요청한 도메인에 대한 IP 주소를 반환한다.
2. 로컬 DNS 서버는 DNS 쿼리(Query) 메시지를 Root DNS 서버에 보낸다.
3. Root DNS 서버는 쿼리 메시지에 있는 도메인을 분석하여 해당 도메인의 TLD에 대한 TLD DNS 서버의 IP 주소를 반환한다.
    -  일반적으로 하나의 TLD에 대응되는 TLD DNS 서버가 여러 개 있기 때문에, 대응되는 IP 주소 목록을 보낸다.
4. 로컬 DNS 서버는 TLD DNS 서버의 IP 주소 목록 중 하나를 골라 해당 TLD DNS 서버에 쿼리 메시지를 보낸다.
5. TLD DNS 서버는 쿼리 메시지에 있는 도메인을 보고 적잘한 Authoritative DNS 서버의 IP 주소를 반환함
    - 이때 `google.com`의 IP 주소가 반환됨
6. 로컬 DNS 서버는 TLD DNS 서버로부터 받은 Authoritative DNS 서버로 쿼리 메시지를 보낸다.
7. Authoritative DNS 서버는 도메인에 맞는 IP 주소를 반환한다.
8. 로컬 DNS 서버는 클라이언트가 요청한 도메인에 대한 IP 주소를 반환한다.


<br/>

### DNS Query

- 사용자가 도메인을 입력하고 IP 주소를 얻기 위해 DNS 서버에 보내는 요청을 뜻함 
- DNS 클라이언트와 DNS 서버는 DNS 쿼리를 교환함
- DNS 쿼리는 `Recursive(재귀적)` 또는 `Iterative(반복적)`으로 구분함

<br/>

**종류**
- Recursive Query (재귀적 질의)
    - 결과물(IP) 주소를 돌려주는 작업
    - Recursive 서버는 Recursive 쿼리를 클라이언트에게 돌려주는 역할
        - Recursive 쿼리를 받은 Recursive 서버(Resolver)는 권한 있는 네임 서버로 Iterative 쿼리를 보내서 IP 주소를 찾고 결과를 반환함
- Iterative Query (반복적 질의)
    - Recursive DNS 서버가 다른 DNS 서버에게 쿼리를 보내어 응답을 요청하는 작업
    - Recursive 서버가 권한 있는 네임 서버들에게 반복적으로 쿼리를 보내서 IP 주소를 찾음
    - 만약 Recursive 서버에 이미 IP 주소가 캐시 되어있다면 해당 과정은 건너뜀



<br/>


### DNS 레코드

- 도메인 이름에 대한 IP 주소 매핑 정보를 구성하고 제어할 수 있는 DNS 데이터베이스에 저장된 특정 리소스 레코드
    - `(Name, Value, Type, TTL)` 형식으로 구성되어 있음
    - TTL(Time To Live) 옵션값: DNS 서버나 사용자 PC의 캐시(메모리)에 저장되는 시간
- 레코드 종류(Type에 따라 나눠짐)

|레코드|Name|Value|Type|TTL|
|---|---|---|---|---|
|A|example.com|100.100.123.1|A|14400|
|AAAA|example.com|0000:8a2e:0370:7334|AAAA|14400|
|CNAME|hello.example.com|example.com|CNAME|14400|
|NS|.com|dns.com|NS|14400|

  - A 레코드
    - DNS에 저장되는 정보의 타입으로 도메인 주소와 서버의 IP 주소가 직접 매핑시키는 방법
    - A는 `Address`를 나타내며, `Name`은 도메인 이름, `Value`는 해당 도메인의 IPv4 주소를 나타냄
  - AAAA 레코드
    - Type A의 IPv6 버전으로, IPv6의 주소를 매핑함
  - CNAME 레코드
    - 어떤 도메인이 다른 도메인의 별칭(alias)인 경우 CNAME 타입 레코드가 사용됨
    - 도메인 주소를 또 다른 도메인 주소로 이중 매핑 시키는 형태
  - NS 레코드
    - 네임 서버 레코드로 도메인에 대한 네임서버의 권한을 누가 관리하고 있는지 알려주는 레코드
    - 어떤 도메인에 대한 처리를 다른 도메인 네임 서버에게 위임하는 기능을 가짐
  - 기타 등등




<br/>
<br/>

---

### 참고

- [한국인터넷진흥원 - 인터넷 주소 관리](https://www.kisa.or.kr/1041103)
- [DNS란? (도메인 네임 시스템 개념부터 작동 방식까지)](https://hanamon.kr/dns%EB%9E%80-%EB%8F%84%EB%A9%94%EC%9D%B8-%EB%84%A4%EC%9E%84-%EC%8B%9C%EC%8A%A4%ED%85%9C-%EA%B0%9C%EB%85%90%EB%B6%80%ED%84%B0-%EC%9E%91%EB%8F%99-%EB%B0%A9%EC%8B%9D%EA%B9%8C%EC%A7%80/)
