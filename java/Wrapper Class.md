# Wrapper Class

<br>

## Java 변수의 기본형 & 참조형
- 자바에서 데이터 타입(자료형)은 크게 두 가지로 구분됨
  - `기본형(primitive type)`: 계산을 위해 실제 값을 저장함
    - 논리형(boolean), 문자형(char), 정수형(byte, short, int, long), 실수형(float, double)
  - `참조형(reference type)`: 객체의 주소를 저장하며, null 또는 객체의 주소(4byte, 0x0 ~ 0xffffffff)를 가짐
    - 배열(Array), 열거형(Enum), 클래스(Class), 인터페이스(interface)
- 프로그래밍을 하다 보면 기본 타입의 데이터를 객체로 표현해야하는 경우가 발생함
  - ex) 메소드의 인수로 객체 타입만이 요구되면, 기본 타입의 데이터를 그대로 사용할 수 없기 때문에 변환 작업이 필요해짐
- 이러한 경우에 `기본 타입`을 `객체`로 다루기 위해서 사용하는 클래스들을 `래퍼 클래스(wrapper class)`라고 함

<br>

### 래퍼 클래스의 필요성

1. 래퍼 클래스는 기본 데이터 타입을 Object로 변환할 수 있음
   - 메소드에 전달된 인수를 수정하려는 경우, 오프벡트가 필요함
2. java.util 패키지의 클래스는 객체만 처리하기 때문에 래퍼 클래스는 이 경우 도움이 됨
3. Collection 프레임 워크의 데이터 구조는 기본 타입이 아닌 객체만 저장하게 되고, 래퍼 클래스를 사용하여 자동 방식과 언박싱이 일어남
    ```Java
    List<Integer>list = new ArrayList<>();
    list.add(200); //자동 박싱
    ```

<br>
<br>

## 래퍼 클래스 (Wrapper Class)

> java.lang 패키지의 클래스들 중 기본형 변수를 감싸는 클래스들로, 래퍼 클래스는 값을 포장하여 객체로 만드는 것  
> 즉, 래퍼 클래스는 기본 타입의 객체화를 말함

```Java
Integer num1 = new Integer(5); // 기본형 타입 정수를 래퍼 클래스로 감싸 객체화
Integer num1 = 5;

Double num2 = new Double(1.11); // 기본형 타입 실수를 래퍼 클래스로 감싸 객체화
Double num2 = 1.11;
```

- 자바의 모든 기본 타입은 값을 갖는 객체를 생성할 수 있고, 이런 객체를 `포장 객체`라고 함
  - 기본 타입의 값을 내부에 두고 포장하는 것처럼 보이기 때문
- 래퍼 클래스로 감싸고 있는 기본 타입의 값은 외부에서 변경할 수 없음
  - 만약 변경하고 싶다면 새로운 포장 객체를 생성해야 함
- 래퍼 클래스는 모두 `java.lang` 패키지에 포함되어 제공됨
  - 따라서 별다른 패키지를 불러오지 않아도 소스 단에서 사용이 가능함
  - 래퍼 클래스를 이용하면 각 타입에 해당하는 데이터를 파라미터로 전달받아 해당 값을 가지는 객체로 만들어줌

<br>

### 래퍼 클래스 구조도

<img src="https://velog.velcdn.com/images%2Fdoxxx93%2Fpost%2Fbdfc45d6-46bb-42ec-97e4-ffc2690dea76%2Fimage.png" width="500"/>

- 모든 래퍼 클래스의 부모는 `Object`이고, 내부적으로 숫자를 다루는 래퍼 클래스의 부모 클래스는 `Number` 클래스임
- 모든 래퍼 클래스는 최종 클래스로 정의됨

<br>

### 래퍼 클래스의 종류

|  기본 타입  |  래퍼 클래스   | 생성자                                                          |
|:-------:|:---------:|:-------------------------------------------------------------|
| boolean |  Boolean  | Boolean(boolean value)<br>Boolean(String s)                  |
|  char   | Character | Character(char value)                                        |
|  byte   |   Byte    | Byte(byte value)<br>Byte(String s)                           |
|  short  |   Short   | Short(short value)<br>Short(String s)                        |
|   int   |  Integer  | Integer(int value)<br>Integer(String s)                      |
|  long   |   Long    | Long(long value)<br>Long(String s)                           |
|  float  |   Float   | Float(float value)<br>Float(double value)<br>Float(String s) |
| double  |  Double   | Double(double value)<br>Double(String s)                     |

- int와 char을 제외한 래퍼 클래스는 모두 기본 타입에서 첫 글자만 대문자로 바꾸면 됨

<br>

### 래퍼 클래스의 자료형 변환 메소드
```Java
String str = "10";
String str2 = "10.5";
String str3 = "true";

byte b = Byte.parseByte(str);
int i = Integer.parseInt(str);
short s = Short.parseShort(str);
long l = Long.parseLong(str);
float f = Float.parseFloat(str2);
double d = Double.parseDouble(str2);
boolean bool = Boolean.parseBoolean(str3);
```
- 래퍼 클래스는 객체를 포장하는 기능 외에도, 래퍼 클래스에서 자체 지원하는 `parse타입()` 정적 메서드를 이용하여 데이터 타입을 형 변환할 때도 유용하게 쓰임
  - 해당 메서드는 문자열을 매개 값으로 받아 기본 타입 값으로 변환함

