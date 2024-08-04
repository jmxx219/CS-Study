## N+1 Problem

N + 1 문제란 연관관계가 설정된 엔티티를 조회할 때 연관된 엔티티를 조회하는 쿼리가 N + 1번 발생하는 문제임.

## 왜 발생할까? 

- JPA와 같은 ORM에서는 연관된 엔티티를 Proxy 객체로 Lazy Loading을 함.
  - Proxy 객체 : 실제 객체를 대신하여 사용자의 요청이 있을 때 실제 객체를 생성하여 반환하는 객체.
  - Lazy Loading : 연관된 엔티티를 조회하는 시점에 쿼리를 실행하여 조회하는 방식.
- 지연로딩을 사용하는 이유?
  - 불필요한 데이터를 조회하지 않기 위함.
  - 일대다의 연관관계의 경우 많은 데이터를 한 번에 조회하게 되면 DB 성능에 문제가 발생 할 수 있음.

### N + 1 예시 코드 

아래와 같은 Entity가 있다고 가정할 때, 

```kotlin filename="" copy showLineNumbers
@Entity(name = "post")
data class Post (
    @field:Id
    @field:GeneratedValue(strategy = GenerationType.IDENTITY)
    val id: Long? = null,
    val title: String,
    val content: String,
    val author: String,
    @field:OneToMany(mappedBy = "post", fetch = FetchType.LAZY)
    val comments: List<Comment> = listOf()
)
```

**/api/posts/\{id\}** API를 호출하면 PostResponse를 1개 반환하고 이 API를 사용할 때 발생한 쿼리는 아래와 같음.

<img src="/paste-image/n-plus-one-problem/2024-08-04-14-40-59.png" width="75%" />

위 쿼리를 보면 Post를 조회하기 위한 쿼리가 1번 Comment를 조회하기 위한 쿼리가 1번 발생할 수 있음. 이를 N + 1 문제라고 함.

### 왜 문제가 될까?

예를 들어서 Post가 20개 정도있고 Comment가 2000개 정도 있다고 가정할 때, Post를 조회하는 API를 호출하면 20개의 Post를 조회하는 쿼리가 발생하고, 각 Post마다 Comment를 조회하는 쿼리가 20번 발생하게 됨. 이렇게 되면 총 40번의 쿼리가 발생하게 되어 DB에 부하가 걸리게 됨. <br></br>
이러한 경우, DB의 성능이 충분하다면 단 한 번의 조인으로 모든 데이터를 조회하는 것이 효율적임.

<img src="/paste-image/n-plus-one-problem/2024-08-04-14-53-52.png" width="100%" />

## 해결 방법

### 🚨 fetchType.EAGER을 사용하면 안됨!!

```kotlin filename="" copy showLineNumbers
    fun findAll(): List<PostResponse> {
        return postRepository.findAll().map{
            logger.info("Comment 조회 전")
            val result = PostConverter.toResponseUseRepository(it)
            logger.info("Comment 조회 후")
            result
        }
    }

```

현재 Comment가 객체탐색되는 코드는 위 코드에서 **toResponseUseRepository 부분**임. 그래서 해당 라인 앞 뒤로 log를 찍어보면 Lazy Loading에서는 아래와 같은 결과가 나옴.

<img src="/paste-image/n-plus-one-problem/2024-08-04-15-23-41.png" width="100%" />

하지만 EAGER로 설정하면 아래와 같은 결과가 나옴.

<img src="/paste-image/n-plus-one-problem/2024-08-04-15-24-54.png" width="100%" />

위와 같은 결과가 나오는 이유는 Post N개를 조회하는 상황에서는 Lazy와 Eager의 차이는 **Comment가 조회되는 시점에 Comment를 조회하는지(Lazy), Post를 조회하는 시점에 Comment를 조회하는지(Eager)의 차이**이기 때문임. 

> Post 1개를 조회하는 상황에선 FetchType.EAGER을 사용하면 N + 1문제가 발생하지 않고 Left Join이 걸림.

> [참조 PR : Post.comments fetchType Eager Lazy 비교](https://github.com/rookedsysc/n-plus-one-example/pull/9)


### FetchJoin 사용

**\@Query Annotation**을 사용해서 FetchJoin을 사용하는 방법.

```kotlin filename="" copy showLineNumbers
    @Query("SELECT p FROM post p JOIN FETCH p.comments")
    fun findAllPostFetchJoinComments(): List<Post>
```

- 👇 실제 발생 쿼리 

```sql filename="" showLineNumbers copy
Hibernate: select p1_0.id,p1_0.author,c1_0.post_id,c1_0.id,c1_0.author,c1_0.content,p1_0.content,p1_0.title from example.post p1_0 join example.comment c1_0 on p1_0.id=c1_0.post_id
```

### BatchSize 

- Lazy Loading시 프록시 객체를 조회할 때 where in절로 묶어서 한번에 조회 할 수 있게 해주는 옵션.
- yml에 전역 옵션으로 적용할 수 있고 @BatchSize를 통해 연관관계 BatchSize를 다르게 적용가능.
- Batch Size 100~1000정도로 적용하고 DBMS에 따라서 where in 절은 1000까지 제한하는 경우가 있으므로 1000이상은 설정을 잘 하지않음.

<img src="/paste-image/n-plus-one-problem/2024-08-04-16-46-26.png" width="100%" />

> [참조 PR](https://github.com/rookedsysc/n-plus-one-example/pull/14)

### EntityGraph 사용 

- **\@EntityGraph Annotation**을 사용하면 EntityGraph의 로딩을 기본 Lazy에서 Eager로 변경할 수 있음.
- EntityGraph를 사용하는 경우 **outer left join을 수행**하여 데이터를 가져옴.

```kotlin filename="" copy showLineNumbers
    @EntityGraph(attributePaths = ["comments"])
    override fun findAll(): List<Post>
```

> [참조 PR](https://github.com/rookedsysc/n-plus-one-example/pull/17)

### 그 외 방법들

MyBatis나 QueryDSL 같은 QueryBuilder를 사용해서도 N + 1 문제를 해결할 수 있음.

## 결론

- DB 성능을 고려하고 쿼리를 잘게 쪼갤 수 있는 방식인 **\@BatchSize Annotation**을 사용하는 방식이 적절하다고 느껴짐.
- 1:1 관계의 경우에서는 FetchType.EAGER을 사용해도 큰 문제가 없음.
- N + 1 문제가 과연 문제가 맞는지?도 고려해봐야 할 대상임.

## Referenec 

- [N + 1 Problem, Incheol's Tech Blog](https://incheol-jung.gitbook.io/docs/q-and-a/spring/n+1#n1-1)
- [JPA N+1 문제와 해결법 총정리, teahee kim's velog](https://velog.io/@xogml951/JPA-N1-%EB%AC%B8%EC%A0%9C-%ED%95%B4%EA%B2%B0-%EC%B4%9D%EC%A0%95%EB%A6%AC)
- [SpringBoot / JPA] JPA Batch Size에 대한 고찰](https://velog.io/@joonghyun/SpringBoot-JPA-JPA-Batch-Size%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B3%A0%EC%B0%B0)