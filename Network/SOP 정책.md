# SOP, CORS

## SOP(Same Origin Policy)

동일 출처 정책

<img src="https://mblogthumb-phinf.pstatic.net/MjAyMTA1MTBfMTM0/MDAxNjIwNjI0OTU1MDYx.dn22YB4KfrBFaOaASr4mmZth5BSVSPVbuv7X96BIHbkg.-oLdtPRsttEFkHB7uhdzb5BqT_XOhS-CoI17XO7f6g4g.PNG.funraon/9.png?type=w800" width="450" height="250"/>


- 동일한 출처의 리소스만 상호작용하는 정책이다. 
- 웹 페이지에서 실행되는 스크립트가 다른 도메인의 웹 페이지에 있는 리소스에 접근할 수 없도록 제한하는 정책이다.
  - 출처 A(ex: `service.example.com`)에서 온 문서가 출처 B(ex: `api.example.com`)에서 가져온 리소스에 접근할 하는 것을 차단
- SOP 정책은 웹 브라우저에서 기본적으로 제공되며, 웹 개발자가 명시적으로 설정할 필요는 없다.

<br/>

| Origin(`https://www.example.com`)    | 비교    |
|--------------------------------------|-------|
| `https://www.example.com/about`        | 동일 출처 |
| `https://www.example.com/about?q=query` | 동일 출처 |
| `https://abc.example.com`              | 교차 출처 |
| `http://www.example.com`               | 교차 출처 |
| `https://www.example.com:8080`         | 교차 출처 |

- 동일 출처와 교차 출처의 기준은 프로토콜, Host(도메인), Port 동일 여부이다. 이중 하나라도 Origin과 다르면 그 URL은 교차출처로 인식된다.
- **두 URL의 프로토콜, 호스트, 포트가 모두 같아야 동일한 출처로 인정되며 웹 사이트를 샌드박스화 하여 잠재적인 보안 위협으로부터 보호해주는 정책이다.**

<br/>

### Q. 왜 다른 출처 간의 상호 작용을 차단할까?

SOP 정책이 없는 상황이라고 가정해보자.

- 사용자가 악의적인 `JavaScript`가 포함되어 있는 페이지 (ex:`service.example.com`) 에 접속한다.
- 사용자가 악성 페이지에 (ex:`service.example.com`) 접속하여 악의적인 `JavaScript`가 실행된다.
- 악성 페이지 (ex:`service.example.com`)는 사용자가 모르는 사이에 다른 사이트 (ex: `portal.example.com`)에 임의의 요청을 보내어, 사용자의 개인 정보(사용자 IP 등)를 탈취할 수 있다.

<br/>

- SOP 장점: CSRF, XSS 같은 보안 취약점 공격으로 부터 안전하다.
- SOP 단점: 제한된 정책 때문에 외부에서 데이터를 불러오지 못한다.

<br/>
<br/>

## CORS(Cross Origin Resource Sharing)

교차 출처 리소스 공유

- 보안도 중요하지만 기능 상 어쩔 수 없이 다른 도메인에 있는 리소스에 접근해야 하는 경우가 발생한다.
- 이러 경우를 대비 하기 위해 SOP의 예외 정책으로 CORS 정책이 만들어졌다. CORS를 사용하면 SOP의 제약을 받지 않는다.
- CORS의 요청 방식에는 크게 2가지 방식이 있다.
  - `Simple Request`
  - `Preflight Request`

<br/>

### Simple Request

<img src="https://blog.kakaocdn.net/dn/m4pHR/btrtXqvZ1t4/MqeEIVar2kcyEidQtIy2i1/img.png" width="500" height="300"/>

#### Simple Request 조건

- HTTP Method 가 `GET`, `POST`, `HEAD` 중 하나
- Content Type 이 `application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain` 중 하나
- `CORS-safelisted request-header` 를 포함하는 경우
- `XMLHttpRequest.upload`에 이벤트 핸들러, 리스너가 등록되지 않은 경우
- `ReadableStream` 객체가 포함되지 않은 경우

<br/>

#### Simple Request 동작 방식

- 클라이언트가 요청 헤더에 자신의 `Origin`을 실어서 서버로 요청
- 서버는 요청 메시지 헤더의 `Origin`을 확인한다
- CORS 요청이 유효하다면 서버는 응답 헤더에 `Access-Control-Allow-Origin` 헤더를 추가해 사용자에게 응답한다.

<br/>

1. `service.example.com` -> `api.example.com` 사이트의 리소스를 가져오기 위해 JavaScript에서 HTTP 요청 메시지를 전송한다.

