# DTO, DAO, VO, Entity

### 개념            

`Data Transfer Object, 데이터 전송 객체`

<br>

<img src ="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F1kY1B%2Fbtsd0vz4t5D%2FafHCLi2KFH8OnxWTgewq9K%2Fimg.png" width="300" heigth="300">

**개념**
- 계층간 데이터 교환을 위해 사용하는 객체
    - 데이터 교환을 위한 `Java Beans`를 의미
- 요청과 응답을 처리하기 위한 객체

<br>

> **Java Beans**                
> - Java로 작성된 소프트웨어 컴포넌트를 지칭하는 단어로, 비즈니스 로직 부분을 담당하는 java프로그램 단위
> - 데이터를 표현하는 것을 목적으로 하는 자바 클래스

<br>

**특징**
- 다른 로직을 갖고 있지 않는 순수한 데이터 객체
- 오직 getter/setter 메서드만 가짐
- 일반적으로 Request용 DTO, Response용 DTO로 구분지어 사용

<br>

**불변객체 DTO**
- DTO가 setter매서드를 가질 경우 해당 DTO는 데이터가 가변적
- setter매서드를 삭제하고, 생성자를 통해 속성값을 초기화하여 불변객체로 만들 수 있음
    - 전달과정 중 데이터 불변성을 보장함

<br>

*가변 객체 DTO*
```java
// 가변 객체 DTO
// 기본생성자로 생성 후 값을 유동적으로 변경 
public class DtoEx {
    private String name;
    private int age;

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

<br>

*불변 객체 DTO*

```java
// 불변 객체 DTO
// 생성시 지정했던 값이 변하지 않고 getter() 메소드만 사용 가능
public class DtoEx {
    private final String name;
    private final int age;

    public DtoEx(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}
```

<br>
<br>

## DAO
`Data Access Object`

**개념**
- DB에 접근하는 역할을 하는 객체로, 직접 DB에 접근해 데이터 삽입, 삭제, 조회등 CRUD 기능 수행
- 서비스 모델에 해당하는 부분과 DB를 연결하는 역할
- 데이터의 CRUD 작업을 시행하는 클래스
    - 데이터에 대한 CRUD 기능을 전담하는 오브젝트

<br>

**특징**
- DB접근을 위한 로직과 비즈니스 로직의 분리를 위함
    - 효율적인 커넥션 관리
    - 보안성: 비즈니스 로직 분리로 도메인 로직으로부터 DB와 관련한 메커니즘을 숨김
- DAO의 형태
    - DAO에 DB Connection이 설정되어있는지 여부로 DAO의 형태가 나뉨
    - `MyBatis`
        - DB Connection정보를 root-context.xml파일에 저장
        - `@Mapper`: mybatis가 작성해준 DAO
    - `JPA`
        - applicaiton.yml(properties)파일에 설정해 사용
         - `@Repository`: 스프링이 작성해준 DAO


<br>
<br>

## VO

`Value Object, 값 객체`

<br>

**개념**
- 값 그 자체를 표현하는 객체
- 도메인에서 한개 또는 그 이상의 속성들을 묶어서 특정 값을 나타내는 객체

<br>

**특징**
- 동등성
    - 값 객체의 모든 속성이 같은 `값`을 가질 시 동일한 객체로 판단
    - 이때, [equals(), hashCode() 오버라이딩](https://github.com/jmxx219/CS-Study/blob/main/Java-Spring/equals와%20hashCode.md#equals와-hashcode-1)을 했다는 전제 조건 필요
    - 해당 속성들은 `primitive 타입`
- 불변성
    - 수정자(setter)를 포함하지 않고, 생성자를 통해서 값 초기화
    - 새로운 값 사용을 위해선 새로운 객체 생성
        - 중간에 데이터를 조작할 수 없음
        - Read Only
- 자가 유효성 검사(Self-Validation)
    - 유효성 검사 진행 후 VO 생성
    - VO 사용 시 유효성 검사가 보장되어있으므로 안전하게 사용 가능
- getter 이외의 로직 포함 가능
- 값 타입을 여러 엔티티에서 공유 시 `Side effect` 발생가능

<br>

**사용 이유**               

primitive 타입이 도메인 객체를 모델링하기에 충분하지 않음

<br>

1. primitive 타입의 기능들을 객체가 전부 사용하지 않음

    ```java
        public class Ladder {

        private final int width;
        private final int height;

