# Generic

### Generic
제네릭(Generic)은 사전적 정의만 보면 "일반적인"이라는 의미를 가지고 있다.

자세한 내용에 앞서 제네릭을 어떻게 사용하는지 먼저 살펴보자

제네릭 사용 예시
```
List<Integer> list1 = new ArrayList<>();
List<String> list1 = new ArrayList<>();
```
이처럼 <>를 사용하여 리스트에 들어갈수 있는 요소의 타입을 정해준다. 예시처럼 리스트에는 Integer타입도, String타입도 들어갈수 있는데 그렇다면 각 타입마다 List를 모두 구현해야할까?

List 인터페이스를 살펴보면 어떠한 특정 타입이 아니라 `E` 라는 타입이 들어간것을 볼수 있다.

<img src="https://github.com/Creative-Semester/server/assets/74135929/8e847024-7b83-4ab0-8cd5-28903dad9f06" width=350>

제네릭을 통해 위에서 잠시 고민했던 타입결정을 해결할수 있다.
즉, 제네릭은 **클래스 내부에 타입을 지정하는것이 아닌 외부에서 사용자에 의해 타입이 지정**되는 것을 의미한다.

<br>


## Generic 사용법

### 표기법
제네릭은 다음과 같이 많이 사용된다.

| 표기  | 의미      |
|-----|---------|
|  | Type    |
| \<E\> | Element |
| \<K\> | Key     |
| \<V\> | Value   |
| \<N\> | Number  |

<br>

### 클래스 및 인터페이스 선언

클래스와 인터페이스에서 다음과 같이 제네릭을 선언한다.
```java
public interface List<E> {...}
public class ArrayList<E> {...}
```
외부에서 지정한 타입인 T는 {}블록 안에서 유효하다.

제네릭타입을 두개로 둘수도 있다.
```java
public class HashMap<K,V> {...}
public interface Map<K,V> {...}
```

이렇게 생성된 제네릭 클래스를 사용할때는 구체적인 타입을 명시해 생성하면 된다.
```java
public static void main(String[] args) {
    Map<String, Integer> map = new HashMap<String, Integer>();
}
```
즉, Map의 제네릭 타입 K는 String이 V는 Integer가 된다.

<br>

