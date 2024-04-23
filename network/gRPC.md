# gRPC

## gRPC란?

- gRPC는 구글에서 개발하고 오픈소스로 공개한 원격 프로시저 호출(Remote Procedure Call, RPC) 시스템이다. 
- 이 시스템은 클라이언트 애플리케이션에서 마치 로컬 객체인 것처럼 다른 컴퓨터에서 실행되는 서버 애플리케이션의 메서드를 직접 호출할 수 있게 해준다.

<br><br><br>

## gRPC의 특징

<img src="https://github.com/dragonappear/gitbook-learn/assets/89398909/23c12b50-049c-4a20-95b7-80568b72d77c" height="400">

- 언어 독립적 인터페이스: gRPC는 다양한 프로그래밍 언어를 지원한다. 이는 서로 다른 언어로 작성된 시스템 간의 통합을 간소화한다.
- 프로토콜 버퍼 사용: gRPC는 구조화된 데이터를 위한 강력하고 효율적인 프로토콜 버퍼를 사용한다.
- 양방향 스트리밍 지원: 클라이언트와 서버 간 양방향 통신을 지원하여 실시간 데이터 처리에 유용하다.
- HTTP/2 기반: gRPC는 HTTP/2를 사용하여 멀티플렉싱, 서버 푸시, 헤더 압축 등의 이점을 제공한다.

<br>

### 장점

- 효율적인 통신: 프로토콜 버퍼와 HTTP/2를 사용하여 낮은 대역폭과 빠른 속도를 제공한다.
- 강력한 인터페이스 정의 언어(IDL): 시스템 간의 계약을 명확하게 정의하여, 개발자가 오류를 줄일 수 있다.
- 다양한 환경 지원: 클라우드, 서버리스, 컨테이너화된 환경에서의 호환성이 뛰어나다.

<br>

### 단점

- 브라우저 지원이 제한적입니다. 현재 브라우저에서 gRPC 서비스를 직접 호출하는 것은 불가능하다.
- 이진 형식이기 때문에 텍스트 형식인 JSON에 비해 읽기가 불편하다
- .proto 파일이 없으면 무슨 의미인지 알 수가 없다.

<br>

### HTTP 1.x 와의 비교

- gRPC는 HTTP/2를 기반으로 하며, 이는 HTTP 1.x에 비해 상당한 성능 이점을 제공한다.
- gRPC 메시지는 Protobuf를 사용하여 직렬화되며, 이는 JSON보다 작고 이진 형식이다.
- gRPC는 클라이언트, 서버, 양방향 스트리밍을 지원하는 반면, HTTP는 클라이언트와 서버 스트리밍만 지원한다.

<br><br><br>

## gRPC 개념

<img src="https://github.com/dragonappear/gitbook-learn/assets/89398909/042c1bd0-c099-49b8-a73e-7669467c27cf">

### Stub

- gRPC에서 스텁(Stub)은 클라이언트와 서버 간의 통신을 추상화한 것이다. 
- 클라이언트 측에서는 클라이언트 스텁이 있으며, 이는 서버의 메서드를 마치 로컬 메서드인 것처럼 호출할 수 있게 해준다. 
- 서버 측에서는 서버 스텁이 있으며, 이는 클라이언트의 요청을 받아 처리하고 응답을 반환한다. 
- 이러한 방식으로 gRPC는 분산 시스템에서의 통신을 단순화하고 효율화한다.

<br>

### .proto 파일

- .proto 파일은 프로토콜 버퍼(Protobuf)의 인터페이스 정의 언어(IDL)로 사용되는 파일이다. 
- 이 파일에는 메시지 타입과 서비스를 정의하며, 이를 바탕으로 클라이언트와 서버의 코드를 생성한다. 
- 각 메시지 타입은 하나 이상의 필드를 가질 수 있으며, 각 필드는 이름과 타입을 가진다. 
- 필드 번호는 1부터 536,870,911 사이의 값으로, 이 번호는 메시지의 와이어 포맷에서 해당 필드를 식별하는 데 사용된다.

