# Generic

<br>

## 개념
- 사전적 정의: "일반적인"
- 동일한 클래스 내부에 타입을 지정하는것이 아닌, **외부에서 사용자에 의해 타입이 지정**되는 것


<br>
<br>

## 등장 배경
- 제네릭 등장전, 컬렉션의 요소들을 다루는 메소드들은 타입이 보장되지 않기 때문에 문제가 발생함
	```java
		int sum(Collection c) {
			int sum = 0;
			Iterator i = c.iterator();
			for (k = 0; k < c.size(); k++) {
				sum += Integer.parseInt(i.next());
			}
			return sum;
		}
	```
	- 위 메소드는 String처럼 다른 타입을 갖는 컬렉션도 호출 가능
	- 다른 타입을 갖는 컬렉션은 컴파일 시점엔 문제가 없다가 런타임 시점에 매소드 호출 시 에러 발생

> 타입을 지정해 컴파일 시점에 안정성을 보장할 수 있는 방법으로 **제네릭** 등장

<br>
<br>

**제네릭 미리 보기**				

- `<>`를 통해 리스트에 들어갈 수 있는 요소의 타입을 정해줌
	```
	List<Integer> list1 = new ArrayList<>();
	List<String> list1 = new ArrayList<>();
	```

- `<>`에 특정 타입이 아닌 `E` 라는 타입이 들어감			
<img src="https://github.com/Creative-Semester/server/assets/74135929/8e847024-7b83-4ab0-8cd5-28903dad9f06" width=350>			

	첫번째 예시와 같이 타입별로 필요한 List를 구현하는 것이 아닌, `E`타입 이용 가능 

<br>

> 이처럼 제네릭을 통해 타입결정에 대한 고민을 해결할수 있음


<br>
<br>
<br>


## Generic 사용법

### 표기법

- `<>`
	- 꺾쇠 괄호 키워드 사용 (다이아몬드 연산자)
- 타입 매개변수 / 타입 변수
	- 꺾쇠 괄호 안에 `식별자 기호`를 지정함으로써 파라미터화 함
	- 제네릭을 이용한 클래스나 매소드 설계 시 사용
	- 타입 파라미터 생략
		- jdk 1.7 버전 이후부터,  new 생성자 부분의 제네릭 타입을 생략 가능
		- `FruitBox<Apple> intBox = new FruitBox<>();`
- 구체화
	- `<T>` 부분에서 실행부에서 타입을 받아옴
	- 내부에서 T 타입으로 지정한 멤버들에게 전파하여 타입이 구체적으로 설정됨

<br>	

| 타입 매개변수    | 의미      |
|-------|---------|
| \<T\> | Type    |
| \<E\> | Element |
| \<K\> | Key     |
| \<V\> | Value   |
| \<N\> | Number  |

<br>
<br>

### 클래스 및 인터페이스 선언

- 제네릭 선언 (클래스, 인터페이스)
	```java
	public interface List<E> {...}
	public class ArrayList<E> {...}
	```
	외부에서 지정한 타입인 T는 {}블록 안에서 유효하다.

- 복수 타입 파라미터
	- 타입 지정이 여러개 필요할 경우 여러개 사용 가능
	```java
	public class HashMap<K,V> {...}
	public interface Map<K,V> {...}
	```

- 중첩 파라미터
	```java
	ArrayList<LinkedList<String>> list = new ArrayList<LinkedList<String>>();
	```

- 제네릭 클래스의 사용
	```java
	public static void main(String[] args) {
		Map<String, Integer> map = new HashMap<String, Integer>();
	}
	```
	이렇게 생성된 제네릭 클래스를 사용할때는 구체적인 타입을 명시해 생성하면 된다.					

	즉, Map의 제네릭 타입 K는 String이 V는 Integer가 된다.

<br>

