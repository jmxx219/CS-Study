# WebFlux

<br>

## 개념
- Spring 5에서 도입된 비동기 논블로킹 웹 프레임워크
    - 반응형 및 비동기적인 웹 애플리케이션 개발을 지원하는 모듈
    - 클라이언트, 서버에서 reactive 스타일의 어플리케이션 개발을 도와주는 모듈

- reacive-stack web framework
    - non-blocking, reactive stream지원

<br>
<br>


## 목적
- `반응형 프로그래밍`을 통해 **높은 처리량**과 **확장성**을 갖는 어플리케이션을 만들도록 함
- 적은 양의 스레드와 최소한의 하드웨어 자원으로 `동시성 핸들링`
    - 서블릿3.1이 논블로킹을 지원하나 일부분임
    - netty와 같은 잘 만들어진 async, non-blocking 서버 사용


> netty: 비동기 이벤트 기반 네트워크 어플리케이션 프레임워크

<br>
<br>

## 배경 지식

#### ■ 반응형 프로그래밍(Reactive Programming)

- 개념
    - 데이터의 흐름과 변경사항의 전파에 중점을 둔 선언적 프로그래밍 패러다임
        - EX) 엑셀의 스프레드 시트 동작과 같은 개념
    - 비동기 데이터 Stream으로 Non-bolockin어플리케이션을 구현하는 프로그래밍
        - Stream으로 프로그래밍 → 함수형 처리 가능
- 특징
    - 웹 프레임워크로의 전환으로 인해 웹 개발에서 반응형 프로그래밍이 중요해짐
        - 웹 프레임워크의 본질: 값이 변경되었을때 템플릿에 선언해둔 대로 알아서 랜더링 해줌(=반응형)
    - **데이터의 변경을 감지**하고 **선언적으로 프로그래밍**하는 방법을 통해 View를 업데이트 하는 방식으로 발전

<br>
<br>


#### ■ 선언적 프로그래밍
- 개념
    - 무엇을 해야할지 따로 약속(표현)을 만들어 기술하게 하고, 언제 어떻게 동작하는지는 내부에서 처리하는 방식의 프로그래밍 기법
- 특징
    - 일반적으로 명령형 프로그래밍에 비해 가독성, 재사용성, 독립성, 유지보수성에서 좋음
    - 내부 매커니즘은 숨기면서 필요한 로직만 외부에서 선언할 수 있게함
        - 알고리즘을 구현하는데 발생하는 에러 최소화
        - 시간과 변화에 구애받지 않고 개발하도록 도움

<br>
<br>


#### ■ 변경사항의 전파 (Pull → Push)
- Pull에서 Push로의 전환
    - `Pull`
        - 관련 데이터가 필요한 시점에 데이터를 요청하는 방식
        - 레거시 데이터가 있거나 중복 요청이 있을 수 있음
    - `Push`
        - 값이 변경될때마다 변경된 값을 전파해서 사용
        - 언제나 변경된 최신의 데이터를 가지고 있음

<br>

> 반응형 프로그래밍의 패러다임에선 미리 선언되어있는 구조에서 값이 변화할때마다 템플릿으로 데이터를 전달하는 PUSH 관점으로 설계되도록 변화 됨 

<br>
<br>

### Reactive Streams
- 비동기 스트림 처리 표준을 정의한 스펙
- 데이터의 흐름과 백프레셔(Backpressure)를 관리하는 방법을 표준화
    - 다양한 구현체가 존재할 수 있도록 설계
    - 대표적인 구현체: Java 9의 Flow API
- 주요 인터페이스
    - `Publisher`: 데이터 항목을 생성하고 구독자(Subscriber)에게 제공하는 역할
    - `Subscriber`: Publisher로부터 데이터를 구독하고, 데이터를 수신할 때 처리하는 역할
    - `Subscription`: 구독과 관련된 정보를 캡슐화하며, 구독자가 Publisher로부터 데이터를 요청하거나 구독을 취소할 수 있게 함
    - `Processor`: Publisher와 Subscriber의 역할을 모두 수행할 수 있는 인터페이스

<br>

### Reactor
- Reactive Streams 표준을 구현한 라이브러리로, Spring WebFlux의 핵심 라이브러리
- 비동기 스트림을 효율적으로 처리하기 위한 다양한 도구와 API 제공
    - 함수형 프로그래밍 스타일을 사용하여 데이터 스트림 처리
    - 다양한 연산자를 제공하여 데이터 변환, 필터링, 집계 등을 수행
