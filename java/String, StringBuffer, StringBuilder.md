# 문자열(String & StringBuffer & StringBuilder)

> String & StringBuffer & StringBuilder는 Java에서 대표적으로 문자열을 다루는 자료형 클래스


<br>
<br>

## String

**개념**
- String 객체 값은 변경할 수 없는 **불변 자료형**
    - 초기 공간과 다른 값에 대한 연산에서 많은 시간과 자원을 사용

**내부 구조**

```java
    public final class String implements java.io.Serializable, Comparable {
        private final byte[] value;
    }
```
- 인스턴스 생성 시 생성자의 매개변수로 입력받는 문자열이 value라는 인스턴스 변수에 문자열 배열로 저장 됨
- 해당 변수는 상수(final)형으로 값을 바꾸지 못함
- jdk 9부터 기존 char[]에서 byte[] 사용
    - String Compacting을 통한 성능 및 heap 공간 효율(2byte -> 1byte)을 높이도록 수정

<br>

### ➕ String 자료형의 연산
- Strign자료형 또한 `+`, `concat()` 메소드로 문자열을 이어붙일 수 있음
    - 내용이 합쳐진 새로운 String 인스턴스 생성
    - 문자열을 많이 결합할수록 **공간 낭비**, **속도 저하**
- `trim`, `toUpperCase` 메소드 역시 또 다른 String 객체를 생성해 리턴하는 것
- 매번 문자열이 업데이트 될때마다 메모리 블럭 추가 → 일회용으로 사용된 메모리들은 GC 제거 대상이 됨 → 빈번하게 Minor GC를 일으켜 [Full GC(Major Gc)](https://github.com/jmxx219/CS-Study/blob/main/java/Garbage%20Collection.md#major-gc---old-generation)를 일으킬수 있는 원인이 됨

<br>
          
**`+연산`**

```java
    String a = "hello" + "world";
    /* 는 아래와 같다. */
    String a = new StringBuilder("hello").append("world").toString(); 
    // StringBuilder를 통해 "hello" 문자열을 생성하고 "world"를 추가하고 toString()을 통해 String 객체로 변환하여 반환
```
- 자바에서 `+ 연산`을 사용하면, 컴파일 전 내부적으로 StringBuilder 클래스를 만든 후 다시 문자열로 돌려줌.         
- 문자열이 합치는 일이 많을 경우, 매번 new StringBuilder() 객체 메모리를 생성하고 다시 변수에 대입하는 행동을 하는 것임
    - 아예 처음부터 StringBuilder() 객체로 문자열을 생성해 다루는게 효율적임

<br>

**`String.concat 메소드`**
- 메소드 호출 시 마다 원본 문자열의 배열을 매번 재구성하는 과정을 거침 >> 느림

<br>
<br>


### ➕ String이 불변인 이유
- `캐싱`
    - String을 불변하게 함으로써 String pool에 각 리터럴 문자열의 하나만 저장
    - 다시 사용하거나 캐싱에 이용가능하며 이를 통해 힙 공간 절약
- `보안`
    - DB이용 시와 같이(DB연결 수신을 위해 암호로 문자열 사용), 번지수의 문자열 값이 변경가능할 경우 해커가 참조 값을 변경할 보안 위험 존재
- `동기화`
    - 불변함으로써 동시에 실행되는 여러 스레드에서 안정적인 공유 가능


<br>
<br>

## StringBuffer / StringBuilder

<br>

```java
    StringBuffer sb = new StringBuffer();  // StringBuffer 객체 sb 생성
    sb.append("hello");
    sb.append(" ");
    sb.append("jump to java");
    String result = sb.toString();
    System.out.println(result); // hello jump to java
```

<br>



**개념**
- 문자열 연산을 전용으로하는 자료형
- 내부적으로 buffer라는 독립 공간을 가져, 문자열을 바로 추가할 수 있음
    - 공간낭비X, 연산속도 매우 빠름

**특징**
- 기본적으로 StringBuffer의 버퍼 크기의 기본값은 16개의 문자를 저장할 수 있는 크기
    - 생성자를 통해 크기 별도 설정 가능
    - 문자열 연산 중 할당된 버퍼 크기를 넘으면 자동으로 버퍼 증가 시킴

<br>
<br>

## StringBuffer/ StringBuilder 차이

> StringBuffer와 StringBuilder는 한가지 차이 존재

<br>

**공통점**
- 가변 문자열 자료형
- 제공되는 메소드 및 문법 동일
- 사용 방법 동일

<br>

**차이점**
- 멀티 스레등에서 안전한지 여부 (동기화에서 지원 유무)

<img width="500" src="https://github.com/jmxx219/CS-Study/assets/50795805/04209be3-9e81-4bd6-980d-eb20bf4c7ac1">
<br>
<br>

- StringBuilder 클래스
    - 동기화 지원 X
    - 쓰레드에서 안전하지 않음(thread unsafe)
    
- StringBuffer 클래스
    - 동기화 지원 O
    - 쓰레드에서 안전(thread safe)
    - 매서드에서 [synchronized](https://github.com/jmxx219/CS-Study/blob/main/java/Synchronized.md#synchronized) 키워드 사용

> web, 소켓환경과 같이 비동기로 동작하는 경우가 많을 경우 StringBuffer 사용이 안전

> 기본 성능은 StringBuilder가 우수


<br>
<br>


## 문자열 자료형 선택

 - StringBuffer/StringBuilder 
    - 생성 시 버퍼 크기를 초기 설정 → 무거운 연산에 속함
    - 문자열 수정시 버퍼 크기 조절하는 내부 연산 필요
        - 많은 양의 문자열 수정이 아닐 경우 String 객체가 효율적일 수 있음
    > 문자열 추가나 변경등의 작업이 많을 경우 유리

<br>

- String
    - 크기가 고정되어 있으므로 단순하게 읽는 조회 연산에서 StringBuffer/StringBuilder 보다 빠르게 읽을 수 있음
    > 문자열 변경 작업이 거의 없는 경우 유리

<br>
<br>

### Ref
[자바 String / StringBuffer / StringBuilder 차이점 & 성능 비교
[Inpa Dev 👨‍💻:티스토리]](https://inpa.tistory.com/entry/JAVA-☕-String-StringBuffer-StringBuilder-차이점-성능-비교#)