`예시`
```proto
syntax = "proto3";

package example;

// 메시지 정의
message Request {
    string query = 1;
}

message Response {
    string result = 1;
}

// 서비스 정의
service SearchService {
    rpc Search(Request) returns (Response);
}
```

<br>

### Protobuf(Protocol Buffer)

- 프로토콜 버퍼는 Google에서 개발한 언어 중립적이고 플랫폼 중립적인 직렬화 데이터 구조
- gRPC에서 메시지 포맷으로 사용되며, 클라이언트와 서버 간에 주고받는 데이터의 구조를 정의
- 정적 타이핑을 지원하여, 컴파일 시점에 데이터 타입의 오류를 감지할 수 있음
- 클라이언트와 서버 사이에서 데이터를 전송하기 전에 데이터를 직렬화하고, 수신 측에서는 이를 다시 역직렬화한다.

<br><br><br>

## 자바로 gRPC 맛보기

`build.gradle` 설정
```Groovy
plugins {
    id 'java'
    id 'com.google.protobuf' version '0.8.12'
}

group 'com.example'
version '1.0-SNAPSHOT'

repositories {
    mavenCentral()
}

dependencies {
    implementation 'io.grpc:grpc-netty-shaded:1.26.0'
    implementation 'io.grpc:grpc-protobuf:1.26.0'
    implementation 'io.grpc:grpc-stub:1.26.0'
}

protobuf {
    protoc {
        artifact = 'com.google.protobuf:protoc:3.11.4'
    }
    plugins {
        grpc {
            artifact = 'io.grpc:protoc-gen-grpc-java:1.26.0'
        }
    }
    generateProtoTasks {
        ofSourceSet('main')*.plugins {
            grpc {}
        }
    }
}
```

<br>

`.proto` 파일 예시
```proto
syntax = "proto3";

option java_multiple_files = true;
option java_package = "com.example.grpc";
option java_outer_classname = "HelloProto";

package hello;

// 서비스 정의
service Greeter {
    // RPC 메소드 정의
    rpc SayHello (HelloRequest) returns (HelloReply) {}
}

// 요청 메시지 정의
message HelloRequest {
    string name = 1;
}

// 응답 메시지 정의
message HelloReply {
    string message = 1;
}
```

<br>

`gRPC` 서버 예시
```Java
package com.example.grpc;

import io.grpc.Server;
import io.grpc.ServerBuilder;
import io.grpc.stub.StreamObserver;
import com.example.grpc.HelloProto.HelloRequest;
import com.example.grpc.HelloProto.HelloReply;

import java.io.IOException;

public class GrpcServer {

    public static void main(String[] args) throws IOException, InterruptedException {
        Server server = ServerBuilder
                .forPort(8080)
                .addService(new GreeterImpl())
                .build();

        server.start();
        System.out.println("Server started at " + server.getPort());
        server.awaitTermination();
    }

    static class GreeterImpl extends GreeterGrpc.GreeterImplBase {
        @Override
        public void sayHello(HelloRequest req, StreamObserver<HelloReply> responseObserver) {
            HelloReply reply = HelloReply.newBuilder()
                    .setMessage("Hello " + req.getName())
                    .build();

            responseObserver.onNext(reply);
            responseObserver.onCompleted();
        }
    }
}
```

<br>

`gRPC` 클라이언트 예시
```Java
package com.example.grpc;

import io.grpc.ManagedChannel;
import io.grpc.ManagedChannelBuilder;
import com.example.grpc.HelloProto.HelloRequest;
import com.example.grpc.HelloProto.HelloReply;

public class GrpcClient {

    public static void main(String[] args) {
        ManagedChannel channel = ManagedChannelBuilder
                .forAddress("localhost", 8080)
                .usePlaintext()
                .build();

        GreeterGrpc.GreeterBlockingStub stub = GreeterGrpc.newBlockingStub(channel);

        HelloRequest request = HelloRequest.newBuilder()
                .setName("World")
                .build();

        HelloReply response = stub.sayHello(request);
        System.out.println("Response: " + response.getMessage());

        channel.shutdown();
    }
}
```

<br><br><br>

## Ref

- https://grpc.io/docs/what-is-grpc/introduction/
- https://www.oreilly.com/library/view/grpc-up-and/9781492058328/ch01.html