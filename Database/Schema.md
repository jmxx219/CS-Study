# 스키마 (Schema)

* 스키마는 데이터베이스의 구조와 제약 조건에 관하여 전반적인 명세를 기술한 메타데이터의 집합이다. 
* 데이터 개체(Entity), 속성(Attribute), 관계(Relationship) 뿐만 아니라 데이터들의 제약 조건까지 포함되어 있다. 
* 속성의 데이터 타입을 나타내지는 않는다. 
* 스키마는 시간에 따라 불변인 특징을 가지고 있다.



<br>

## 스키마의 3계층

스키마는 사용자의 관점에 따라 3계층으로 나뉜다.  


### 외부 스키마 (View Schema)

* 사용자 관점에서의 논리적인 구조이다. 
* 사용자와 데이터베이스 간의 상호 작용을 정의할 수 있는 뷰 레벨이다. 
* 전체 데이터베이스의 한 논리적인 부분으로 볼 수 있으므로 서브스키마라고도 불린다.
* 때문에 하나 이상의 외부 스키마가 존재할 수 있으며 하나의 외부 스키마를 여러 개의 응용 프로그램이나 사용자가 사용할 수 있다.  

### 개념 스키마 (Logical Schema)

* 데이터베이스의 전체적인 **논리적인 구조**이다. 
* 모든 응용 프로그램이나 사용자들이 필요로 하는 데이터를 종합한 전체의 데이터베이스로 하나만 존제한다.
* 개체간의 관계와 제약 조건을 나타내고 데이터베이스의 접근 권한, 보안 및 무결성 규칙에 관한 명세를 정의한다.
* 데이터가 테이블 형식으로 저장되는 방식과 테이블의 속성이 연결되는 방식을 정의한다.

### 내부 스키마 (Physical Schema)

* 물리적 저장장치의 입장에서 본 데이터베이스 구조이며 물리적인 저장장치와 밀접한 계층이다. 
* 데이터 또는 정보가 파일 및 인덱스의 형태로 스토리지 시스템에 물리적으로 저장되는 방식을 정의한다.

<br>

<img alt="schema" height="400" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/23c2f7ea-6adb-4e3e-ace9-288b299d4e09"/>

<br>

## 데이터베이스 스키마 디자인

데이터베이스를 구성하는 방법에는 여러 가지가 있으며 비효율적인 스키마 디자인은 추가 메모리와 리소스를 관리하고 소비하기 어렵기 때문에
데이터베이스 생성에 가장 적합한 스키마 디자인을 사용해야 한다.

* **평면 모델**(Flat Model)
  * 데이터를 2차원 단일 배열로 생성한다.
  * 서로 다른 엔티티 간에 복잡한 관계가 없는 간단한 테이블 및 데이터베이스에 적합하다.

<img alt="Flat Model" height="200" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/e6f9ffb9-c0c2-4fda-91fe-2fcd1cba2a75"/>

<br>

* **계층적 모델**(Hierarchical Model)
  * 루트 데이터 노드에서 분기되는 하위 노드가 있는 트리와 같은 구조를 가진다. 
  * 가계도 또는 생물학적 분류와 같은 중첩 데이터를 저장하는 데 적합하다.

<img alt="Hierarchical Model" height="270" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/d78b84d5-87f3-4b60-820a-5a7d42ba54e8"/>

<br>

* **네트워크 모델**(Network Model)
  * 계층적 모델과 마찬가지로 데이터를 서로 연결된 노드로 취급하지만, 다대다 관계, 다대다 주기 등 보다 복잡한 연결을 허용한다. 
  * 위치 간의 상품 및 자재 이동이나 특정 작업을 수행하는 데 필요한 워크플로를 모델링하는 데 적합하다.

<img alt="Network Model" height="250" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/053ae6c5-8614-4852-87d5-10296d67a028"/>

<br>

* **관계형 모델**(Relational Model)
  * 일련의 테이블, 행, 열로 데이터를 구성하고 서로 다른 엔티티 간의 관계를 사용한다. 
  * 관계형 데이터베이스에 사용되며 객체 지향 프로그래밍에 적합하다.

<img alt="Relational Model" height="320" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/111831ac-febd-48f1-9156-2dd0b2f48d93"/>

<br>

* **스타 스키마**(Star Schema)
  * 데이터를 "팩트"와 "차원"으로 구성하는 관계형 모델의 발전된 형태이다.
  * 팩트 데이터는 숫자(예: 제품 판매량)이고 차원 데이터는 설명(예: 제품 가격, 색상, 중량)입니다.

<img alt="Star Schema" height="290" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/d80dfda8-a810-4cd5-8f21-7f9dffaa50ae"/>

<br>

* **눈송이 스키마**(Snowflake Schema)
  * 스타 스키마를 추가로 추상화 한 형태이다.
  * 팩트 테이블은 자체 차원 테이블이 있을 수도 있는 차원 테이블을 가르키며 데이터베이스 내에서 가능한 설명을 확장한다.

<img alt="Snowflake Schema" height="400" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/d665b502-66b6-4d85-8431-248424b0cbf4"/>



#### ref

[[DB기초] 스키마란 무엇인가?](https://coding-factory.tistory.com/216)  
[Database Schemas](https://www.geeksforgeeks.org/database-schemas/)  
[스키마](https://itwiki.kr/w/%EC%8A%A4%ED%82%A4%EB%A7%88)  
[데이터베이스 스키마 설계에 대한 전체 가이드](https://www.integrate.io/ko/blog/complete-guide-to-database-schema-design-ko/)  
[6 Database Schema Designs and How to Use Them](https://www.integrate.io/blog/database-schema-examples/)