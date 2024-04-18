# 스키마 (Schema)

- 개념
  - 데이터베이스의 구조와 제약 조건에 관하여 전반적인 명세를 기술한 메타데이터의 집합
    - 메타 데이터: 어떤 목적을 가지고 만들어진 데이터
  - 데이터 `개체(Entity)`, `속성(Attribute)`, `관계(Relationship)` 뿐만 아니라 데이터들의 `제약 조건` 등에 관해 전반적으로 정의함
- 특징
  - 스키마는 데이터 사전(Data Dictonary)에 저장됨
    - 데이터 사전: 데이터 항목들에 대한 정보를 지정한 중앙 저장소(테이블 및 뷰들의 집합)
  - 현실 세의 특정한 한 부분의 표현으로, 특정 데이터 모델을 이용해서 만들어짐
  - 스키마는 시간에 따라 불변인 특징을 가지고 있음
  - 스키마는 데이터의 구조적 특성을 의미하며, 인스턴스에 의해 규정됨

<br/>
<br/>

## 데이터 구조 간의 사상과 데이터 독립성

<img alt="schema" width="400px" src="https://github.com/jmxx219/CS-Study/assets/52346113/a7a959ff-5e06-464b-ae85-0694de4ab86d"/>
<br/> <br/>

- 데이터베이스의 독립성을 유지하기 위해서 사상(Mapping)이라는 데이터 독립성 구현 기법을 사용함
  - 이를 이용하여 논리적, 물리적 구조가 변하더라도 응용 프로그램의 변형 없이 변경된 데이터를 사용할 수 있게 되었음
- 사진 참고) 응용 프로그램에 B라는 중복 데이터가 존재하며, 논리적 구조에 있는 B가 변경되어 B'이 되었다고 가정해보자
  - 각 응용 프로그램에서 B라는 데이터를 사용할 경우, 응용 프로그램에서는 직접 B라는 데이터를 변경된 B'로 변경하지 않고 논리적 구조에서 존재하는 B'를 Mapping하여 사용함
  - 이를 통해 논리적 구조가 변하더라도 응용 프로그램에는 영향을 끼치지 않음

<br/>

#### 데이터베이스 독립성 종류

- 논리적 데이터 독립성
  - 외부 스키마와 개념 스키마 단계의 사상으로, 응용 프로그램에 영향을 주지 않고 논리적 데이터 구조의 변경이 가능함
- 물리적 데이터 독립성

  - 개념 스키마와 내부 스키마 단게의 사상으로, 응용 프로그램에 영향을 주지 않고 물리적 데이터 구조의 변경이 가능함

<br/>
<br/>

## 스키마의 3계층

> 스키마는 데이터베이스의 독립성을 위해서 사용자의 관점에 따라 3계층으로 나누어짐

<img alt="schema" width="400px" src="https://blog.kakaocdn.net/dn/bnptbc/btrKq5J76iu/oc6nn2fyfIyHSRhpJmIsmk/img.png"/>

<br/>

### 외부 스키마 (External Schema, View Schema)

- 개념
  - 각 사용자 관점에서 정의한 논리적인 구조로, 개개인의 사용자나 응용 프로그래머가 접근하는 데이터베이스
  - 실세계에 존재하는 데이터들을 어떤 형식, 구조, 배치 화면을 통해 사용자에게 보여줄 것인가에 대해 정의함
- 특징
  - 하나 이상의 외부 스키마가 존재할 수 있으며, 하나의 외부 스키마를 여러 개의 응용 프로그램이나 사용자가 사용할 수 있음
  - 동일한 데이터에 대해서 서로 다른 관점을 정의할 수 있도록 허용함
  - 전체 데이터베이스의 한 논리적인 부분으로 볼 수 있으므로 서브스키마라고도 불림
  - 일반 사용자는 질의어(SQL)을 이용하여 DB를 쉽게 사용할 수 있음

<br/>

### 개념 스키마 (Conceptual Schema, Logical Schema)

- 개념
  - 데이터베이스의 전체 조직에 대한 **논리적인 구조**로, 모든 응용 프로그램이나 사용자들이 필요로 하는 데이터를 종합한 조직 전체의 데이터베이스
  - 기관이나 조직체의 관점에서 데이터베이스를 정의함