### 주의점
제네릭의 타입 파라미터로 명시할수 있는 것은 [Reference Type](https://github.com/jmxx219/CS-Study/blob/main/java/Wrapper%20Class.md)이다.

즉, primitive type은 올수 없다.
다시말하면 사용자가 정의한 클래스 또한 제네릭타입으로 명시할수 있다는 의미이다. 

```java
class Person{
    int height;
    int weight;
}

public static void main(String[] args) {
    List<Person>list=new ArrayList<>();
}
```

<br>


### Generic활용
제네릭 클래스를 활용해보자!
```java
class ClassName<E> {
	
	private E element;	// 제네릭 타입 변수
	
	void set(E element) {	// 제네릭 파라미터 메소드
		this.element = element;
	}
	
	E get() {	// 제네릭 타입 반환 메소드
		return element;
	}
	
}
 
class Main {
	public static void main(String[] args) {
		
		ClassName<String> a = new ClassName<String>();
		ClassName<Integer> b = new ClassName<Integer>();
		
		a.set("10");
		b.set(10);
	
		System.out.println("a data : " + a.get());
		// 반환된 변수의 타입 출력 
		System.out.println("a E Type : " + a.get().getClass().getName());
		
		System.out.println();
		System.out.println("b data : " + b.get());
		// 반환된 변수의 타입 출력 
		System.out.println("b E Type : " + b.get().getClass().getName());
		
	}
}
```
<img src="https://github.com/jmxx219/CS-Study/assets/74135929/4381b3a4-9fae-4618-9c02-2fdddbd5e4a8" width="200">

결과를 살펴보면 각 클래스의 선언된 제네릭에 따라 타입이 결정된 것을 확인할수 있다.

<br>

실제 ArrayMap 또한 제네릭을 통해 구현하는 것을 확인할수 있다.
```java
public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable {
    
    public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }
    public V get(Object key) {
        Node<K,V> e;
        return (e = getNode(key)) == null ? null : e.value;
    }
    
}
```

<br>


### 제네릭 메서드
지금까지 클래스에 붙은 제네릭에 대해 알아보았다. 그러나 클래스와 별도록 메서드에 한정해 제네릭을 사용할수 있다.
```java
public <T> T genericMethod(T o) {	// 제네릭 메소드
		...
}
 
[접근 제어자] <제네릭타입> [반환타입] [메소드명]([제네릭타입] [파라미터]) {
	// 텍스트
}
```
지금까지 본 클래스의 제네릭과 달리 메서드에 <> 제네릭 타입을 선언했다.

<br>

```java
// 제네릭 클래스
class ClassName<E> {
	
	private E element;	// 제네릭 타입 변수
	
	void set(E element) {	// 제네릭 파라미터 메소드
		this.element = element;
	}
	
	E get() {	// 제네릭 타입 반환 메소드 
		return element;
	}
    
	<T> T genericMethod(T o) {	// 제네릭 메소드
		return o;
	}
}
 
public class Main {
	public static void main(String[] args) {
		
		ClassName<String> a = new ClassName<String>();
		ClassName<Integer> b = new ClassName<Integer>();
        
		// 제네릭 메소드 Integer
		System.out.println("<T> returnType : " + a.genericMethod(3).getClass().getName());
		
		// 제네릭 메소드 String
		System.out.println("<T> returnType : " + a.genericMethod("ABCD").getClass().getName());
		
		// 제네릭 메소드 ClassName b
		System.out.println("<T> returnType : " + a.genericMethod(b).getClass().getName());
	}
}
```
<img src="https://github.com/jmxx219/CS-Study/assets/74135929/88dae760-f1e3-456a-b59c-a86420a6e3b5" width="250">

제네릭 메서드인 genericMethod()는 파라미터의 타입에 따라 T타입이 결정된다. 
결과를 확인하면 같은 a인스턴스이지만 genericMethod의 파라미터에 따라 타입이 달라진것을 확인할수 있다.

그렇다면 이러한 방식이 왜 필요할까? -> **[정적(static)](https://github.com/jmxx219/CS-Study/blob/main/java/static%20class%EC%99%80%20static%20method.md) 메서드**를 선언할때 필요하기 때문이다.

static키워드가 붙으면 프로그램 실행시 메모리에 올라가게된다. 따라서 외부에서 타입을 결정해주는 제네릭의 특성상 인스턴스 생성이 되기 전까지 타입 결정이 되지 않아 에러가 날수 있다.

```java
class ClassName<E> {
    static <E> E genericMethod1(E o) { // 제네릭 메소드
        return o;
    }
}

class Main {
    public static void main(String[] args) {
        ClassName.getnerMethod(3);
    }
}
```
<br>
<br>

### 제한된 제네릭과 와일드 카드
지금까지 제네릭에 넣은 Reference Type에 따라 타입에 결정되었다. 그렇다면 타입을 특정짓는것이 아닌 가능한 타입을 범위로 제한할수 없을까?

이때 필요한것이 extends, super 그리고 ?(와일드 카드)이다.
> 와일드 카드는 "알수 없는 타입"이라는 의미이다.

<br>

보통 다음과 같이 부른다.

extend T : 상한경계

? super T : 하한 경계

`<?>` : 와일드 카드

```java
<K extends T>	// T와 T의 자손 타입만 가능 (K는 들어오는 타입으로 지정 됨)
<K super T>	// T와 T의 부모(조상) 타입만 가능 (K는 들어오는 타입으로 지정 됨)
 
<? extends T>	// T와 T의 자손 타입만 가능
<? super T>	// T와 T의 부모(조상) 타입만 가능
<?>		// 모든 타입 가능. <? extends Object>랑 같은 의미
```

<br>

**주의점**

이 때 주의해야 할 게 있다. K extends T와 ? extends T는 비슷한 구조지만 차이점이 있다.

'유형 경계를 지정'하는 것은 같으나 경계가 지정되고 K는 특정 타입으로 지정이 되지만, ?는 타입이 지정되지 않는다는 의미다.

```java
/*
 * Number와 이를 상속하는 Integer, Short, Double, Long 등의
 * 타입이 지정될 수 있으며, 객체 혹은 메소드를 호출 할 경우 K는
 * 지정된 타입으로 변환이 된다.
 */
<K extends Number>
 
 
/*
 * Number와 이를 상속하는 Integer, Short, Double, Long 등의
 * 타입이 지정될 수 있으며, 객체 혹은 메소드를 호출 할 경우 지정 되는 타입이 없어
 * 타입 참조를 할 수는 없다.
 */
<? extends T>	// T와 T의 자손 타입만 가능
```

<br>


### 제한된 Generic의 사용 예시

**extend**

extend의 경우 상한경계 즉, 자신과 자손타입만 제네릭으로 지정가능하다.
```java
public class ClassName <K extends Number> { 
	... 
}
 
public class Main {
	public static void main(String[] args) {
 
		ClassName<Double> a1 = new ClassName<Double>();	// OK!
 
		ClassName<String> a2 = new ClassName<String>();	// error!
	}
}
```
예시를 보면 <K extends Number>로 설정되어 있다, Double은 Number를 상속받기 때문에 괜찮지만, String은 아니기 때문에 에러가 나게 된다.

<br>

**super의 예시**

super는 하한 경계 즉, super뒤에 오는 타입이 최하위 타입으로 지정된다.
```java
public class SaltClass <E extends Comparable<E>> { ... }
 
public class Student implements Comparable<Student> {
	@Override
	public int compareTo(Person o) { ... };
}
 
public class Main {
	public static void main(String[] args) {
		SaltClass<Student> a = new SaltClass<Student>();
	}
}
```
보통 업캐스팅이 필요할때 사용된다.

<br>

**복잡한 예제**
```java
public class ClassName <E extends Comparable<? super E>> { ... }
```
코테를 풀다보면 PriorityQueue, TreeSet과 같이 정렬이 필요한 클래스에서 Comparator에 대해 한번쯤 본적 있을것이다.
위의 예시와 같은 제네릭으로 구성되어 있는데 한번 분석해보자

<br>

우선 `E extend Comparable`을 먼저 살펴보자 

extend의 경우 Comparable이 상한 경계가 된다는 의미인데, 이는 곧 Comparable의 구현이 필수적이라는 의미이다.(Comparable은 인터페이스이다.)

```java
public class SaltClass <E extends Comparable<E>> { ... }
 
public class Student implements Comparable<Student> {
	@Override
	public int compareTo(Person o) { ... };
}
 
public class Main {
	public static void main(String[] args) {
		SaltClass<Student> a = new SaltClass<Student>();
	}
}
```

즉, 다음과 같이 구현이 필수적이다. 하지만 코드를 살펴보면 Comparable의 제네릭에 super를 사용하지 않은것을 볼수 있다.

그렇다면 왜 다른 클래스들에서는 super를 사용할까? 왜냐하면 구현코드가 다른 클래스를 상속받아 더 큰 범주의 클래스와 비교될수 있기 때문이다.
```java
public class SaltClass <E extends Comparable<E>> { ... }	// Error가능성 있음
public class SaltClass <E extends Comparable<? super E>> { ... }	// 안전성이 높음
 
public class Person {...}
 
public class Student extends Person implements Comparable<Person> {
	@Override
	public int compareTo(Person o) { ... };
}
 
public class Main {
	public static void main(String[] args) {
		SaltClass<Student> a = new SaltClass<Student>();
	}
}
```
단순히 `<E extends Comparable<E>>`한다면 특정 객체와의 비교에서는 괜찮지만 해당 객체와 더 큰 범주의 객체가 비교되는 순간 제대로 정렬이 안되거나 에러가 발생할수 있다.
따라서 제네릭의 super를 통해 위의 에러를 사전에 방지합니다.

<br>

**<?> 와일드 카드**

와일드 카드 `<?>`은 `<? extend Object>` 와 같다.

즉, 어떤 타입이든 상관없다는 의미이다.

보통 데이터가 아닌 기능에 중점을 두는 경우 <?>를 사용한다.


