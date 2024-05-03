# REST, REST API, RESTful

<br/>

## REST(Representational State Transfer)

- 정의
  - 자원의 이름(자원의 표현)으로 구분하여 해당 자원의 상태를 주고 받는 모든 것을 의미함
    - 즉, 자원의 표현에 의한 상태 전달을 의미함
    - ex) DB의 학생 정보가 자원일 때 `students`를 자원의 표현으로 정하고, 학생 정보가 요청되는 시점에 자원의 상태(정보)를 전달함
  - 월드 와이드 웹(WWW)과 같은 분산 하이퍼미디어 시스템을 위한 소프트웨어 개발 아키텍처의 한 형식
    - REST는 네트워크 상에서 클라이언트와 서버 사이의 통신 방식 중 하나임
    - 기본적으로 웹의 기존 기술과 [HTTP 프로토콜](https://github.com/jmxx219/CS-Study/blob/main/network/HTTP.md)을 그대로 활용하기 때문에 웹의 장점을 최대한 활용할 수 있는 아키텍처 스타일
- 구체적인 개념
  - 즉, REST란 HTTP URI를 통해 자원을 명시하고, [HTTP Method](https://github.com/jmxx219/CS-Study/blob/main/network/HTTP%20Method.md)를 통해 해당 자원(URI)에 대한 CRUD Operation을 적용하는 것을 의미함
  - CRUD Operation: Create, Read, Update, Delete, Head


<br/>

### REST의 구성요소
- `Resource(자원)` - `HTTP URI`
    - 모든 자원에는 고유한 ID(HTTP URI)가 존재하고, 이 자원은 Server에 존재함
      - 자원을 구별하는 ID는 HTTP URL임
    - Client는 URI를 이용해서 자원을 지정하고, 해당 자원의 상태에 대한 조작을 Server에 요청함
    - [URI와 URL의 차이점 참고](https://github.com/jmxx219/CS-Study/blob/main/network/%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80%EC%97%90%20URL%EC%9E%85%EB%A0%A5%EC%8B%9C%20%EC%9D%BC%EC%96%B4%EB%82%98%EB%8A%94%EC%9D%BC.md#1-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80%EC%97%90-url-%EC%9E%85%EB%A0%A5)
- `Verb(행위)` - `HTTP method`
    - HTTP 프로토콜의 Method(POST, GET, DELETE, PUT, PATCH)를 사용함
- `Representation(표현)` - `HTTP Message Pay Load`
    - Client가 자원의 상태에 대한 조작을 요청하면 Server는 이에 대해 응답(표현)을 보냄
    - 자원은 JSON, XML, TEXT, RSS 등으로 표현되어 나타낼 수 있고, JSON 혹은 XML을 통해 전달하는 것이 일반적


<br/>

### REST의 특징

1. Uniform Interface(인터페이스 일관성)
   - URI로 지정한 자원의 조작을 통일되고 한정적인 인터페이스로 수행함
   - 특정 언어나 플랫폼 및 기술에 종속되지 않고 HTTP 표준 프로토콜에 따르는 모든 플랫폼에서 사용이 가능함
2. Stateless(무상태)
   - 서버는 클라이언트가 보낸 요청에 대해 세션이나 쿠키 정보를 별도 보관하지 않음
     - 따라서 서비스는 서버가 불필요한 정보를 관리하지 않으므로 비즈니스 로직의 자유도가 높고 설계가 단순함
   - 서버는 각각의 요청을 완전히 별개의 것으로 인식하고 처리함
     - 각 API 서버는 Client의 요청만을 단순히 처리하고, 이전 요청이 다음 요청의 처리에 연관되어서는 안됨
     - 서버의 처리 방식에 일관성을 부여하고 부담이 줄어듦
3. Cacheable(캐시 처리 가능)
   - HTTP 표준을 그대로 사용하므로 HTTP의 캐싱 기능을 적용할 수 있음
     - 캐싱 기능을 사용하기 위해서는 응답과 요청이 모두 캐싱 가능한지(Cacheable) 명시가 필요함
   - 대량의 요청을 효율적으로 처리하기 위해서는 캐시가 요구 됨
4. Layered System(계층화)
   - REST 서버는 네트워크 상의 여러 계층으로 구성될 수 있음
     - API Server는 순수 비즈니스 로직을 수행하고 그 앞단에 보안이나 로드밸런싱, 암호화, 사용자 인증 등을 추가하여 구조상의 유연성을 줄 수 있음
   - 서버의 복잡도와 관계없이 클라이언트는 서버와 연결되는 포인트만 알면 됨
5. Server-Client(서버-클라이언트 구조)
   - REST 서버는 API를 제공하고, 클라이언트는 사용자 정보를 관리하는 구조로 분리해 설계함
     - REST Server: API를 제공하고 비즈니스 로직 처리 및 저장을 담당함
     - Client: 사용자의 인증이나 context(세션, 로그인 정보) 등을 직접 관리하고 책임짐
   - 클라이언트와 서버는 각각 서로에 대한 의존성을 낮추는 기능을 함


<br/>
<br/>

## REST API

> `API`: `Application Programming Interface`  
>   - 데이터와 기능의 집합을 제공하여 컴퓨터 프로그램간 상호작용을 촉진하며, 서로 정보를 교환가능 하도록 하는 것
 

<br/>

<img width="500px" src="https://appmaster.io/cdn-cgi/image/width=1024,quality=83,format=auto/api/_files/NydJSQz2pxfUmD5yTEe2FR/download/">

- REST 기반으로 서비스 API를 구현한 것으로, REST 아키텍처를 따르는 시스템/애플리케이션 인터페이스를 의미함
- 각 요청이 어떤 동작이나 정보를 위한 것인지를 그 요청의 모습 자체로 추론이 가능하다는 큰 특징을 가짐


<br/>

### REST API 디자인 가이드

1. URI는 정보의 자원을 표현해야 한다.
    - resource는 동사보다는 명사를, 대문자보다는 소문자를 사용함
2. 자원에 대한 행위는 HTTP Method로 표현한다.
    - URI에 HTTP Method가 들어가면 안됨
        - ex) `GET /members/delete/1` ➜ `DELETE /members/1`
    - URI의 행위에 대한 동사 표현이 들어가면 안됨
        - ex) `GET /members/show/1` ➜ `GET /members/1`
    - 경로 부분 중 변하는 부분은 유일한 값(id)으로 대체함
        - ex) `DELETE /members/{id}`

