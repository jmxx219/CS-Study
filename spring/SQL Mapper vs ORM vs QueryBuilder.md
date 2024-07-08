# SQL Mapper vs ORM vs QueryBuilder

<br>

## SQL Mapper 

- Spring에서 사용하는 SQL Mapper로는 대표적으로 MyBatis가 있음
  - SQL Mapper는 XML에 SQL을 작성하고, Java 코드에서 SQL을 호출하는 방식
  - 프로젝트의 기본적인 구조가 Controller.java -> Service.java -> Mapper.java -> Mapper.xml로 이루어지는 구조
  - 아래와 같이 Mapper.xml에 실제 SQL 쿼리를 작성함

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.example.mapper.UserMapper">
  <insert id="insertUser">
      INSERT INTO users (username, email) VALUES (#{params.username}, #{params.email})
  </insert>
</mapper>
```

  - Mapper.java에는 Mapper.xml에 작성한 SQL 쿼리를 호출하는 메서드를 작성함

```java
@Mapper
public interface UserMapper {
    @Insert("INSERT INTO users (username, email) VALUES (#{username}, #{email})")
    void insertUser(@Param("username") String username, @Param("email") String email);
}
```

<br>

### MyBatis with Custom DAO

@Mapper 없이 DAO를 사용한다면 아래와 같이 사용할 수 있음

- SQL Session Factory 

```java
@Configuration
public class MyBatisConfig {

    @Bean
    public SqlSessionFactory sqlSessionFactory(DataSource dataSource) throws Exception {
        SqlSessionFactoryBean sessionFactory = new SqlSessionFactoryBean();
        sessionFactory.setDataSource(dataSource);
        sessionFactory.setMapperLocations(new PathMatchingResourcePatternResolver().getResources("classpath:/mappers/*.xml"));
        return sessionFactory.getObject();
    }

    @Bean
    public SqlSessionTemplate sqlSessionTemplate(SqlSessionFactory sqlSessionFactory) {
        return new SqlSessionTemplate(sqlSessionFactory);
    }
}
```

- Custom DAO 

```java
@Repository
public class UserDao {
    private final SqlSession sqlSession;

    @Autowired
    public UserDao(SqlSession sqlSession) {
        this.sqlSession = sqlSession;
    }

    public void insertUser(User user) {
        sqlSession.insert("org.example.mapper.UserMapper.insertUser", user);
    }
}
```

- Mapper 방식 
  - 장점 
    - SQL 매핑이 인터페이스에 직접 명시되므로, SQL 쿼리와 Java 메서드 간의 관계가 명확하고, 리팩토링이나 코드 탐색이 용이함
    - Spring Framework와의 통합이 용이하여, 스프링의 의존성 주입, 트랜잭션 관리 등의 기능을 자연스럽게 활용할 수 있음
  - 단점
    - @Mapper 애너테이션을 사용할 때는 MyBatis가 제공하는 방식을 따라야 하므로, 사용자 정의 처리가 필요할 때 제약이 따름
    - SQL과 밀접하게 연결되어 있어, 데이터베이스 로직이 비즈니스 로직에 침투할 가능성이 있음
      - 👉 Repository(DAO)로 감싸는 것으로 이 단점을 해소할 수 있으나, Depth가 깊어질 수 있음
- Custom DAO 방식
  - 장점
    - 데이터베이스 액세스 로직을 완전히 추상화할 수 있음
    - 공통의 DAO 인터페이스를 정의하고, 다양한 구현체를 통해 다른 데이터베이스 기술로 쉽게 전환가능
  - 단점
    - DAO를 사용하면 추가적인 인터페이스와 구현 클래스를 작성해야 하므로, 프로젝트의 코드 베이스가 커지고 복잡해짐
    - @Mapper 애너테이션을 사용할 때보다 개발 속도가 느릴 수 있으며, 데이터베이스 연결과 관련된 코드를 직접 관리해야 함

<br>

### Mapper도 DAO인가?

Mapper는 Data Access에 대한 모든 역할을 담당하지는 않음. 
Mapper는 XML을 감싸는 Interface Adapter 역할을 함.
하지만, DAO가 BusinessLogic과 독립적으로 데이터 액세스에 관한 책임을 가지는 오브젝트라는 관점에서 볼 때, Mapper 또한 **DAO라고 볼 수 있을 것 같음** (동일한 관점에서 볼 때 JPARepository 또한 DAO라고 볼 수 있음)

<br>
<br>

## ORM 

[ORM 참고](https://github.com/jmxx219/CS-Study/blob/main/spring/ORM.md)

<br>
<br>

## QueryBuilder 

- SQL 쿼리 빌더는 원시 데이터베이스 네이티브 쿼리 언어 위에 추상화 계층을 추가 함 
  - 쉽게 말해서 사용하는 프로그래밍 언어로 작성되고 네이트비 클래스 및 함수를 사용하는 라이브러리를 Qeury Builder라고 할 수 있음
- SQL 네이티브 쿼리 언어를 함수로 호출해서 사용한다는 점에 데이터 베이스와 비교적 가깝다고 할 수 있음

```java
    public List<Post> searchByPostIdWithComment(Long postId) {
        JPAQuery<Post> query = queryFactory.selectFrom(post)
                .where(post.id.eq(postId))
                .leftJoin(post, comment.post)
                .on(post.id.eq(comment.post.id))
                .fetchJoin();
        return query.fetch();
    }

```

<br>
<br>

## 총평 

- Mapper 
  - 장점
    - 직접 SQL을 작성하므로, ORM에 비해 성능 최적화가 용이
    - SQL 쿼리에 대한 완전한 제어가 가능하며 특정 데이터 베이스에 대한 최적화 기능 활용 가능
  - 단점
    - 파라미터 명이 틀리면 쿼리 실행 중에 오류가 남
    - 문자열로 SQL을 작성하므로, 오타가 발생할 수 있음
    - 중복된 코드가 많아질 수 있음
    - ORM이나 QueryBuilder에 비해서 Depth가 깊어짐
- ORM 
  - 장점
    - 객체지향 프로그램을 자연스럽게 활용 가능
    - 기본적인 CRUD는 자동으로 생성되므로, 개발 속도가 빠름
    - 데이터 베이스 스키마와 맞지 않으면 처음 실행하는 시점에 오류 발견 가능
  - 단점
    - 복잡한 쿼리의 경우 ORM이 생성하는 SQL이 최적화되지 않을 수 있어 성능저하가 발생
    - 복잡한 쿼리 작성이 어려움
    - N + 1 문제가 발생하며 해결하기 힘듬 
- QueryBuilder
  - 장점
    - 프로그래밍 언어의 구문을 활용하여 SQL 쿼리를 구성하므로 직관적임
    - 쿼리 조각을 프로그래밍 방식으로 쉽게 재사용하고 조합할 수 있음 (Procedure 처럼 사용 가능)
    - N + 1 문제에서 자유로움 (N + 1 문제를 고의로 발생시킬 수도 있고 N + 1문제가 발생안하게 할 수도 있음)
  - 단점
    - SQL을 직접 작성하지 않기 때문에 때때로 생성된 쿼리가 예상과 다를 수 있음
    - 컴파일 타임 위빙 방식을 사용하기 때문에 세팅이 번거롭고 꼬일 수 있음
    - build/*.class 파일이 잘못생성되면 제대로 작성된 코드도 동작하지 않을 수 있음

<br>
<br>

## Reference 

- [DAO와 Mapper의 차이](https://doongjun.tistory.com/43)
- [SQL vs ORM vs QueryBuilder prisma 사이트](https://www.prisma.io/dataguide/types/relational/comparing-sql-query-builders-and-orms)
- [마틴 파울러 Data Mapper 패턴](https://martinfowler.com/eaaCatalog/dataMapper.html)
- [마틴 파울러 Repository 패턴](https://martinfowler.com/eaaCatalog/repository.html)