<br>
<br>

## 박싱(Boxing)  & 언박싱(UnBoxing)

- 값을 포장하여 객체로 만드는 것은 좋지만, 만일 값을 변환시켜야 할 필요가 생긴다면 포장을 다시 뜯을 필요가 있음
- 래퍼 클래스는 산술 연산을 위해 정의된 클래스가 아니기 때문에 생성된 인스턴스의 값만 참조가 가능함
  - 따라서 래퍼 클래스에 저장된 값을 직접 변경하는 것은 불가능함
  - 그래서 래퍼 클래스를 언박싱 한 뒤에 값을 변경하고, 이후에 박싱하는 중간 단계가 필요함

```Java
Integer num = new Integer(20); // 박싱
int n = num.intValue(); // 언박싱 (intValue)

// 재 포장(박싱)
n = n + 100; // 120
num = new Integer(n);
```
- `Boxing`: 기본 타입의 데이터 → 래퍼 클래스의 인스턴스로 변환(포장 객체로 만드는 과정)
- `UnBoxing`: 래퍼 클래스의 인스턴스에 저장된 값 → 기본 타입의 데이터로 변환(포장 객체에서 기본 타입의 값을 얻는 과정)

<br>
<br>

## 자동 박싱(AutoBoxing) & 자동 언박싱(AutoUnBoxing)

- JDK 1.5부터는 자바 컴파일러가 박싱과 언박싱을 자동으로 처리해주어 이를 오토박싱과 오토 언박싱이라고 부름
- 기본 타입 값을 직접 박싱/언박싱하지 않아도 래퍼 클래스 변수에 대입만 하면 자동으로 박싱과 언박싱이 됨
  ```Java
  * 기존 박싱 & 언박싱 */
  Integer num = new Integer(17); // 박싱
  int n = num.intValue();        // 언박싱
  
  /* 오토 박싱 & 언박싱 */
  Integer num = 17;  // new Integer() 생략
  int n = num;       // intValue() 생략
  ```
  - new 키워드를 사용하지 않아도 자동으로 인스턴스를 생성할 수 있음
  - 또한, 언박싱 메서드를 사용하지 않아도 오토 언박싱을 이용하여 인스턴스에 저장된 값을 바로 참조할 수 있게 됨


<br>

### 래퍼 클래스 연산

```Java
Integer num1 = new Integer(7); // 박싱
Integer num2 = new Integer(3); // 박싱

int int1 = num1.intValue();    // 언박싱
int int2 = num2.intValue();    // 언박싱

// 박싱된 객체를 오토 언박싱하여 연산하고 다시 박싱하여 저장
Integer result1 = num1 + num2; // 10 
Integer result2 = int1 - int2; // 4
int result3 = num1 * int2;     // 21
```
- 오토 박싱과 언박싱 기능을 이용해서 래퍼 객체를 직접 연산하는 것이 가능해짐
- 원래는 래퍼 클래스는 직접 연산이 불가능하지만, 컴파일러가 스스로 판단해 자동으로 언박싱하여 연산하기 때문에 허용됨


<br>

### 래퍼 클래스 동등 비교

- 인스턴스에 저장된 값에 대한 동등 여부 판단은 동등 연산자(`==`)로 값을 비교하는 것이 아닌 객체의 주소값을 비교함
  - 따라서 의도적이지 않은 작동이 일어나기 때문에 객체 값을 비교하는 경우 조심해야 함
- 래퍼 클래스 간의 객체 값 비교는 포장 내부의 값을 얻어 비교해야 하기 때문에, 직접 언박싱해서 비교하거나 `equals()` 메서드를 통해 비교함
  ```Java
  Integer num1 = new Integer(10);
  Integer num2 = new Integer(20);
  Integer num3 = new Integer(10);
  
  System.out.println(num1 == num3);      // false
  System.out.println(num1.equals(num3)); // true
  ```
  - 대신 래퍼 클래스와 기본 자료형의 비교는 자동으로 오토박싱과 언박싱을 해주기 때문에 `==` 연산과 `equals()` 연산 모두 가능함
    ```Java
    Integer num = new Integer(10); // 래퍼 클래스1
    Integer num2 = new Integer(10); // 래퍼 클래스2
    int i = 10; // 기본타입
  
    System.out.println("래퍼클래스 == 기본타입 : " + (num == i));              //true
    System.out.println("래퍼클래스.equals(기본타입) : " + num.equals(i));      //true
    System.out.println("래퍼클래스 == 래퍼클래스 : " + (num == num2));          //false
    System.out.println("래퍼클래스.equals(래퍼클래스) : " + num.equals(num2));  //true
    ```


<br>
<br>

### Ref

- [자바 Wrapper 클래스와 Boxing & UnBoxing 총정리](https://inpa.tistory.com/entry/JAVA-%E2%98%95-wrapper-class-Boxing-UnBoxing)
- [[Java] 래퍼 클래스(Wrapper Class)란 무엇인가? (박싱, 언박싱)](https://coding-factory.tistory.com/547)