### 주의점
- 제네릭의 타입 파라미터로 명시할수 있는 것은 [Reference Type](https://github.com/jmxx219/CS-Study/blob/main/java/Wrapper%20Class.md)
	- 사용자가 정의한 클래스 또한 제네릭타입으로 명시할수 있음 
	- primitive type은 올수 없음

	<br>

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
<br>
<br>


## 배경 지식

- 변성
	- 타입의 상속 계층 관계에서 서로 다른 타입 간에 어떤 관계가 있는지를 나타태는 지표
	- 공변성(Covariance)과 반공변성(Contravariance)을 합친 개념

- 공변
	- S 가 T 의 하위 타입이면, S[] 는 T[] 의 하위 타입
	- `List<S>`는 `List<T>`의 하위 타입

- 반공변
	- S가 T의 하위 타입이면, T[] 는 S[] 의 하위 타입 (공변의 반대) 
	- `List<T>`는 `List<S>`의 하위 타입 (공변의 반대)

- 무공변 / 불공변 
	- S 와 T 는 서로 관계가 없음
	- `List<S>`와 `List<T>`는 서로 다른 타입

> Java에서는 일반적으로 제네릭 타입에 대해서 공변성 / 반공변성을 지원하지 않는다. 			
> 즉, 자바의 제네릭은 무공변의 성질을 지닌다


<br>
<br>
<br>

## Generic 특징

**특징**
- 컴파일 타임에 타입 검사를 통해 예외 방지
- 불필요한 캐스팅을 없애 성능 향상
	- 미리 타입을 지정 & 제한해 놓기 때문에 형 변환(Type Casting)의 번거로움을 줄일 수 있음
	- 타입 겁사에 들어가는 메모리 줄임
	- 가독성 향상 
- 제네릭은 공변성과 상하관계가 없음

<br>

**주의 사항**
- 제네릭 타입의 객체는 생성 불가
	- new 연산자 뒤에 제네릭 타입 파라미터가 올수 없음
- static 멤버에 제네릭 타입 올수 없음
- 제네릭 배열 선언
	- 기본적으로 제네릭 클래스 자체를 배열로 만들 수는 없음
	- 하지만 제네릭 타입의 배열 선언은 허용

<br>
<br>


### 제네릭은 공변성과 상하관계가 없음
- 제네릭은 무공변으로, 전달받은 타입으로만 서로 캐스팅이 가능함
	- 자바의 캐스팅
		```java
			Object[] Covariance = new Integer[10];
			Integer[] Contravariance = (Integer[]) Covariance;
		```

	- 제네릭의 캐스팅
		```java
			ArrayList<Object> Covariance = new ArrayList<Integer>();
			ArrayList<Integer> Contravariance = new ArrayList<Object>();
			-> 에러
		```
- 따라서 제네릭의 타입 파라미터(`<>`) 끼리 타입이 상속 관계에 놓여도 캐스팅 불가능
	- 원시 타입부분은(꺾쇠 괄호 부분을 제외) 공변성 적용
	- 꺾쇠 괄호 안의 실제 타입 매개변수에 대해서는 공변성이 적용이 되지 않음

	- 컬렉션 프레임워크 상속 관계
		```java
			Collection<Integer> parent = new ArrayList<>();
			ArrayList<Integer> child = new ArrayList<>();

			parent = child; // 다형성 (업캐스팅)
		```

	- 제네릭의 상속 관계
		```java
			ArrayList<Object> parent = new ArrayList<>();
			ArrayList<Integer> child = new ArrayList<>();

			parent = child; // ! 업캐스팅 불가능
			child = parent; // ! 다운캐스팅 불가능
		```

> 공변성이 없는 특징때문에 매개변수로 제네릭을 사용할때 외부에서 대입되는 인자의 캐스팅 문제로 애로사항이 발생함. 하지만 캐스팅이 지원되지 않는다면 유연하게 타입을 결정하기 위한 제네릭의 장점이 사라지게 됨.
>
> 해당 문제를 해결하기 위한 기능이 **와일드 카드**

<br>
<br>
<br>

## 와일드 카드

> 컴퓨터 프로그래밍에서 `?`와 같이 하나 이상의 문자들을 상징하는 특수 문자



### 와일드카드(`?`)
- 모든 타입을 대신 할 수 있는 타입
- 정해지지 않은 unknown type
- 타입에 제한이 없어 `비제한 와일드카드` 타입이라함
- 요소를 추가하는 연산은 허용하지 않음
	- unknown type으로 컴파일러가 구체적으로 어떤 타입인지 알 수 없기에 타입 안정성을 보장할 수 없음

<br>

### 한정적 와일드 카드
- 특정 타입을 기준으로 상한 범위와 하한 범위를 지정함으로써 호출 범위를 확장 또는 제한 가능
- 상한 경계 와일드 카드
	- `<? extends MyParent>`
		- MyParent와 unknown의 모든 MyParent 자식 클래스들 가능
	- 와일드카드 타입에 extends를 사용해서 와일드카드 타입의 최상위 타입을 정의함으로써 상한 경계를 설정
	- 상한 경계가 지정된 경우, 하위 타입을 특정할 수 없으므로 새로운 원소를 추가하는 것이 불가능
	- 공변
- 하한 경계 와일드 카드
	- `<? super MyParent>`
		- MyParent와 unknown의 MyParent 부모 타입들 가능
	- super를 사용해 와일드카드의 최하위 타입을 정의하여 하한 경계 설정
	- 해당 컬렉션에는 MyParent의 자식 타입이라면 원소를 안전하게 컬렉션에 추가 가능
	- 반공변

<br>
<br>

```java
<K extends T>	// T와 T의 자손 타입만 가능 (K는 들어오는 타입으로 지정 됨)
<K super T>	// T와 T의 부모(조상) 타입만 가능 (K는 들어오는 타입으로 지정 됨)
 
<? extends T>	// T와 T의 자손 타입만 가능
<? super T>	// T와 T의 부모(조상) 타입만 가능
<?>		// 모든 타입 가능. <? extends Object>랑 같은 의미
```

<br>

**주의점**

- `K extends T`와 `? extends T`는 비슷한 구조지만 차이점이 있음
- '유형 경계를 지정'하는 것은 같으나, 경계가 지정되고 K는 특정 타입으로 지정이 되지만, ?는 타입이 지정되지 않는다는 의미

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
따라서 제네릭의 super를 통해 위의 에러를 사전에 방지한다.

<br>
<br>

### 와일드 카드사용시 주의점
**비한정적 와일드 카드**
제네릭의 범위를 extend와 super로 제한하는 것이 아닌 <?>만으로 구성하면 안된다!
```java
public MyArrayList(Collection<?> in) {
    for (T elem : in) {
        element[index++] = elem;
    }
}

public void clone(Collection<?> out) {
    for (Object elem : element) {
        out.add((T) elem);
    }
}
```
?는 어떤 타입이든 올수 있지만, 어떤 타입이든 올수 있기 때문에 컴파일러 입장에서 매개변수를 꺼내거나 저장할시 타입을 알지못해 논리적 에러가 발생하게 된다.

**<T super 타입>**
<T super 타입>의 경우 무수히 많은 클래스와 인터페이스가 올수 있기때문에 Object와 다르지 않아 사용하지 않는다.


<br>
<br>

### PECS(Producer-Extends, Consumer-Super) 공식
- 이펙티브 자바에서 만든 공식
- Producer-Extends
	- 컬렉션으로부터 와일드카드 타입의 객체를 꺼내서 생성하면(produce) extends 사용
- Consumer-Super
	- 갖고 있는 객체를 컬렉션에 사용(consumer)하여 더하면 super를 사용

```java
	void printCollection(Collection<? extends MyParent> c) {
		for (MyParent e : c) {
			System.out.println(e);
		}
	}

	void addElement(Collection<? super MyParent> c) {
		c.add(new MyParent());
	}
```



<br>
<br>
<br>




## 제네릭 메서드

<img src="https://github.com/sejong-bucket/sejong-bucket-v0/assets/74135929/207752c5-0389-49b2-a0a1-c7059f3a816b" width=500>

<br>

### 개념
- 메소드의 선언 부에 적은 제네릭으로 리턴 타입, 파라미터의 타입이 정해지는 메소드
- 메서드의 선언부에 `<T>`가 선언된 메서드
- 직접 메서드에 `<T>` 제네릭을 설정함으로서 동적으로 타입을 받아와 사용할 수 있는 독립적으로 운용 가능한 메서드

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

- 제네릭 메서드인 genericMethod()는 파라미터의 타입에 따라 T타입이 결정됨
- 결과를 확인하면 같은 a인스턴스이지만 genericMethod의 파라미터에 따라 타입이 달라진것을 확인할수 있음

<br>
<br>

### 특징
- 제네릭 클래스와 독립적
	- 형식과 사용이 제너릭 클래스와 똑같지만, 클래스의 `<T>`와 제너릭 메소드의 `<T>`는 다름
	- 제네릭 메소드는 그 메소드를 포함하고 있는 클래스가 제네릭인지 아닌지 상관없음
	```java
		class Student<T>{
			public T getOneStudent(T id){ return id; }  // 1
			public <T> T getId(T id){return id;} // 2 제네릭 클래스의 T와 다름  
			public <S> T toT1(S id){return id; }  // 3
			public static <S> T toT2(S id){return id;}  // 4 에러 
		}
	```
	- 1번: 클래스의 제너릭 타입 T를그대로 사용하는 경우
	- 2번: 클래스의 제너릭 타입 T와 제너릭 메소드 타입 T는 다름
	- 3번: static 메소드가 아닌 일반메소드기 때문에 클래스의 타입과 제너릭 메소드의 타입 같이 사용가능
	- 4번: static 메소드기 때문에 클래스의 제너릭 타입 T를 사용하기 때문에 에러 발생

<br>
<br>

### 제네릭 메서드 사용이유
- 제네릭 매소드는 호출 시에 매게 타입을 지정하기 때문에 Static이 가능함
	- **[정적(static)](https://github.com/jmxx219/CS-Study/blob/main/java/static%20class%EC%99%80%20static%20method.md) 메서드**를 선언할때 필요
	- static키워드가 붙으면 프로그램 실행시 메모리에 올라감
	- 외부에서 타입을 결정해주는 제네릭의 특성상 인스턴스 생성이 되기 전까지 타입 결정이 되지 않아 에러가 날수 있음

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
	따라서 static의 경우 제네릭 타입을 메서드의 파라미터를 통해 지정하여 에러를 막을수 있다! 



<br>
<br>


### Ref
[[Java] 제네릭과 와일드카드 타입에 대해 쉽고 완벽하게 이해하기(공변과 불공변, 상한 타입과 하한 타입)](https://mangkyu.tistory.com/241)								

[[Java] 제네릭 메소드(Generic Method)란?](https://devlog-wjdrbs96.tistory.com/201)									

[ 자바 제네릭(Generics) 개념 & 문법 정복하기](https://inpa.tistory.com/entry/JAVA-☕-제네릭Generics-개념-문법-정복하기)