```HTML
<html>
〈head〉
    〈title> test〈/title>
</head>
〈body>
    〈div id="result">
    </div>
    〈script>
        httpRequest = new XMLHttpRequest();
        httpRequest.onreadystatechange = function() {
        if(this status == 200 && this.readystate == this.DONE) {
            document.getElementById("result").innerHTML = httpRequest.responseText;
        };
    
        httpRequest.open('GET', 'http://api.example.com/api/v1/test');
        httpRequest.send();
    〈/script>
</body>
〈/html〉
```

<br/>

2. HTTP 요청 메시지는 아래와 같이 전송된다.

```HTTP
GET /api/v1/test HTTP/1.1
Host: api. example.com
User-Agent : Mozilla/5.0 (windows NT 10.0; Win64; x64) Applewebkit/537.36 (KHTML, 1ike Ge
cko) Chrome/88.0.4324.190 Safari/537.36
Accept: text/html, application/xhtml+xml, application/xmljq=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: keep-alive
Origin: https://service.example.com
```

`service.example.com` 사이트의 프로토콜, 호스트, 포트 정보가 Origin 헤더를 통해 전송된다.

<br/>

3. 서버는 HTTP 요청  메시지의 Origin 헤더를 보고 어느 사이트를 통해 요청 메시지가 전송되었는지 확인할 수 있다. 해당 요청에 대한 HTTP 응답 메시지는 아래와 같다.

```HTTP
HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 00:23:53 GMT
Server : Apache/2
Access-Control-Allow-Origin: *
Keep-Alive: timeout=2, max=100
Connection: Keep-ALive
Transter-Encoding: chunked
Content-Type: application/xml
```

**서버는 `Access-Control-Allow-Origin` 헤더를 통해 응답해야 한다.**

- `Access-Control-Allow-Origin: *` = 모든 도메인에서 접근을 허용한다.
- `Access-Control-Allow-Origin: https://service.example.com` = `https://service.example.com`의 요청에서만 리소스에 대한 접근을 허용한다.

**만약 `Access-Control-Allow-Origin` 헤더가 없거나, 해당 헤더에 명시된 출처가 요청을 보낸 페이지의 출처와 일치하지 않는 경우, 브라우저는 SOP 정책에 따라 응답을 차단한다.**

<br/>

### Preflight Request

- `Simple Request`의 조건을 만족하지 못할시 브라우저가 자동으로 생성한다
- `Simple Request`와 달리 `OPTIONS` 메서드를 통해 다른 `Origin`의 리소스로 HTTP 요청을 미리 보내(`preflight`) 실제 요청이 전송하기에 안전하지 확인한다
- 브라우저는 안전하다고 판단되면 이를 통해 실제 요청을 보낸다. CORS 요청의 경우, 유저 데이터에 영향을 줄 수 있기 때문에 이와 같이 미리 전송한다

<br/>

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbiXNij%2FbtrtRDvYlf5%2FXDMOtjWgj3cKEUsF1G0lck%2Fimg.png" width="500" height="500"/>

#### 요청 헤더
- `Origin`
  - 현재 자신의 URL 정보를 포함
- `Access-Control-Request-Method`
  - `Preflight Request`에서 사용되며 어떤 Method를 사용할지 서버에게 알리기 위해 사용되는 헤더(POST, GET, DELETE 등이 포함)
- `Access-Control-Request-Headers`
  - `Preflight Request`에서 사용되며 어떤 Header를 사용할 것인지 서버에게 알리기 위해 사용되는 헤더 (X-PINGOTHER, Content-Type 등이 포함)

`Access-Control-Request-*` 헤더의 경우 실제 POST 요청시에는 포함되지 않고 **OPTIONS 요청시에만 사용된다.**

<br/>

#### 응답 헤더
- `Access-Control-Allow-Origin`
  - 브라우저가 해당 `Origin`이 리소스에 접근 가능하도록 허용할 때 사용되는 헤더
  - `*` 로 설정할 경우 브라우저는 `credentials` 옵션이 없는 요청에 한해 모든 `Origin`이 해당 리소스에 접근 가능하도록 허용
- `Access-Control-Allow-Methods`
  - `Preflight Request`에 대해 리소스에 접근할 때 허용되는 `Method`를 지정하기 위해 사용되는 헤더 (POST, GET, OPTIONS, *)
- `Access-Control-Allow-Headers`
  - Preflight Request에 대해 해당 요청에서 사용할 수 있는 Header를 지정하기 위해 사용되는 헤더 (X-PINGOTHER, Content-Type 등)

<br/>

### Ref

- [링크](https://blog.naver.com/funraon/222345124431)
- [링크](https://yoo11052.tistory.com/139)
