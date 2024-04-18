# Flyway

* Flyway란 오픈소스 데이터베이스 마이그레이션 툴이다.
* 다시말해 데이터베이스의 DDL 이력을 쌓아서 관리하는 툴이다. 
* 이를 통해 데이터베이스 형상관리 및 마이그레이션을 할 수 있다.


<br>

## 사용 이유

* 개발을 진행하다보면 엔티티의 구조가 변경되고 이로 인해 데이터베이스의 스키마도 변경되는 일이 발생한다.
* 소프트웨어 소스 코드는 Git과 같은 형상관리 툴을 사용하여 버전관리를 진행했다면, 데이터베이스는 그러지 못했다.
* 일일히 스키마 수정을 위한 **DDL**을 각 환경별로 모두 실행해주어야 했다.
* 로컬 개발 환경이나 개발 서버에서는 하이버네이트 설정 중 `ddl-auto` 에서 `create`나 `create-drop`, `update` 옵션을 사용하여 엔티티 구조를 맞춰 실행이 가능하나,
* 운영 환경에서는 해당 설정이 불가능하다.
* 하지만 flyway를 사용한다면 이러한 문제들을 해결할 수 있다. 


<br>

## flyway 사용 장점

1. 버전 관리 및 히스토리 추적
   * 각각의 데이터베이스 스키마 변경이 버전으로 기록되고, 변경이력을 추적할 수 있다.
2. 자동 마이그레이션
   * flyway는 어플리케이션이 시작될 때 자동으로 마이그레이션을 수행한다.
   * 이를 통해 개발자가 수동으로 데이터베이스 변경을 관리하지 않아도 된다.
3. CI/CD 파이프라인 통합
   * Flyway는 CI/CD 파이프라인에 자동 마이그레이션을 통합할 수 있어, 데이터베이스 변경과 애플리케이션 배포를 함께 관리할 수 있다.

<br>

## flyway 동작 방식

<br>

<br>
<img alt="image" height="230" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/06b7a0ea-4e28-40aa-8232-c22ff3eef587"/>
<br>
<br>

* 두 개의 마이그레이션(데이터베이스 스키마 변경 sql이 기록된)을 가지고 있고, 데이터베이스는 비어있다.
* flyway를 통해서 마이그레이션을 처리하고자 할 때 대상 데이터베이스에 `SCHEMA_VERSION` table이 존재하는지 확인하고 없다면 신규로 생성한다.
* 이 테이블은 flyway가 스키마 변경 이력을 관리하는 메타데이터 테이블이다.


<br>
<img alt="image" height="150" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/502addf4-7727-4f3f-ad82-796a1851c2b2"/>
<br>


* flyway는 마이그레이션을 위해 지정된 파일(sql or java)을 지정된 classpath에서 탐색하여 버전 순서대로 실행한다.

<br>
<img alt="image" height="250" width="900" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/0477cd0d-fcc9-4535-9e41-2edb37581233"/>
<br>

<br>

* 마이그레이션이 실행되고 나면 `SCHEMA_VERSION` table에 실행 이력을 저장하게 된다.
* 테이블에서 버전정보, 체크섬, 성공여부등을 확인할 수 있다.

<br>
<img alt="image" height="110" width="800" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/71ca41d5-25aa-4678-addd-8c5ad9e53180"/>
<br>


<br>

## 마이그레이션 파일명 규칙

* flyway는 마이그레이션 대상 파일명과 경로로 파일을 구분하고 스캔한다.
* `/resources/db/migration`에 존재하는 파일들을 스캔한다. 해당 경로는 변경이 가능하다.
* 이 경로 변경을 활용하여 아예 다른 데이터베이스의 스키마 변경도 진행할 수 있다. (h2에서 MySql으로)


<br>

<img alt="네이밍" height="200" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/9bae075b-d891-40fd-8662-63b1e09ffa8b"/>

<br>


* `prefix`
  * **V** 버전 마이그레이션 (Versioned Migrations)
  * **U** 취소 마이그레이션 (Undo Migrations)
  * **R** 반복 마이그레이션 (Repeatable Migrations)
* `version`
  * 버전을 나타낸다.
  * 버전 마이그레이션과 취소 마이그레이션에서만 사용한다. 반복 마이그레이션에서 사용시 에러
  * 숫자와 `점(.)` 그리고 `언더바(_)` 조합으로 구성된다.
* `separator`
  * 설명 부분을 구분하기 위한 구분자이며 반드시 언더바 2개를 사용해야 한다. `__`
* `description`
  * 이 부분은 스키마 버전 테이블에 저장시 설명으로 사용된다.
* `suffix`
  * 확장자 기본은 **.sql**