- 주요 클래스
    - `Mono`
        - 0 또는 1개의 요소를 비동기적으로 처리하는데 사용
    - `Flux`
        - 0부터 N개의 요소를 비동기적으로 처리하는데 사용

> WebFlux는 Reactor와 Reactive Streams을 기반으로 한 비동기 논블로킹 웹 프레임워크

<br>


### 특징
- 비동기적인 이벤트 지향 프로그래밍을 통해 높은 확장성과 성능 제공
- 높은 확장성
    - 비동기 논블로킹 방식으로 더 많은 동시 요청 처리 가능
- 효율적인 리소스 사용
    - 효율적인 스레드 풀 사용 (블로킹 IO를 사용하지 않기때문)
- 백프레셔 관리
    - Reactive Streams 표준을 따르기 때문에 데이터 흐름 제어와 백프레셔를 효과적으로 관리               
    `백프레셔(Backpressure)`
        - 데이터 처리 시스템에서 생산자와 소비자 사이의 데이터 흐름을 제어하는 메커니즘
        - 비동기 스트림 처리에서 생산자의 빠른 데이터 생성을 소비자가 따라잡지 못하는 경우 발생하는 시스템이 과부하, 메모리 문제 발생 방지를 위해 데이터 흐름을 조절하는 방법

<br>
<br>


## Spring MVC VS WebFlux
<img width="450" alt="스크린샷 2024-07-30 오전 9 12 30" src="https://github.com/user-attachments/assets/1d83299f-8b14-4d97-81e8-fc6eb58a0f0d">

<br>
<br>

**Spring MVC**
- 동기 블로킹 방식의 전통적인 웹 프레임워크
- 서블릿 API 기반 동작

<br>

**WebFlux**
- 비동기 논블로킹 방식의 모듈이자 웹 프레임워크
- Reactive Streams 표준을 따르며, 다양한 논블로킹 런타임에서 실행될 수 있음

<br>

- 요청 처리 방식
    - `Spring MVC`
        - 동기 및 블로킹 방식
        - 요청이 들어오면 해당 요청을 처리할 스레드가 블로킹되어 응답이 완료될 때까지 기다림
    - `Spring WebFlux`
        - 비동기 및 논블로킹 방식
        - 요청이 들어오면 이벤트 루프 기반의 비동기 프로세싱을 통해 처리되어 한 스레드가 여러 요청을 처리 가능
- 동작 흐름
    - `Spring MVC`
        - request - Dispatcher Servlet - Handler Mapper - Controller - B/L - Controller - ViewResolver ...
     - `Spring WebFlux`
        - request - HttpHandler - WebHandler - Handler Mapper / Handler Adapter - Controller - B/L - 이하 같음
- 기본 구현체
    - `Spring MVC`
        - 서블릿 API 기반 동작
        - 주로 Tomcat, Jetty 같은 서블릿 컨테이너에서 실행
    - `Spring WebFlux`
        - 서블릿 API 뿐만 아니라 Netty, Undertow 같은 논블로킹 런타임에서도 실행 가능
- 백프레셔 관리
    - `Spring MVC`
        - 전통적인 방식으로 백프레셔 관리가 내장되어 있지 않음
        - 동시 요청 수 제어에 제한이 있음
    - `Spring WebFlux`
        - Reactive Streams 표준을 따름
        - 백프레셔를 관리할 수 있어 시스템의 안정성 높임
- 응용 프로그래밍 적합성
    - `Spring MVC`
        - 전통적인 웹 애플리케이션, RESTful 서비스 등 동기 요청/응답 모델에 적합
    - `Spring WebFlux`
        - 대규모의 동시 연결을 처리해야 하는 애플리케이션, 실시간 데이터 스트리밍, 마이크로서비스 아키텍처 등에 적합
- 반응형 프로그래밍 지원
    - `Spring MVC`
        - 일반적으로 블로킹 방식으로 코드를 작성하지만, 필요에 따라 CompletableFuture, ListenableFuture 등을 통해 비동기 작업 수행 가능
    - `Spring WebFlux`
        - 기본적으로 Mono, Flux와 같은 Reactor 타입을 사용하여 반응형 프로그래밍 지원
    

<br>
<br>
<br>

### REF
[[Java] Spring Boot Webflux 이해하기 -1 : 흐름 및 주요 특징 이해](https://adjh54.tistory.com/232)
[[Spring] WebFlux란?](https://heeyeah.github.io/spring/2020-02-29-web-flux/)                
[Spring WebFlux](https://kimyhcj.tistory.com/343)