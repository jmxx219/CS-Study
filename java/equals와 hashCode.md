# Equals()와 Hashcode()

> equals()와 Hashcode는 모든 Java 객체의 부모 객체인 Object 클래스에 정의되어 있다. 따라서 Java의 모든 객체는 두 함수를 상속받고 있다.

<br/>

## == 연산자

- `primitive type`에 대해선 값 비교를, `reference Type`에 대해서는 객체의 주소 값을 비교함
    - `원시 타입(Primitive type)`
        - 기본적인 데이터 타입으로, 자바에서 미리 정의되어 있는 데이터 타입
        - 빈 객체 타입으로 NULL값을 가질 수 없으며 Stack메모리 저장
    - `참조 타입(Reference Type)`
        - 객체를 가리키는 참조(메모리 주소)를 저장하는 데이터 타입
        - 빈 객체 타입 NULL을 가질 수 있으며 Heap 메모리에 생성

<br/>
<br/>


## equals()

```java
// Object의 기본 equals 메서드
public boolean equals(Object obj) {
    return (this == obj);
}
```

- 개념        
    - 어떤 두 참조 변수 값의 동등여부를 비교할 때 사용하는 매소드
        - 동일성(Identity) 비교
    - 어떤 두 객체가 가리키는 곳이 동일한 메모리 주소일 경우에만 동일한 객체로 판단
- 특징            
    - java.lang.Object 클래스에 정의
    - equals 메소드의 기본동작은 객체의 참조값을 비교하나, **객체의 내부 값을 비교하기 위해 논리적으로 재정의(override)** 될 수 있음
- 주의사항
    - Null의 처리
        - equals() 메서드는 null과의 비교도 처리할 수 있어야 함
        - null과는 항상 false를 반환
        - null인지 검사하여 NullPointerException이 발생하지 않도록 주의
    - 타입체크
        - 매개변수로 전달된 객체가 현재 객체와 동일한 클래스(또는 하위 클래스)의 인스턴스인지 확인
    - 상속과 호환성
        - 부모 클래스의 equals() 메서드도 동작하도록 super.equals()를 호출하거나 부모 클래스의 동치성 비교에 필요한 속성들을 자식 클래스에서 고려

<br/>

### equals 메소드 오버라이딩
- 프로그래밍 시 동일한 객체가 메모리 상에 여러 개 띄워져 있는 경우가 존재함
    - 해당 객체가 서로 다른 메모리에 띄워져있을 경우 동일한(Identity) 객체가 아님
- 하지만 프로그래밍 상으로 같은 값을 지님으로 같은 객체로 인식되도록 처리하고 싶은 상황이 존재할 수 있음
    - 이런 동등성(Identity)을 위해, **값으로 객체를 비교하도록** equals 매소드 오버라이딩을 진행함
- EX) 동일한 값을 갖는 문자열 2개에 대한 equals() 호출               
    - 두 문자열은 서로 다른 메모리에 할당되어 동일하지 않으나, 같은 값을 가져 동등함
    - String 클래스에서 equals매소드 오버라이드 진행
        - 객체가 같은 값을 갖는지 동등성(Equality)을 비교하도록 처리
    - 동일성을 비교하는 equals 메소드 호출 시 true 반환

<br/>
<br/>

## Hash code

> **native** 키워드               
> : 메소드가 JNI라는 native code를 이용해 구현되어 있음을 의미하며, OS에 C언어로 작성되어있어 그 안의 내용은 볼 수 없고 사용만 가능하다.
> `hashCode()`는 native 코드 중 하나이다.

<br/>

```java
public native int hashCode();
```

- 개념
    - 객체의 주소 값을 이용해서 해싱(hashing) 기법을 통해 반환한 해시 코드로 객체를 식별하는 하나의 정수 값
- 특징      
    - java.lang.Object 클래스에 정의
    - 객체의 메모리 번지를 이용해 해시코드를 만들어 리턴하므로 객체마다 다른 값을 가짐
        - 주소값으로 만든 고유한 숫자값
- 재정의 시, 고려사항
    - 객체 상태 기반 해시코드 생성
        - 객체의 필드들이 모두 hashCode에 반영되어야 함
        - 객체가 가지는 모든 중요한 정보 고려해야 함
    - 불변성
        - 객체의 상태가 변경되지 않으면 hashCode()의 결과도 변하지 않아야함
    - 충돌 최소화
        - 서로 다른 객체가 동일한 해시값을 갖지 않도록 충돌을 최소화하는 해시함수 선택

<br/>

### equals와 hashCode

> **자바 규칙**            
> equals()의 결과가 `true`인 두 객체의 해시코드는 반드시 같아야 한다.

- Hash값을 사용하는 Collection 객체의 논리적 비교
    1. HashCode() 리턴 값 비교
        - 리턴 값 다름 → 다른 객체
        - 리턴 값 같음 → equals()
    2. equals() 리턴 값 비교
        - 리턴 값 다름 → 다른 객체
        - 리턴 값 같음 → 동등 객체

<br/>

**Hash값을 재정의 하지 않을 경우**
- hash값을 사용하는 Collection Framework(HashSet, HashMap, HashTable)에서 객체 비교를 진행함
- 이때 hashCode 메서드를 재정의 하지 않았으므로, HashCode() 리턴 값 비교 과정에서 Object 클래스의 hashCode 메서드를 사용함
    - 같은 객체여도 equals()로 비교하기 전에 서로 다른 hashcode 메서드의 값이 리턴되어 다른 객체로 판단 됨
- 위와 같은 이유로 equals와 HashCode의 값을 같이 재정의 해야 함

<br/>
<br/>

### identity HashCode 메소드

```java
System.identityHashCode(p1);
```

- 개념           
    - hashCode()를 오버라이딩 해서 쓰되, 오버라이딩 하기 전의 원조 기능이 필요할 때 사용 하는 메서드
- 배경
    - HashCode의 본래 목적은 `객체의 주소값을 기반으로 해싱한 고유 숫자 값`을 바탕으로 객체가 동등한지 여부를 판단하는 것
    - 하지만 equals()와 hashCode() 메서드를 오버라이딩할 경우, 본래 목적인 객체 자체의 해시코드를 얻을 수 없음
    - 따라서 자바에서는 똑같이 해시 코드를 반환해주는 identityHashCode() 코드를 만들게 되었음

<br/>
<br/>