<br>

### 버전 마이그레이션 Versioned Migrations

* Flyway의 핵심 기능이다.
* 마이그레이션 스크립트의 최신 버전과 현재 데이터베이스의 스키마 버전을 비교하고, 차이점이 있다면 마이그레이션 스크립트를 순차적으로 실행하여 최신 스키마와 격차를 좁힌다.
* 예시)
  * 마이그레이션 버전이 1부터 9까지 있다고 가정
  * 현재 개발환경의 데이터베이스 스키마 버전이 5일 때,
  * 최신 버전에 대해 마이그레이션을 실행하면,
  * 마이그레이션 스크립트 6부터 9가 순차적으로 실행된다.


<br>

### 취소 마이그레이션 Undo Migrations

* Flyway Teams 유료 버전에서만 사용 가능한 기능이다.
* 마이그레이션을 롤백하는 스크립트를 작성해야 한다.
* 이 스크립트는 마이그레이션의 작업을 반대 방향으로 데이터베이스 스키마를 변경한다.
* 프로덕션 환경에서는 신중하게 사용해야 한다. 
* 롤백이 실제로 수행되기 전에는 항상 백업을 만들고, 롤백 후에는 데이터의 일관성을 확인하는 것이 좋다.

<br>

### 반복 마이그레이션 Repeatable Migrations

* 모든 마이그레이션 스크립트가 실행된 이후 실행되는 스크립트이다.
* 반복 마이그레이션끼리는 description 순서대로 실행된다.
* 한번 실행되며, 파일이 변경되어 체크섬이 변경된경우 다시 실행된다.


<br>

## 실행명령 Command

* 무료 버전의 flyway에서 제공하는 명령어는 6가지이다.
* 유료 버전에는 check, snapshot, undo를 제공한다.

<br>

* **Migrate**
  * 데이터베이스 마이그레이션

<br>

<img alt="migrate" height="180" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/6de5db3b-06a7-437b-8988-ddb6e803a396"/>

<br>


* **Info**
  * 모든 마이그레이션 상세정보 출력
  * 마이그레이션의 성공여부와 실행 시간 확인 가능

<br>

<img alt="info" height="180" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/77c1c04b-e31e-4eb9-87d0-f3cd49f6b0ad"/>

<br>


* **Clean**
  * 데이터베이스의 schema_version 테이블을 포함한 모든 테이블, 뷰, 프로시저등을 drop
  * 운영환경에서 실행주의

<br>

<img alt="clean" height="180" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/efbe9f07-0066-4ea0-a172-fab113a8efae"/>

<br>

* **Validate**
  * 데이터베이스에 적용된 마이그레이션 정보의 유효성 검증

<br>

<img alt="validate" height="180" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/a2e88806-e5a1-4ac2-9ff5-dacaf3c90044"/>

<br>


* **Baseline**
  * 특정 버전에서 기존 데이터베이스에 flyway를 도입하기 위해 사용
  * 베이스라인 버전까지의 모든 마이그레이션은 무시

<br>

<img alt="baseline" height="180" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/b2798bd7-7c12-4aae-8713-0127bfc4445f"/>

<br>

* **Repair**
  * flyway_schema_table의 이슈를 수리하기 위해 사용
  * 방식은 두가지
    * 실패한 마이그레이션 항목 제거 (DDL 트랜잭션을 지원하지 않은 데이터베이스만 해당 ex) SQLite, MySQL, Oracle)
    * 적용된 마이그레이션의 체크섬, 설명, 타입들을 사용 가능한 마이그레이션의 체크섬으로 재정렬

<br>

<img alt="repair" height="180" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/d429f048-7dcd-449c-a0a8-3ba9972df5f4"/>

<br>

<br>

---

#### ref

* [Flyway docs](https://documentation.red-gate.com/fd/commands-184127446.html)
* [Flyway](https://tecoble.techcourse.co.kr/post/2021-10-23-flyway/)
* [Flyway와 DB Migration / 우리 팀에서 Flyway를 사용하는 이유](https://ecsimsw.tistory.com/entry/Flyway%EB%A1%9C-DB-Migration)
* [Spring docs](https://docs.spring.io/spring-boot/docs/current/reference/html/howto.html#howto.data-initialization.migration-tool.flyway)
* [나만 모르고 있던 - Flyway (DB 마이그레이션 Tool)](https://www.popit.kr/%EB%82%98%EB%A7%8C-%EB%AA%A8%EB%A5%B4%EA%B3%A0-%EC%9E%88%EB%8D%98-flyway-db-%EB%A7%88%EC%9D%B4%EA%B7%B8%EB%A0%88%EC%9D%B4%EC%85%98-tool/)
