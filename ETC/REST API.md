# REST API

## REST란?
- REST는 'Representational State Transfer'의 약자
- 월드 와이드 웹 (WWW) 과 같은 분산 하이퍼미디어 시스템을 위한 소프트웨어 개발 아키텍처의 한 형식
- 주고 받는 자원(Resource)의 이름을 규정하고 URI에 명시해 HTTP 메서드(GET, POST, PUT, DELETE)를 통해 해당 자원의 상태를 주고 받는다
- REST는 기본적으로 웹의 기존 기술과 HTTP 프로토콜을 그대로 활용하기 때문에 웹의 장점을 최대한 활용할 수 있는 아키텍처 스타일이다

### REST의 구성요소
- Resource(자원) - URI
    - 모든 자원에 고유한 ID가 존재하고, 이 자원은 Server에 존재한다.
    - 자원을 구별하는 ID는 HTTP URI이다.
    - Client는 URI를 이용해서 자원을 지정하고, 해당 자원의 상태에 대한 조작을 Server에 요청한다.
- Verb(행위) - HTTP method
    - HTTP 프로토콜의 method(POST, GET, DELETE, PUT)를 사용한다.
- Representation(표현)
    - Client가 자원의 상태에 대한 조작을 요청하면 Server는 이에 대해 응답(표현)을 보낸다.
    - 자원은 JSON, XML, TEXT, RSS 등으로 표현되어 나타내질 수 있다. JSON 혹은 XML을 통해 전달하는 것이 일반적이다.

### REST의 특징
(1) 유니폼 인터페이스


- 유니폼 인터페이스는 일관된 인터페이스를 의미한다
- Rest 서버는 Http 표준 전송 규약을 따르므로 어떤 프로그래밍 언어로 만들어 졌느냐와 상관없이 플랫폼 및 기술에 종속되지 않고 타 언어, 플랫폼, 기술 등과 호환해서 사용할 수 있다

 (2) 무상태성

- 무상태성 : 서버에 상태 정보를 따로 보관하거나 관리하지 않는다
- 서버는 클라이언트가 보낸 요청에 대해 세션이나 쿠키 정보를 별도 보관하지 않는다
</br>
    그러므로 서비스는 서버가 불필요한 정보를 관리하지 않으므로 비즈니스 로직의 자유도가 높고 설계가 단순함

 (3)캐시 가능성
- HTTP 표준을 그대로 사용하므로 HTTP의 캐싱 기능을 적용할 수 있다
- 캐싱기능을 사용하기 위해서는 응답과 요청이 모두 캐싱 가능한지(Cacheable) 명시가 필요함

 (4) 레이어 시스템

- REST 서버는 네트워크 상의 여러 계층으로 구성될 수 있다
- 서버의 복잡도와 관계없이 클라이언트는 서버와 연결되는 포인트만 알면 된다

 (5) 클라이언트-서버 아키텍처

- REST 서버는 API를 제공하고 클라이언트는 사용자 정보를 관리하는 구조로 분리해 설계한다
- 클라이언트와 서버 각 서로에 대한 의존성을 낮추는 기능을 한다


## REST API란?
> API는 'Application Programming Interface' 의 약자
</br>
API를 통해 서버 또는 프로그램 사이를 연결할 수 있다

즉, REST API는 REST 아키텍처를 따르는 시스템/애플리케이션 인터페이스이다


- RESTful 하다 = REST 아키텍처를 구현하는 웹 서비스를 표현하는 말

예시 ) 
https://school.com/grade 의 주소로 반의 학생들을 목록으로 받는 요청을 알 수 있다
```
{
"results" : [
    {"idx": 1, "name": "1학년"},
    {"idx": 2, "name": "2학년"},
    {"idx": 3, "name": "3학년"}
    ]
}
```
https://school.com/grade/1 이렇게 idx의 고유번호가 붙는다면 idx가 1인 학년의 정보가 옵니다.


이러한 예시로 RESTful API는 이해하기 쉽고 사용하기 쉬운 REST API를 만드는 것을 목적으로 한다.

만일 성능이 중요한 상황이면 굳이 RESTful한 API를 구현할 필요가 없다.


### REST URI의 설계 규칙

- URI의 마지막에는 '/'를 포함하지 않는다
    - 옳은 예) http://ocalhost.com/product
    - 잘못된 예) http://ocalhost.com/product/

- '/'로 계층 관계를 표현한다


- 언더바를 사용하지 않고 하이픈(-)을 사용한다
    - 하이픈은 리소스의 이름이 길어지면 사용함
    - 옳은 다) http://localhost.com/provider-company-name
    - 잘못된 예) http://ocalhost.com/provider_company _name

- URL에는 행위(동사)가 아닌 결과(명사)를 포함한다
    - 옳은 다) http://localhost.com/product/123
    - 잘못된 예) http://localhost.com/delete-product/123
    - 행위는 HTTP 메서드로 표현할 수 있어야 한다

- URI는 소문자로 작성해야 한다
    - URI 리소스 경로에는 대문자 사용을 피하는 것이 좋다
    - 일부 웹 서버의 운영체제는 리소스 경로 부분의 대소문자를 다른 문자로 인식하기 때문이다

- 파일의 확장자는 URI에 포함하지 않는다
    - 잘못된 예) http://csstudy.tistory.com/restapi/220/photo.jpg
    - HTTP에서 제공하는 Accept 헤더를 사용하는 것이 좋다


### HTTP 메소드 및 상태 코드
 - POST: Create or search
 - GET: Read
 - PUT: Update
 - DELETE: Delete
 - PATCH: Partial update


(1) POST
- 일반적으로 리소스 작업 생성과 연결
- 읽기작업에도 사용하려는 예외도 있다
    - GET 쿼리 문자열은 256자로 제한이 된다. 또한 GET HTTP메소드는 최대 2048자에서 실제 경로의 문자 수를  뺀 것으로 제한된다. 
    </br>
    반면에 POST방식은 이름 값 쌍을 제출하기 위한 URL의 크기에 제한을 받지 않기 때문에 사용하는 경우가 있다
- 성공적인 생성 작업 : 201 Created 상태로 응답

(2) GET
- 일반적으로 리소스 읽기 작업과 연결
- 성공적인 작업 : 200 ok 상태 코드와 연결
- 응답에 데이터가 없는 경우 : 204 No Content

(3) PUT
- 일반적으로 리소스 업데이트 작업과 연결
- 성공적인 업데이트 작업 : 200 OK
- 응답에 데이터가 포함되지 않는 경우 : 204 No Content

(4) DELETE
- 일반적으로 리소스 삭제 작업과 연결
- 성공적인 삭제 : 204 No Content

(5) PATCH
- 부분 업데이트 리소스 작업과 연결
- 성공적인 작업 : 200 OK 상태 코드와 연결

### HTTP Status Code
- 정보 응답(100–199)
- 성공 응답(200–299)
- Redirects (300–399)
- 클라이언트 에러(400–499)
- 서버 에러(500–599)