<br/>

### REST URI의 설계 규칙

1. 슬래시 구분자(`/`)는 계층 관계를 표현하는데 사용한다.
2. URI의 마지막에는 `/`를 포함하지 않는다.
   - 옳은 예) http://ocalhost.com/product
   - 잘못된 예) http://ocalhost.com/product/
3. 언더바(`_`)를 사용하지 않고 하이픈(`-`)을 사용한다.
   - 하이픈은 리소스의 이름이 길어지면 사용함(가독성을 높일 때 사용)
   - 옳은 예) http://localhost.com/provider-company-name
   - 잘못된 예) http://ocalhost.com/provider_company_name
4. URL에는 행위(동사)가 아닌 결과(명사)를 포함한다.
   - 행위는 HTTP 메서드로 표현할 수 있어야 함
   - 옳은 예) http://localhost.com/product/123
   - 잘못된 예) http://localhost.com/delete-product/123
5. URI는 소문자로 작성해야 한다.
    - 일부 웹 서버의 운영체제는 리소스 경로 부분의 대소문자를 다른 문자로 인식하기 때문에 URI 리소스 경로에는 대문자 사용을 피하는 것이 좋음
6. 파일의 확장자는 URI에 포함하지 않는다.
    - 메시지 바디 내용의 포맷을 나타내기 위한 파일 확장자를 URI 안에 포함시키지 않고, HTTP에서 제공하는 `Accept 헤더`를 사용하는 것이 좋음
    - 잘못된 예) http://csstudy.tistory.com/restapi/220/photo.jpg
7. [HTTP 상태 코드](https://github.com/jmxx219/CS-Study/blob/jmxx219/network/HTTP%20%EC%83%81%ED%83%9C%20%EC%BD%94%EB%93%9C.md)를 사용해야 한다.

<br/>
<br/>

## RESTful

> `RESTful하다` = REST 아키텍처를 구현하는 웹 서비스를 표현하는 말

- REST의 원리를 따를 시스템을 의미하며, REST를 사용했다고 모두가 RESTful한 것은 아님
  - REST API의 설계 규칙을 올바르게 지킨 시스템을 RESTful하다고 말할 수 있음

<br/>

### RESTful의 목적
- 이해하기 쉽고 사용하기 쉬운 REST API를 만드는 것
- RESTful한 API를 구현하는 근본적인 목적은 성능 향상에 있는 것이 아닌 **일관적인 컨벤션을 통한 API의 이해도 및 호환성을 높이는 것**
  - 따라서 성능이 중요한 상황에서는 굳이 RESTful한 API를 구현할 필요는 없음
- REST API의 설계 규칙을 올바르게 지키지 못한 시스템은 REST API를 사용했지만, RESTful하지 못한 시스템이라고 할 수 있음
- RESTful 하지 못한 경우
  - ex) 모든 CRUD 기능을 POST로 처리 하는 API
  - ex) URI 규칙을 올바르게 지키지 않은 API


<br/>
<br/>

### Ref

- [REST API란 무엇이며 다른 유형과 어떻게 다른가요?](https://appmaster.io/ko/blog/rest-apiran-mueosimyeo-dareun-yuhyeonggwa-eoddeohge-dareungayo)
- [REST, REST API, RESTful 특징](https://hahahoho5915.tistory.com/54)