- 특징
  - 데이터 베이스 관리자(DBA)에 의해 구성되고, 데이터베이스 당 딱 하나 존재함
  - 개체간의 관계와 제약 조건을 나타내고 데이터베이스의 접근 권한, 보안 및 무결성 규칙에 관한 명세를 정의함
  - 데이터베이스 파일에 저장되는 데이터의 형태를 나타내는 것으로, 단순히 스키마라고 하면 개념 스키마를 의미함

<br/>

### 내부 스키마 (Internal Schema, Physical Schema)

- 개념
  - 물리적 저장장치의 입장에서 본 데이터베이스 구조로, 물리적인 저장장치와 밀접한 계층
  - 개념 스키마를 디스크 기억 장치에 물리적으로 구현하기 위한 방법을 기술한 것
- 특징
  - 실제로 데이터베이스에 저장될 레코드의 물리적인 구조를 정의하고, 저장 데이터 항목의 표현 방법과 내부 레코드의 물리적 순서 등을 나타냄
  - 시스템 프로그래머나 시스템 설계자가 보는 관점의 스키마

<br/>
<br/>

## 데이터베이스 스키마 디자인

> 데이터베이스를 구성하는 방법에는 여러 가지가 있으며, 비효율적인 스키마 디자인은 추가 메모리와 리소스를 관리하고 소비하기 어렵기 때문에 데이터베이스 생성에 가장 적합한 스키마 디자인을 사용해야 함

- **평면 모델**(Flat Model)

  <img alt="Flat Model" width="250px" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/e6f9ffb9-c0c2-4fda-91fe-2fcd1cba2a75"/>

  - 데이터를 2차원 단일 배열로 생성함
  - 서로 다른 엔티티 간에 복잡한 관계가 없는 간단한 테이블 및 데이터베이스에 적합함

- **계층적 모델**(Hierarchical Model)

  <img alt="Hierarchical Model" width="250px" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/d78b84d5-87f3-4b60-820a-5a7d42ba54e8"/>

  - 루트 데이터 노드에서 분기되는 하위 노드가 있는 트리와 같은 구조를 가짐
  - 가계도 또는 생물학적 분류와 같은 중첩 데이터를 저장하는 데 적합함

- **네트워크 모델**(Network Model)

  <img alt="Network Model" width="250px" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/053ae6c5-8614-4852-87d5-10296d67a028"/>

  - 계층적 모델과 마찬가지로 데이터를 서로 연결된 노드로 취급하지만, 다대다 관계, 다대다 주기 등 보다 복잡한 연결을 허용함
  - 위치 간의 상품 및 자재 이동이나 특정 작업을 수행하는 데 필요한 워크플로를 모델링하는 데 적합함

- **관계형 모델**(Relational Model)

  <img alt="Relational Model" width="250px" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/111831ac-febd-48f1-9156-2dd0b2f48d93"/>

  - 일련의 테이블, 행, 열로 데이터를 구성하고 서로 다른 엔티티 간의 관계를 사용함
  - 관계형 데이터베이스에 사용되며 객체 지향 프로그래밍에 적합함

- **스타 스키마**(Star Schema)

  <img alt="Star Schema" width="250px" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/d80dfda8-a810-4cd5-8f21-7f9dffaa50ae"/>

  - 데이터를 "팩트"와 "차원"으로 구성하는 관계형 모델의 발전된 형태
  - 팩트 데이터는 숫자(예: 제품 판매량)이고, 차원 데이터는 설명(예: 제품 가격, 색상, 중량)

- **눈송이 스키마**(Snowflake Schema)

  <img alt="Snowflake Schema" width="250px" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/d665b502-66b6-4d85-8431-248424b0cbf4"/>

  - 스타 스키마를 추가로 추상화 한 형태
  - 팩트 테이블은 자체 차원 테이블이 있을 수도 있는 차원 테이블을 가르키며 데이터베이스 내에서 가능한 설명을 확장함

<br/>
<br/>

### ref

[[DB기초] 스키마란 무엇인가?](https://coding-factory.tistory.com/216)  
[Database Schemas](https://www.geeksforgeeks.org/database-schemas/)  
[스키마](https://itwiki.kr/w/%EC%8A%A4%ED%82%A4%EB%A7%88)  
[데이터베이스 스키마 설계에 대한 전체 가이드](https://www.integrate.io/ko/blog/complete-guide-to-database-schema-design-ko/)  
[6 Database Schema Designs and How to Use Them](https://www.integrate.io/blog/database-schema-examples/)
