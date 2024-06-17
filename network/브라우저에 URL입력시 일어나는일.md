## 브라우저에 URL입력시 네트워크 상 일어나는 일

```
브라우저에 www.github.com 입력시 일어나는 일
```
        
### **1. 브라우저에 URL 입력**

- 핸들링 인풋 진행
    - 브라우저 프로세스 안의 ui스레드가 입력된 텍스트 값 판단
    - `Search Query` 일 경우, 검색이 되도록 함(크롬 브라우저 → 구글 검색 엔진)
    - `URL` 일 경우, URL값을 네트워크 스레드에게 전달할 준비를 함
- URI, URL, URN

    <img width="555" alt="스크린샷 2023-10-10 오후 1 52 50" src="https://github.com/jmxx219/CS-Study/assets/50795805/63e432db-6730-45bd-a69c-b11cb508e600">

<br>

#### URI(Uniform Resource Identifier, 통합 자원 식별자)
- 인터넷 자원을 식별할 수 있는, 하나의 `리소스`를 가르키는 문자열
    - 이때 리소스는 HTTP와 같은 프로토콜에서 요청한 대상
- URI의 하위개념으로 `URL`, `URN` 존재

<br>

#### URL(Uniform Resource Locator)
- 네트워크 상에서 웹 페이지, 이미지, 동영상 등의 파일이 `위치`한 정보를 나타냄
- FTP, SMTP등 다른 프로토콜에서 사용 가능 

<br>

#### URN(Uniform Resource Name)
- `URI`의 표준 포맷중 하나로 이름으로 리소스를 특정하는 URI
- 리소스의 name을 가리키는데 사용
    - http와같은 프로토콜을 제외함
- 특징
    - 리소스를 영구적이고 유일하게 식별할 수 있는 URI
    - 리소스 접근방법과 웹 상의 위치가 표기되지 않음
    - 리소스 자체에 부여된 영구적이고 유일한 이름이며 변하지 않음
    - 실제 자원을 찾기 위해선 URN을 URL로 변환해 이용

<br>

> **URL과 URI의 차이점**
> - URL은 어떻게 리소스를 얻을 것이고 어디에서 가져와야하는지 명시하는 URI
> - URN은 리소스를 어떻게 접근할 것인지 명시하지 않고 경로와 리소스 자체를 특정하는 것을 목표로하는 URI

<br>
<br>


### 2. 입력한 URL의 IP 주소를 찾기 위해 DNS 이용

- 인터넷의 모든 URL에는 고유한 [IP 주소](https://github.com/jmxx219/CS-Study/blob/main/network/IP%20%EC%A3%BC%EC%86%8C.md)가 할당 됨
- [DNS](https://github.com/jmxx219/CS-Study/blob/main/network/DNS.md#dns%EB%9E%80)를 통해 변환된 도메인 이름의 IP 주소를 찾음

<br>

#### [DNS 작동 방식 참고](https://github.com/jmxx219/CS-Study/blob/main/network/DNS.md#dns-%EC%9E%91%EB%8F%99-%EB%B0%A9%EC%8B%9D)

1. 캐시의 DNS 기록 확인
2. 캐시에 없을 경우 DNS 쿼리 진행

<br>
<br>

### 3. 브라우저와 서버의 연결 (TCP connection)
- 인터넷 프로토콜을 이용해 브라우저와 서버 연결
- 웹 사이트의 경우 HTTP요청에 일반적으로 **TCP연결 진행**
    - [3-way handshake](https://github.com/jmxx219/CS-Study/blob/main/network/3-way%20handshake.md)을 통한 TCP connection 완성

<br>
<br>

### 4. 브라우저가 웹 서버에 HTTP 요청
- TCP연결 후, 데이터 전송

<br>
<br>

### 5. 요청에 대한 서버의 응답
- 서버는 요청을 처리하고 response 생성
- 서버가 [HTTP response](https://github.com/jmxx219/CS-Study/blob/main/network/HTTP%20응답코드.md)를 보냄

<br>
<br>

### 6. 브라우저는 HTML content를 보여줌
- 브라우저는 HTML content를 단계적으로 보여줌
- HTML 스켈레톤 렌더링 → HTML tag체크 후 추가 필요 요소(이미지, CSS, js파일..) Get 요청 → 정적파일 캐싱 → www.github.com 화면 보임

<br>
<br>
