# Jackson 라이브러리
- Java에서 많이 사용되는 JSON 데이터 처리 라이브러리
- JSON 데이터를 직렬화하거나 역직렬화하는 기능을 제공



## 주요 기능
1. **데이터 바인딩(Data Binding)**

- **객체-JSON 바인딩**: JSON 문자열을 Java 객체로 변환하고(Java object deserialization), Java 객체를 JSON 문자열로 변환(Java object serialization)할 수 있습니다.

- **스트림 API**: 대용량 JSON 데이터를 스트림 방식으로 효율적으로 처리할 수 있습니다.

  

2. **어노테이션 기반 구성**

- 다양한 어노테이션을 통해 직렬화 및 역직렬화 과정을 세밀하게 제어할 수 있습니다.
- `@JsonIgnore` : 특정 필드를 직렬화 및 역직렬화 과정에서 무시하도록 지정합니다.
 ```java
public  class Person {
	private String name;

	@JsonIgnore
	private int age;

}
```
- `@JsonInclude` : 조건에 따라 필드를 포함하거나 생략할 수 있습니다.
```java
public  class Person {
	private String name;
	
	@JsonInclude(JsonInclude.Include.NON_NULL)
	private Integer age;

}
```
- `@JsonFormat` : 날짜 및 시간 형식 등을 지정할 때 사용됩니다.
```java
public  class Event {
	private String name;
  
	@JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd HH:mm:ss")
	private Date eventDate;
}
```
- `@JsonValue` : 단일 값 객체를 직렬화할 때 사용됩니다. 주로 값 객체(value object)나 열거형(enum)에 사용됩니다.
```java
public enum Status {
	SUCCESS("success"),
	FAILURE("failure");
	
	private String value;

	Status(String value) {
		this.value = value;
	}

@JsonValue
public String getValue() {
	return value;
	}
}
```
- `@JsonIgnoreProperties` : 특정 속성들을 무시하도록 클래스 레벨에서 설정할 수 있습니다.
```java
@JsonIgnoreProperties({"age", "address"})
public  class Person {
	private String name;
	private int age;
	private String address;
}
```

3. **확장성**

- 사용자 정의 직렬화기 및 역직렬화기를 작성하여 특정 객체의 JSON 변환 방식을 정의할 수 있습니다.

- 다양한 데이터 형식(CSV, XML 등)이나 서드파티 라이브러리와의 통합이 가능합니다.

  

## 구성 요소

  

1. **ObjectMapper**

- Jackson의 핵심 클래스 중 하나로, JSON과 Java 객체 간의 변환 작업을 수행합니다.

- 기본적인 직렬화 및 역직렬화 설정을 관리하고 다양한 설정 옵션을 제공합니다.

  

2. **JsonParser와 JsonGenerator**

- JSON 데이터를 스트림 방식으로 처리합니다. `JsonParser`는 JSON 데이터를 읽어들이고, `JsonGenerator`는 JSON 데이터를 생성합니다.

  

3. **Tree Model**

- `JsonNode` 클래스를 사용하여 JSON 데이터를 트리 구조로 표현합니다.

- 트리 모델을 사용하면 JSON 데이터의 특정 부분을 쉽게 탐색하고 조작할 수 있습니다.

  

## 사용 예제

  

```java
import com.fasterxml.jackson.databind.ObjectMapper;

public class JacksonExample {

	public static void main(String[] args) {

		ObjectMapper objectMapper = new ObjectMapper();

		// JSON 문자열을 Java 객체로 역직렬화

		String jsonString = "{\"name\":\"John\", \"age\":30}";

		try {

			Person person = objectMapper.readValue(jsonString, Person.class);

			System.out.println(person.getName()); // John

		} catch (Exception e) {

			e.printStackTrace();

		}

		// Java 객체를 JSON 문자열로 직렬화

		Person person = new Person("Jane", 25);

		try {

			String json = objectMapper.writeValueAsString(person);

			System.out.println(json); // {"name":"Jane","age":25}
	
		} catch (Exception e) {

			e.printStackTrace();

		}

	}

}

  

class Person {
	private String name;
	private int age;
}
