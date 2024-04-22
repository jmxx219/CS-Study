# Collections

## Collections Framework

JDK 1.2 전에는 다수의 데이터를 저장할 수 있는 클래스들을 서로 다른 방식으로 처리해야 했으나, JDK1.2 부터 컬렉션 클래스로 표준화된 방식으로 다룰 수 있게 체계화되었다.

<img src="https://github.com/jmxx219/CS-Study/assets/74135929/ab0b996b-cb92-4b6e-a329-8ca9155e9e59" alt="Collections">

`List` 와 `Set`을 구현한 컬렉션 클래스들은 공통 부분이 많아서, `Collection` 인터페이스로 정의할 수 있었지만 `Map` 인터페이스는 Key-Value 형태로 컬렉션을 다루기 때문에 같은 상속 계층도에 포함되지 못한다.

<br>

Collection 인터페이스를 보면 제네릭을 사용한 것을 볼수 있다. 제네릭은 Reference타입만을 할당받을 수 있기 떄문에 Collection의 구현클래스를 선언할때 Primitive타입은 사용이 불가하다!

```
public interface Collection<E> extends Iterable<E>
```

<br>

## [List](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/List.html)

<img src="https://github.com/dragonappear/java-101/assets/89398909/ed56b928-6727-4d34-b513-e87709f52635" height="400">

- 데이터의 저장 **순서**가 유지되고, **중복**을 허용함
- 구현 클래스: `AraryList`, `LinkedList`
    - `Vector`, `Stack` 등은 컬렉션 프레임워크가 만들어지기 이전부터 존재하던 것이기 때문에 명명법을 따르지 않음.
    - `Vector`와 `ArrayList`는 내부 구조가 거의 동일하고, `Vector`는 `Thread-safe` 하기 때문에 멀티 스레드 환경에서 안전하게 객체를 추가, 삭제할 수 있지만, `ArrayList`는 그렇지 않다.
    - `Vector`과 같은 컬렉션들은 기존 코드와의 호환성을 위해 남겨두었고, `ArrayList`를 사용하는 것이 좋다.

<br>

### [ArrayList](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/ArrayList.html)

- Object 배열을 사용해서 데이터를 순차적으로 저장
    - `Object` 배열은 `Thread-safe` 하지 않음
- 배열 크기를 변경할 수 없음
    - 크기를 변경해야 하는 경우 새로운 배열을 생성 후 데이터를 복사해야 함
    - 크기 변경을 피하기 위해 충분히 큰 배열을 생성하면 메모리가 낭비됨
- 비순차적인 데이터의 추가, 삭제에 시간이 많이 걸림
- 배열의 크기를 늘릴 때, 현재 배열의 크기의 1.5배 크기(`oldCapacity >> 1`)로 늘림

<img src="https://github.com/dragonappear/java-101/assets/89398909/a0076234-9f30-4f90-803e-f2a43e9d8a54" height="400">

<br>

### [LinkedList](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/LinkedList.html)

- 이중 연결 리스트로 구현되어 있다. 배열과 달리 불연속적으로 존재하는 데이터를 연결한다
    - 단 한 번의 참조 변경만으로 데이터 삭제 가능
- 인덱스로 `O(1)` 참조가 가능한 배열에 비해 데이터 접근성이 나쁨

<br>

### ArraysList vs LinkedList 성능 비교

|           | ArrayList                    | LinkedList            |
|-----------|------------------------------|-----------------------|
| 구현        | 배열                           | 노드 연결                 |
| 접근 시간     | 상수 시간                        | 위치에 따라 이동 시간 발생       |
| 삽입/삭제 시간  | 삽입/삭제 자체는 상수 시간              | 삽입/삭제 자체는 상수 시간       |
|           | 삽입/삭제 시 시프트가 필요한 경우 추가 시간 발생 | 삽입/삭제 위치까지 이동하는 시간 발생 |
| 크기 확장     | 배열 확장이 필요한 경우 복사 추가 시간 발생    |                      |
| 검색        | 최악의 경우 리스트에 있는 데이터 수 만큼 확인   |        |

<br>

## [Set](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Set.html)

<img src="https://github.com/dragonappear/java-101/assets/89398909/5f65a0d1-b034-4502-8bd4-c3b5535604e6" height="400">

- 순서를 유지하지 않는 데이터의 집합, 데이터의 중복을 허용하지 않음
- 구현 클래스: `HashSet`, `TreeSet`

<br>

### [HashSet](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/HashSet.html)

- 저장 순서를 유지 하지 않음
    - 저장 순서를 유지하고자 한다면 `LinkedHashSet`을 사용하면 됨.
