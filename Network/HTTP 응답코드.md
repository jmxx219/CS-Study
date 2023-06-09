HTTP 응답 코드는 클라이언트의 요청에 대한 서버에서 반환되는 코드를 말한다. 응답 코드를 통해서 요청 결과에 대한 정보를 알 수 있다. 

응답 코드는 5개의 분류로 구분된다.

- 1xx (정보 전달) : 1로 시작하는 응답코드는 서버가 요청을 받았고, 클라이언트는 작업을 진행하라는 의미. HTTP/1.0 이후 거의 쓰이지 않는다.
- 2xx (성공) : 요청이 서버에서 정상적으로 수신되었음을 나타낸다.
    - 200 OK : 요청이 성공했음을 나타내는 응답 코드.
    - 201 Created : 요청이 성공했고, 자원이 생성되었음을 나타내는 응답 코드. 주로 POST, PUT 요청에 대한 결과.
    - 202 Accepted : 요청은 성공했으나, 서버가 아직 요청을 완료하지 못함.
- 3xx (리다이렉션) : 클라이언트가 요청을 완료하기 위해 추가 작업을 수행해야 함을 말한다.
    - 301 Moved Permanently : 요청한 리소스의 URI가 영구적으로 변경되었음을 의미
    - 302 Found: 요청한 리소스의 URI가 일시적으로 변경되었음을 의미
- 4xx (클라이언트 오류) : 이 코드는 클라이언트 요청이 잘못되었음을 의미한다.
    - 400 Bad Request : 서버가 클라이언트 오류를 감지해 더 이상 작업을 진행하지 않는 경우를 의미.
    - 401 Unauthorized : 비인증을 의미.
    - 403 Forbidden : 리소스에 접근할 권리가 없음.
    - 404 Not Found : 클라이언트가 요청한 자원이 존재하지 않음
- 5xx (서버 오류) : 요청을 받은 서버에서 오류가 발생했음을 의미한다.
    - 500 Internal Server Error : 서버에 문제 있음을 의미. 문제에 대한 구체적인 설명은 없음.

## 200 vs 201

두 코드 다 성공적인 요청을 의미하는 2xx 계열이다. 하지만 서로 다른 맥락으로 사용된다. 200 OK 코드는 성공적인 응답에 사용되며, 클라이언트에서 요청된 리소스가 존재하고 반환되었음을 나타낸다. 

반면 201 Created 코드는 새 리소스가 성공적으로 생성되었음을 나타내며, 클라이언트가 찾을 수 있게 위치에 대한 정보도 제공한다. 

## 401 vs 403

두 코드 다 클라이언트 오류를 의미하는 4xx 계열이다. 하지만 서로 다른 의미로 사용된다. 401 Unauthorized 응답 코드는 클라이언트 요청에 유효한 자격 증명을 제공해야 함을 말한다. 즉 401이 반환되었다는 것은 리소스에 대한 요청에 유요한 자격 증명이 없음을 의미한다.

반면 403 Forbidden 응답 코드는 클라이언트가 유요한 자격 증명을 제시했지만 요청한 리소스에 접근할 수 없음을 의미한다. 

쉽게 생각하면 401은 로그인이 안 된 상태고, 403은 로그인은 했지만, 관리자 권한이 없는 상태를 생각하면 이해가 쉬울 것이다. 그렇기에 403은 서버가 클라이언트가 누군지 알고 있다.