        public void Ladder(int width, int height) {
            this.width = width;
            this.height = height;
        }
    }
    ```
    - 위 코드의 경우, '사다리의 높이가 음수가 되는 경우', '사다리 높이의 연산' 등 사다리에서 필요없는 `int`기능들이 사용된다.

<br>

2. 한 곳이 아니라 여러 곳에서 사용될 때 중복 코드 발생

    ```java
    public class Rectangle {

        private final int width;
        private final int height;

        public void Ladder(int width, int height) {
            validateWidth(width); // 중복 코드
            validateHeight(height); // 중복 코드
            this.width = width;
            this.height = height;
        }
        
        private void validateWidth() {
            ...
        }
        
        private void validateHeight() {
            ...
        }
    }
    ```
    - 다음과 같이 `사다리`와 같은 형태를 가진 `사각형`에서 또한 유효성 검사코드가 중복되서 사용된다. 
    - 이처럼 유효성 검사, 불변 체크등의 진행을 `값`이 있는 모든 객체에서 진행해야하는 상황이 일어남


<br>

*VO 예시*
```java
public class ShapeProperty {
		
    // 불변성 (Immutable)
    private final int width;
    private final int height;

    public Shape(final int width, final int height) {
        // 자가 유효성 검사 (Self-Validation)
        validateWidth(width);
        validateHeight(height);
        
        this.width = width;
        this.height = height;
    }

    // 동등성 (Equality)
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        ShapeProperty that = (ShapeProperty) o;
        return width == that.width && height == that.height;
    }

    @Override
    public int hashCode() {
        return Objects.hash(width, height);
    }
}
```

```java
// 원시 타입 사용
public class Ladder {
		
    private final int width;
    private final int height;

    public void Ladder(int width, int height) {
    	validateWidth(width);
        validateHeight(height);
        
        this.width = width;
        this.height = height;
    }
}

// VO로 변경
public class Ladder {
		
    private final ShapeProperty shapeProperty;

    public void Ladder(final ShapeProperty shapeProperty) {
        this.shapeProperty = shapeProperty;
    }
}
```

- 사다리가 `모양 속성`이란 네이밍을 가진 객체를 가질 수 있게 됨
    - 원시타입 너비, 높이를 묶어 ShapeProperty라는 값 객체를 가짐
- Ladder에서는 `핵심 비즈니스 로직`만 가짐
    - 유효성 검사와 불변체크를 Ladder가 아닌, 값 객체 ShapeProperty에서 수행
- 향후 같은 형태의 Rectangle이 추가되어도 중복으로 유효성 검사, 불변 체크 불필요
    - Rectangle이 너비, 높이를 사용하더라도 원시 타입이 아닌 ShapeProperty 사용

<br>
<br>

### Entity

**개념**
- JPA에서 사용되는 개념으로 `@Entity`어노테이션을 붙여 엔티티를 나타냄
- 실제 DB의 테이블과 정확하게 매칭되는 속성들을 갖도록 디자인 된 클래스

<br>

**특징**
- ID로 구분 되며 로직 포함 가능
- 비즈니스 로직을 엔티티로 모음으로 응집도가 높아짐

<br>

**Entity와 DTO의 분리**
- Entity는 요청/응답 값을 사용하는 클래스로 사용하면 안됨
    - Entity는 DB와 매핑되어있는 핵심 클래스
    - 이를 기준으로 테이블이 생성되고 스키마가 변경 됨
- Entity클래스를 요청/응답 값을 전달하는 클래스로 사용할 경우 뷰 변경시 마다 Entity클래스를 변경해야 함  
    - REST API 설계시 엔티티의 속성이 변화(추가, 삭제)될때마다 API명세가 바뀌게 됨
    - API명세가 테이블에 의존하는 현상이 발생
- 요청/응답 값을 사용하는 클래스로 DTO의 사용
    - 뷰의 변경에 따라 다른 클래스에 영향을 끼치지 않고 자유롭게 변경 가능
    - 여러 테이블들을 Join한 결과값을 응답값으로 표현하기 편리함

<br>
<br>

### Ref
[DTO란 무엇이고 왜 사용해야 할까?](https://tristy.tistory.com/54)               
[[10분 테코톡] 📍인비의 DTO vs VO](https://www.youtube.com/watch?v=z5fUkck_RZM&t=10s)                   
[VO(Value Object)는 무엇일까? 왜 사용할까?](https://ksh-coding.tistory.com/83)