- 순서가 없으니 정렬을 할 수 없음.
    - 정렬을 하고자 한다면 `Set`의 모든 요소를 `List`에 저장해서 정렬하거나 `TreeSet`을 사용하면 됨.
- 객체를 저장하기(`boolean add(Object o)`) 전에 기존에 같은 객체가 있는지 확인
    - 같은 객체가 없으면 저장하고, 있으면 저장하지 않는다
- 같은 객체를 확인하는 것은 `hashCode()` 메서드를 호출해서 해시코드를 얻어냄.
    - 같은 해시코드를 가진 객체가 있다면 `equals()` 메서드로 두 객체를 비교해서 `true`가 나오면 같은 객체로 판단하고 저장하지 않음.
    - 제대로 저장하려면 `equals()` 와 `hashCode()`를 목적에 맞게 재정의해야함.
- 내부적으로 `HashMap`을 사용함. `HashSet`은 `Map` 키에 해당하는 부분에 객체를 저장하고 값에는 더미 객체를 사용함.

<br>

### [TreeSet](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/TreeSet.html)

<img src="https://media.geeksforgeeks.org/wp-content/cdn-uploads/20221215114732/bst-21.png" height="300">

- `Binary Search Tree`로 구현되어 있음.
- 범위 검색과 정렬에 유리한 집합
- `HashSet` 보다 데이터 추가, 삭제에 시간이 더 걸림
- 내부적으로 `TreeMap`을 사용함. `Treㄷset`은 `Map` 키에 해당하는 부분에 객체를 저장하고 값에는 더미 객체를 사용함.
- BST 자료구조에 저장되기 때문에 객체 간의 비교가 가능해야 함
    - 저장되는 객체가 `Comparable` 나 `Comparator` 인터페이스를 구현하고 있어야 함
    - 혹은 생성자에 `Comparator` 객체를 넘겨줘야 함 (`TreeSet(Comparator comparator)`)

<br>  

#### TreeSet API

- `subSet(Object fromElement, Object toElement)` : 범위 검색의 결과를 반환
- `headSet(Object toElement)` : 지정된 객체보다 작은 객체들을 반환
- `tailSet(Object fromElement)` : 지정된 객체보다 큰 객체들을 반환
- 그 외 기타 API 는 공식문서 참고

<br>

### Java List vs Set

|           | List                  | Set                             |
|-----------|-----------------------|---------------------------------|
| 구현체       | ArrayList, LinkedList | HashSet, LinkedHashSet, TreeSet |
| 중복 데이터 저장 | 가능                    | 불가능                             |
| 데이터 조회 속도 | 상대적으로 느림              | 상대적으로 매우 빠름                     |
| 순서 존재     | 존재함                   | 구현체에 따라 다름                      |


<br>

## [Map](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Map.html)

<img src="https://github.com/dragonappear/java-101/assets/89398909/a57b2b2f-008f-42b6-946d-d2efadba3851" height="400">

- key-value 형식으로 데이터를 저장하는 자료구조
- `key` 는 유니크 해야함. `value`는 중복 가능
- `HashTable`과 `HashMap`의 관계는 `Vector`와 `ArrayList`의 관계와 같다.
    - `HashTable`은 `Thread-safe` 하지만 `HashMap`은 그렇지 않다.

<br>

### [HashMap](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/HashMap.html)

<img src="https://github.com/dragonappear/java-101/assets/89398909/9edaf9bd-4a80-4970-ad20-c58aa64df135">

- 해시 테이블을 사용한다
- 해시를 사용하기 때문에 보통 모든 데이터를 상수 시간에 접근한다
- 순서를 보장하지 않는다
    - 순서를 유지하려면, `LinkedHashMap`을 사용하면 된다
- 해시 충돌 시 [체이닝 방식](https://www.geeksforgeeks.org/separate-chaining-collision-handling-technique-in-hashing/)으로 해결한다

<br>

### [TreeMap](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/TreeMap.html)

- `Binary Search Tree`로 구현되어 있음.
- 범위 검색과 정렬에 유리한 집합
- `HashMap` 보다 데이터 추가, 삭제에 시간이 더 걸림

<br>

## 유용한 메서드

- `Collections.copy(List dest, List src)` : src의 내용을 dest로 복사
- `Collections.sort(List list)` : list를 오름차순으로 정렬
- `Collections.sort(List list, Comparator c)` : list를 c의 규칙으로 정렬
- `Collections.binarySearch(List list, Object key)` : 이진 검색으로 list에서 key를 찾아서 인덱스를 반환
- `Collections.reverse(List list)` : list의 내용을 반대로 정렬
- `Collections.shuffle(List list)` : list의 내용을 뒤섞음
