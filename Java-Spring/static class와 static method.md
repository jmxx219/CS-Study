# static class와 static method

자바에서 static 키워드는 JVM이 시작될 때 static 영역에 한번 저장되어 프로그램이 종료될 때 해제되는 것을 의미한다.  
static 영역에 할당된 메모리는 모든 객체가 공유하는 메모리라는 장점을 가지지만 [Garbage Collection](https://github.com/jmxx219/CS-Study/blob/main/Java-Spring/Garbage%20Collection.md)이
관리하지 않으므로 자주 사용한다면 메모리 효율이 떨어질 수 있다.  

<br> 

## static 메서드

static 메서드는 객체 생성 없이 사용할 수 있는 메서드이다. static 메서드의 장점은 속도가 빨라지고 공유에 효율적이라는 점이다.  


<br>

**사용 예시**

```java
class Counter  {
    static int count = 0;
    Counter() {
        count++;
        System.out.println(count);
    }

    public static int getCount() {
        return count;
    }
}

public class Sample {
    public static void main(String[] args) {
        Counter c1 = new Counter();
        Counter c2 = new Counter();

        System.out.println(Counter.getCount());  // 스태틱 메서드는 클래스를 이용하여 호출
    }
}

```

<br>

**실행 결과**

```text
1
2
2
```

<br>

`Counter` 클래스의 `getCount` 메서드는 static 메서드이다. 때문에 객체 생성 없이 클래스를 통해 메서드를 직접
호출할 수 있다. 이때 주의할 점은 static 메서드 안에서는 static 변수만 접근이 가능하다.  

static 메서드는 디자인 패턴 중 **싱글톤 패턴**에서 사용한다. 싱글톤 패턴은 객체를 한번만 생성하고 그 객체를 공유하는 패턴이다.  

생성자를 private로 만든다면 외부에서 new 키워드로 생성이 불가하다. 하지만 static 메서드를 사용한다면 객체 생성 없이 접근할 수 있기 때문에 
싱글톤 패턴에서 인스턴스를 가져올 때 사용한다.  

<br>

**예제 코드**

```java
class Singleton {
    private static Singleton one;
    private Singleton() {
    }

    public static Singleton getInstance() {
        if(one==null) { // 처음 호출 되어 one 객체가 null 이라면
            one = new Singleton(); 
        }
        return one;
    }
}

public class Sample {
    public static void main(String[] args) {
        Singleton singleton1 = Singleton.getInstance();
        Singleton singleton2 = Singleton.getInstance();
        System.out.println(singleton1 == singleton2);  // true 출력
    }
}
```

<br>

그럼 static 메서드는 장점만 있을까?  
아니다, **객체지향에서 멀어지고, 메모리 효율이 오히려 떨어질 수 있다.**

<br>

### 객체지향

객체지향에서는 객체들이 서로 관계를 맺고 메시지를 통해 정보를 교환하고 결과를 반환하길 기대한다.
static 메소드를 실행하는 것은 객체에게 행위를 지시하는 것도 아니고, 다른 객체와 관계를 맺고 있지도 않다. 
메시지 전달이 아니라 (절차지향의) 함수 호출에 불과하다고 할 수 있다.

객체지향의 특징인 다형성 또한 위반한다. static 메서드는 오버라이딩과 동적 바인딩이 불가하다. 컴파일 시에 정적 바인딩 되기 때문이다.  

<br>

### 메모리 효율

static 메서드를 사용하면 객체를 생성하지 않고 사용할 수 있기에 메모리 효율이 좋을 수 있지만 그렇다고 막무가내로 사용하면 안된다. 
static 키워드가 붙은 메서드는 GC의 대상이 아니다. static으로 할당된 영역이 크다면 GC의 효율이 떨어지고, 프로그램이 끝날 때까지 
그 영역은 메모리에서 내릴 수 없다. 때문에 static 영역이 많은 메모리를 차지하고 있다면 메모리 부족 현상이 발생할 수 있다.  


<br>

## static 클래스

Inner 클래스는 클래스 내부에 있는 클래스를 말한다.  

Inner 클래스의 문제점은 **외부 참조를 한다는 점**과 **메모리 누수 현상이 발생한다는 점** 이다.  

<br>

### 외부 참조

Inner 클래스를 생성하면 내부 클래스가 외부의 멤버를 사용하지 않아도, 숨겨진 외부 참조가 생성된다.  

즉 비정적 멤버 클래스의 인스턴스는 외부 클래스의 인스턴스와 암묵적으로 연결된다. 

<br>

**예시 코드**

```java
public class Outer_Class {
   int field = 10;

   class Inner_Class {
       int inner_field = 20;
   }
}
```

<br>

위의 코드는 `Outer_Class.class` 파일이 생성된다. 그리고 `Outer_Class$Inner_Class.class` 파일도 같이 생성된다. 이 class 파일을 
바이트 코드로 디컴파일 시켜보면 아래와 같이 외부 클래스를 매개변수로 받아 인스턴스 변수로 저장하는 것을 확인 할 수 있다.  


<br>

```java
class Outer_Class$Inner_Class {
    int inner_field;
    
    Outer_Class$Innter_Class(Outer_Class this$0) {
        this.this$0 = this$0;
        this.inner_field = 20;
    }
}
```

<br>

이때 `this$0`는 OuterClass를 참조하는 있는 바이트 코드에서만 보여지는 **Hidden** 변수이다.  

<br>

### 메모리 누수

Inner 클래스에서 외부 클래스는 필요가 없어지고 내부 클래스만 남아있을 경우, 외부 클래스가 GC 대상이 되어 바로 사라져야 할거 같지만, 외부 참조로 인해
내부 클래스와 연결되어 있어 사라지지 않는다. 메모리에서 제거가 안되고 이는 메모리 누수로 연결되어 프로그램이 터진다.  

예시 코드로 살펴보자.  


**Outer_Class**

```java
import java.util.ArrayList;

class Outer_Class {
	// 외부 클래스 배열 변수
    private int[] data;

    // 내부 클래스
    class Inner_Class {
    }
	
    // 외부 클래스 Constructor
    public Outer_Class(int size) {
        data = new int[size]; // 사이즈를 받아 배열 필드의 크기를 불림
    }
	
    // 내부 클래스 객체를 생성하여 반환하는 메소드
    Inner_Class getInnerObject() {
        return new Inner_Class();
    }
}
```

<br>

```java
public class Main {
    public static void main(String[] args) {

        ArrayList<Object> arr = new ArrayList<>();
        
        for (int cnt = 0; cnt < 50; cnt++) {
            // inner_Class 객체를 생성하기 위해 Outer_Class를 초기화하고 메서드를 호출하여 리스트에 넣는다.
            // Outer_Class 는 메서드 호출용으로 한번 사용되고 더이상 사용되지 않기에 GC 대상이 되어야 한다.
            arr.add(new Outer_Class(100000000).getInnerObject());
            System.out.println(cnt);
        }
    }
}
```

<br>

위의 코드의 실행과정을 따라가보자.  


1. Inner_Class 를 담을 리스트 arr 생성
2. 반복문을 통해 50개의 Inner_Class 객체를 리스트의 담는다.  
3. 이때 내부 클래스 생성을 위해 외부 클래스를 먼저 인스턴스화 해야하기에, Outer_Class를 new 키워드로 인스턴스화한다..  
4. Outer_Class 생성자 파라미터로 1억 사이즈의 int 배열이 생성되어 400MB 크기의 객체가 힙 메모리에 쌓인다. 
5. 그리고 `getInnerObject()` 메서드를 호출하여 Inner_Class 객체를 생성하고 반환한다.  
6. 이제 생성된 Inner_Class 객체를 리스트에 저장한다.  
7. 메서드 호출용도로 일회용으로 사용된 400MB의 Outer_Class 객체는 더이상 필요없게 되어 GC 대상이 되어 힙 메모리에서 삭제가 되어야 할 것이다?


<br>

<img alt="image" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/79aea9fd-c5f7-494b-9fca-20a3a564b193" width="614"/>

<br>

위 코드를 실행해보면 얼마 못가 OutOfMemoryError 예외가 발생한다. GC가 정상적으로 Unreachable 데이터를 수거하지 못해 메모리 관리가 불가하여 
메모리 누수가 됐기 때문이다. 메서드 호출만을 위한 일회용 객체가 GC 수거 대상이 되지 못하여 400MB 데이터가 계속 쌓이게 되었고, 프로그램이 터진 것이다.  


<br>


**Inner 클래스는 static으로 선언하자**  

앞서 설명한 문제점들을 해결할 수 있는 것이 static class 이다.  

static 클래스로 만든 Inner 클래스는 외부 인스턴스 없이 사용하기에 외부 참조를 하지 않는다. 때문에 일회용으로 사용된
외부 클래스 객체는 더이상 내부 클래스와 관계가 없기 때문에 GC 수거 대상이 되어 메모리 관리가 가능하다.  

static 클래스가 아닌 클래스는 얼마 못가 OutOfMemoryError 를 던졌지만 static Inner class 는 끝까지 프로그램이 수행되었다.  

<br>

<img width="604" alt="image" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/11e82c1e-9c1c-4759-b4bc-1f54bc11292d">

<br>


<br>


> static 잘 알고 사용하자.


<br>

---

#### ref

[정적 메소드, 너 써도 될까?](https://tecoble.techcourse.co.kr/post/2020-07-16-static-method/)  
[07-03 스태틱](https://wikidocs.net/228)  
[☕ 내부 클래스는 static 으로 선언 안하면 큰일 난다](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%9E%90%EB%B0%94%EC%9D%98-%EB%82%B4%EB%B6%80-%ED%81%B4%EB%9E%98%EC%8A%A4%EB%8A%94-static-%EC%9C%BC%EB%A1%9C-%EC%84%A0%EC%96%B8%ED%95%98%EC%9E%90)
