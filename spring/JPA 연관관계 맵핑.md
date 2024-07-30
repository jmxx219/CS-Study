# JPA 연관관계 맵핑

<br>

## 연관관계 매핑 기초

### ◻ 연관관계가 필요한 이유

> **객체지향 설계의 목표는 자율적인 객체들의 협력 공동체를 만드는 것이다.**  
> by 조영호(`객체지향의 사실과 오해`)

- **테이블 중심 설계의 문제점**

  - 객체를 테이블에 맞추어 데이터 중심으로 모델링하면, 협력 관계를 만들 수 없음
    - 테이블은 외래키로 조인을 사용해서 연관된 테이블을 찾지만, 객체는 참조를 사용해서 연관된 객체를 찾음
  - 따라서 연관관계가 없는 객체라면 참조 대신에 외래키를 그대로 사용하기 때문에 테이블과 객체 사이에는 큰 간격이 존재함
  
  
- 연관관계가 없는 객체 ➜ 참조 대신에 외래키를 그대로 사용함
  ```java
  @Entity
  public class Member {
      @Id @GeneratedValue
      private Long id;
      
      @Column(name = "USERNAME")
      private String name;
      
      @Column(name = "TEAM_ID")
      private Long teamId;
  }
  
  @Entity
  public class Team {
      @Id @GeneratedValue
      private Long id;
      private String name;
  }
  ```
  ```java
  Team team = new Team();
  team.setName("TeamA");
  em.persist(team);
  
  Member member = new Member();
  member.setUserName("member1");;
  member.setTeamId(team.getId());
  em.persist(member);
  
  Member findMember = em.find(Member.class, member.getId());
  Long teamId = findMember.getTeamId(); // 연관관계가 없음
  Team findTeam = em.find(Team.class, teamId);
  ```
    - 외래키 식별자를 직접 다뤄서 저장함
    - 식별자로 다시 조회하는데, 이는 객체 지향적인 방법이 아님
        - `member`를 조회했음에도 불구하고 `member`가 속한 `team`을 알기 위해서 `teamId(FK)`로 다시 조회해서 가져와야 함
        - 연관관계가 없어 객체지향스럽지 않음


<br/>

### ◻ 단방향 연관관계

#### 객체 지향 모델링

- 객체 연관관계 사용
    - Team의 Id가 아닌 Team 참조 값을 그대로 가져옴
    - Member 객체의 Team 레퍼런스와 Member 테이블의 Team_Id(FK)를 매핑함
- 객체의 참조와 테이블의 외래키를 매핑
  ```java
  @Entity
  public class Member {
      @Id @GeneratedValue
      private Long id;
        
  //    @Column(name = "TEAM_ID")
  //    private Long teamId;
        
      @ManyToOne
      @JoinColumn(name = "TEAM_ID")
      private Team team;
  }
  ```
    - 연관관계가 무엇인지(다대일), 이 관계를 정의할 때 조인하는 컬럼은 무엇인지 나타냄
- ORM 매핑
- 연관관계 저장
  ```java
  // member.setTeamId(team.getId());
  member.setTeam(team); 
  ```
    - member에 team을 참조해서 저장하면, JPA가 DB에 엔티티를 저장할 때 알아서 team 엔티티에서 `PK` 값을 꺼내 member 테이블의 `FK` 컬럼에 저장함
- 참조로 연관관계 조회 - 객체 그래프 탐색
  ```java
  Member findMember = em.find(Member.class, member.getId());
  Team findTeam = member.getTeam(); // 참조 사용해서 연관관계 조회
  ```
- 연관관계 수정
  ```java
  Team teamB = new Team(); //새로운 팀 B
  teamB.setName("TeamB");
  em.persist(teamB);
    
  member.setTeam(teamB); //회원에 새로운 팀B 설정
  ```
    - 더티 체킹을 이용한 연관관계 수정

<br/>


### ◻ 양방향 연관관계

- 테이블의 연관관계에는 외래키 하나에 양방향이 다 존재함
    - 테이블은 외래키로 조인해서 연관 관계를 만들기 때문
        - Member에서 자신이 속한 팀을 알고 싶으면 Member의 TEAM_ID(FK)와 TEAM의 TEAM_ID(PK)를 조인하면 됨
        - Team에서 속해있는 멤버들을 알고 싶으면 TEAM의 TEAM_ID(PK)와 Member의 TEAM_ID(FK)를 조인하면 됨
    - 사실 방향이란 개념 자체가 없음
- 객체는 참조를 통해 연관관계를 설정하기 때문에, Team에서 Member에서 서로에 대한 참조를 모두 가지고 있어야 함
- 엔티티
    - Member 엔티티는 단방향과 동일함
    - Team 엔티티에만 member 컬렉션을 추가
        ```java
        @Entity
        public class Team {
            ...
            @OneToMany(mappedBy = "team") 
            private List<Member> members = new ArrayList<Member>(); // 추가
        }
        ```
    - 반대 방향으로 객체 그래프 탐색 가능
      ```java
      Member findMember = em.find(Member.class, member.getId());
      List<Member> members = findMember.getTeam().getMembers(); // 역방향 조회
      ```

> 양방향으로 매핑하면 신경써야할 점이 많기 때문에 가급적이면 단방향이 좋음


<br/>

### ◻ 연관관계의 주인과 mappedBy

- `mappedBy`는 JPA에서 이해하기 가장 어려운 부분임
- **객체와 테이블간에 연관관계를 맺는 차이를 이해해야 함!**

<br/>

#### 객체와 테이블이 관계를 맺는 차이

- 객체의 양방향 관계
    - 객체 연관관계 = 2개
        - 회원 ➜ 팀 연관관계 1개 (단방향)
        - 팀 ➜ 회원 연관관계 1개 (단방향)
    - 객체의 양방향 관계는 사실 양방향 관계가 아닌 `서로 다른 단방향 관계 2개`임
        - 객체 세상에서는 양방향 연관관계를 맺으려면, 참조가 양쪽 객체에 있어야 함
- 테이블의 양방향 연관관계
    - 테이블 연관관계 = 1개
        - 회원 ↔ 팀 `연관관계 1개` (양방향, 사실 방향이 없는 것)
    - 테이블은 외래 키 하나도 두 테이블의 연관관계를 관리함
        - 외래키 값 하나로 테이블을 조인하면 양방향 연관관계를 가짐

<br/>

#### 둘 중 하나로 외래키를 관리해야 함

- 두 가지 참조 값이 존재
    - Member ➜ Team 참조값
    - Team ➜ Member 참조값
- 둘 중 어떤 것으로 테이블의 외래키에 매핑해야 하는지에 대한 고민
    - `Member` 객체의 team 값을 변경했을 때, 외래키 값이 업데이트 되어야 하는지
    - `Team` 객체의 members 값을 변경했을 때, 외래키 값이 업데이트 되어야 하는지
- DB 입장에서는 `TEAM_ID(FK)`만 업데이트되면 되기 때문에 둘 중 하나로 외래키를 관리해야 함!
    - 둘 중 어떤 것(Team이나 Member)으로 관리를 해야할 지 주인을 정해야 함(이것이 `연관관계의 주인`)


<br/>

#### 연관관계의 주인(Owner)

- 양방향 매핑 규칙
    - 객체의 두 관계 중, 하나를 연관관계의 주인으로 지정
    - 연관관계의 주인만이 외래키를 관리(등록, 수정)
    - 주인이 아닌 쪽은 읽기만 가능
    - 주인은 `mappedBy` 속성 사용 x
    - 주인이 아니면 `mappedBy` 속성으로 주인 지정
- 그래서 누구를 주인으로?
    - **외래 키가 있는 곳을 주인으로❗❗**
        - `ManyToOne`의 `Many` 쪽이 연관관계의 주인으로 하면 됨
        - 여기서는 Member.team이 연관관계의 주인

  <br/>
  <img src="https://github.com/user-attachments/assets/25136c80-713d-492c-9555-bee14af33f6a" width="400"/>
  <br/><br/>

- 외래키가 있는 쪽의 테이블과 대응되는 엔티티에 있는 참조를 연관관계의 주인으로 정하는 것이 헷갈리지 않음
    - 만약 외래키가 있는 곳이 아닌 Team.members를 연관관계의 주인으로 정할 경우
    - Team에 있는 members를 수정했는데 Update 쿼리는 다른 테이블(Member)에 날아감(헷갈림)
- 외래키가 존재하는 엔티티에서 관리를 나중에 문제가 발생하지 않음
    - DB 입장에서는 `ManyToOne(다대일)` 관계에서 외래키가 있는 쪽이 `Many(다)`고 외래키가 없는 쪽이 `One(일)`임
        - DB의 `Many`쪽이 무조건 연관관계의 주인이 됨
    - 객체에서는 `@ManyToOne`, `@XXXToOne` 쪽이 무조건 연관관계의 주인이 됨
    - 연관관계의 주인은 비즈니스적으로 큰 의미가 없음
- 외래키가 있는 쪽이 연관관계 주인이 되어야 설계가 깔끔해짐


<br/>

#### 양방향 매핑시 가장 많이 하는 실수

- 연관관계의 주인에 값을 입력하지 않음
  ```java
  Team team = new Team );
  team.setName("TeamA");
  em.persist(team);
    
  Member member = new Member();
  member.setName("member1");
    
  team.getMembers().add(member); //역방향(주인이 아닌 방향)만 연관관계 설정
    
  em.persist(member);
  ```
    - 연관관계의 주인만이 외래키 값을 등록, 수정할 수 있음
        - 연관관계의 주인이 아닌쪽은 조회만 가능함
    - JPA에서 update나 insert할 때는 `mappedBy`된 쪽은 보지 않음
        - `mappedBy`는 그냥 읽기 전용, 가짜 매핑이기 때문에 실제로 DB에 반영되지 않음
- 양방향 매핑시 연관관계의 주인에만 값을 입력(양쪽에 다 값을 입력하지 않는 경우)
  ```java
  Team team = new Team();
  team.setName("TeamA");
  em.persist(team);
    
  Member member = new Member();
  member.setName("member1");
  member.setTeam(team); //연관관계의 주인에 값 설정
  
  em.persist(member);
  ```
    - 연관관계의 주인에만 값을 설정해주면 영속성 컨텍스트에 있는 team 객체의 member 컬렉션은 여전히 비어있는 상태
        - 따라서 트랜잭션 안에서 영속성 컨텍스트에 flush하고 clear 되기 전에 해당 컬렉션을 조회하면 정상적인인 결과가 출력되지 않음
        - 물론, 커밋 시점에는 연관관계의 주인에 의해서 DB에 업데이터 쿼리가 날아감
    - 테스트 케이스 작성 시, JPA 없이도 동작하게 순수 자바 코드로 작성하기 때문에 `Team.getMembers()`를 하면 값이 없어 null로 나오는 문제가 발생


- 결론
    - 순수 객체 상태를 고려해서 항상 양쪽에 값을 설정하자
    - `연관관계 편의 메서드`를 생성하자
      ```java
      @Entity
      public class Member {
          @ManyToOne
          @JoinColumn(name = "TEAM_ID")
          private Team team;
      
          public Team getTeam() {
              return team;
          }
      
          public void changeTeam(Team team) {
              this.team = team;
              team.getMembers().add(this);
          }
      }
      ```
        - 진짜 주인에 값을 넣을 때, 자동으로 가짜 주인에도 넣을 수 있도록 편의 메소드를 활용
            - 연관관계 편의 메서드나 JPA 상태를 변경하는 건 setter를 활용하지 않음
        - 이는 단순하게 getter, setter 관례에 의한게 아니라 어떤 작업을 수행하는지 명확하게 알 수 있어 좋음
    - 양방향 매핑 시, 무한루프를 조심하자
        - Lombok의 toString()은 사용하지 말기
        - JSON 생성 라이브러리와 관련해서 컨트롤러에서 entity를 절대 반환하지 않기
            - entity를 반환할 경우 문제가 생김
                - 무한루프 발생
                - 나중에 entity를 변경하는 순간 API 스펙이 바뀌어 버림
            - DTO을 변환해서 반환하는 것이 좋음


<br/>

### ◻ 정리
- JPA 사용 시, 엔티티 설계할 때
    - 단방향 매핑으로 모두 끝내기
    - `일대다`일 때 `Many(다)`쪽에 연관관계 매핑을 전부 설정해주고 설계를 끝내기
    - 실제 애플리케이션을 개발하는 단계에서 양방향 매핑을 고려
        - 객체 입장에서 양방향 매핑이 크게 이득되는 것이 없음
- 양방향 매핑
    - 단방향 매핑만으로도 이미 연관관계 매핑은 완료
        - JPA 모델링할 때 단방향 매핑으로 처음 설계를 끝내야 함(양방향 매핑을 하면 안됨)
            - 실무에서는 사실 객체만으로 설계할 수 없고, 테이블 설계를 먼저 그리면서 객체 설계를 같이 들어가야 함
            - 그 시점에 테이블 관계에서 대략전인 FK가 나옴
            - 결국 `Many` 쪽에서 단방향 매핑(ManyToOne, OneToOne)을 다 걸어서 들어가야하기 때문에 이때 양방향 매핑을 하지 말자
    - 양방향 매핑은 반대 방향으로 조회(객체 그래프 탐색) 기능이 추가된 것 뿐임
    - JPQL에서 역방향으로 탐색할 일이 많음
    - 단방향 매핑을 잘하고 양방향은 필요할 때 추가해도 됨(`테이블에 영향을 주지 않음`)
        - **처음에 무조건 단방향 매핑으로 설계를 끝내고, 그리고 나서 역방향으로 조회 기능이 필요할 때 양방향을 사용하기**
- 연관관계의 주인을 정하는 기준
    - 비즈니스 로직을 기준으로 연관관계의 주인을 선택하면 안됨
    - 연관관계의 주인은 외래 키의 위치를 기준으로 정해야함


 <br/>


## 다양한 연관관계 매핑

### ◻ 연관관계 매핑 시 고려사항

- 다중성
    - 다대일: `@ManyToOne`
    - 일대다: `@OneToMany`
    - 일대일: `@OneToOne`
    - 다대다: `@ManyToMany` (실무에서 사용 x)
- 단방향, 양방향
    - 테이블
        - 외래키 하나로 양쪽 조인 가능
        - 사실 방향이라는 개념 존재 x
    - 객체
        - 참조용 필드가 있는 쪽으로만 참조 가능
        - 한쪽만 참조하면 단방향
        - 양쪽이 서로 참조하면 양방향(사실은 단방향이 2개)
- 연관관계의 주인
    - 테이블은 외래키 하나로 두 테이블이 연관관계를 맺음
    - 객체 양방향 관계는 참조가 2군데 존재. 둘 중 테이블의 외래키를 관리할 곳을 지정해야 함
    - 연관관계의 주인: 외래키를 관리하는 참조(등록, 수정)
    - 주인의 반대편: 외래키에 영향을 주지 않음, 단순 조회만 가능

<br/>

### ◻ 다대일


#### 다대일 단방향 [N:1]
- 가장 많이 사용하는 연관관계
- 다대일의 반대는 일대다 관계
- 관계형 DB에서는 설계 상, 항상 `다(N)`쪽에 외래키가 있음

  <br/>
  <img src="https://github.com/user-attachments/assets/25136c80-713d-492c-9555-bee14af33f6a" height="200" width="350"/>
  <br/><br/>

    - 회원과 팀은 다대일 연관관계
        - 회원은 `Member.team`으로 team 엔티티를 참조할 수 있지만, 반대로 팀에는 회원을 참조하는 필드가 없음
    - `@JoinColumn(name = "TEAM_ID")`
        - `Member.team` 필드를 `TEAM_ID` 외래키와 매핑하여 외래키를 관리함

<br/>

#### 다대일 양방향 [N:1, 1:N]
- 외래키가 있는 쪽이 연관관계의 주인
    - 일대다와 다대일 관계는 항상 `다(N)`에 외래키가 있음
- 양방향 연관관계는 항상 서로를 참조해야 함
    - 어느 한 쪽만 참조하면 양방향 연관관계가 성립하지 않음
    - 항상 서로 참조하게 하려면 연관관계 편의 메서드를 작성하는 것이 좋음
    - 연관관계 편의 메서드는 한 곳에 작성하거나 양쪽 모두 작성할 수 있는데, 양쪽에 다 작성하면 무한루프에 빠지기 때문에 주의해야 함

      <br/>
      <img src="https://github.com/user-attachments/assets/cf777ecf-0a04-444a-b254-6d8721821467" height="200" width="350"/>
      <br/><br/>

        - 다쪽인 MEMBER 테이블이 외래키를 가지고 있기 때문에, `Member.team`이 연관관계의 주인임
        - 실선이 연관관계의 주인(`Member.team`)이고, 점선(`Team.members`)는 연관관계의 주인이 아님

<br/>

### ◻ 일대다


#### 일대다 단방향 [1:N]
- 일대다(1:N)에서 `일(1)`이 연관관계의 주인
- 테이블 일대다 관계는 항상 `다(N)`쪽에 외래키가 있음
- 객체와 테이블의 차이 때문에 반대편 테이블의 외래키를 관리하는 특이한 구조(권장 x)
- `@JoinColumn`을 꼭 사용해야 함
    - 그렇지 않으면 조인 테이블 전략(중간에 테이블을 하나 추가하여 연관관계를 관리함)을 기본으로 사용해서 매핑함

  <br/>
  <img src="https://github.com/user-attachments/assets/60b030c2-a383-4b88-8b47-2a3b4d126cdb" height="200" width="350"/>
  <br/><br/>

    - Team은 Member를 알고 싶은데, Member 입장에서는 Team을 알고싶지 않은 경우
        - 객체 입장에서 이런 설계가 나올 확률이 높음
    - DB 설계 상, `일(1)`은 외래키가 들어갈 수 없고 무조건 `다(N)`쪽에 외래키가 들어가야 함
        - 하지만 `다(N)`쪽인 Member 엔티티에는 외래키를 매핑할 수 있는 참조 필드가 없음
        - 따라서 `Team.members`에서 회원 테이블의 `TEAM_ID(FK)` 외래키를 관리함
            - 보통 자신이 매핑한 테이블의 외래키를 관리하지만, 이 매핑은 반대쪽 테이블에 있는 외래키를 관리함
    - `Team.members`의 값을 바꿨을 때, Member 테이블을 있는 `TEAM_ID(FK)`를 업데이트해주어야 함
- 단점
    - 엔티티가 관리하는 외래키가 다른 테이블에 존재함
        - 성능 문제도 있지만 관리가 부담스러움
    - 다른 테이블에 외래키가 있으면, 연관관계 관리를 위해 추가로 UPDATE SQL을 실행해야 함
        - 만약 본인 테이블에 외래키가 있으면, 엔티티의 저장과 연관관계 처리를 INSERT SQL 한 번으로 처리 가능

> 일대다 단방향 매핑보다는 다대일 양방향 매핑을 사용하자!  
> 다대일 양방향 매핑은 관리해야 하는 외래키가 본인 테이블에 존재하기 때문에 일대다 단방향 매핑 같은 문제가 발생하지 않음

<br/>


#### 일대다 양방향 [1:N, N:1]

- 해당 매핑은 공식적으로 존재하지 않음
    - 대신 다대일 양방향 매핑을 사용해야 함
    - 일대다 양방향과 다대일 양방향은 같은 말이지만, 왼쪽을 연관관계의 주인으로 가정해서 분류함
    - 양방향 매핑에서 `@OneToMany`는 연관관계의 주인이 될 수 없음
        - 관계형 DB의 특성상 일대다와 다대일 관계에서 항상 `다(N)`쪽에 외래키가 존재함
    - 일대다 양방향 매핑이 완전히 불가능한 것은 아님
- 읽기 전용 필드를 사용해서 양뱡향처럼 사용하는 방법
    - `@JoinColumn(insertable=false, updatable=false)`
      ```java
      @Entity
      public class Member {
          @Id
          @GeneratedValue
          @Column(name = "MEMBER_ID")
          private Long id;
          
          @ManyToOne
          @JoinColumn(name = "TEAM_ID", insertable = false, updatable = false) // 읽기 전용이 됨
          private Team team;
      }
      ```
    - 일대다 단방향 매핑 반대편에 같은 외래키를 사용하는 다대일 단방향 매핑을 `읽기 전용`으로 하나 추가함
    - 둘 다 같은 키를 관리하므로 문제가 발생할 수 있기 때문에 다대일 쪽은 `insetable`과 `updatable` 설정으로 읽기만 가능하게 함
- 결론은 다대일 양방향을 사용하자
    - 해당 방법은 일대다 양방향 매핑이라기 보다 일대다 단방향 매핑 반대편에 다대일 단방향 매핑을 읽기 전용으로 추가해서 양방향처럼 보이도록 하는 방법
    - 따라서 일대다 단방향 매핑이 가지는 단점을 그대로 가지게 됨

      <br/>
      <img src="https://github.com/user-attachments/assets/ef8eae23-4eb2-4aa4-99ce-6bc0eae0ecc3" height="200" width="400"/>
      <br/>

    
<br/>


### ◻ 일대일


#### 일대인 관계 [1:1]
- 일대일 관계는 그 반대도 일대일
- 주 테이블과 대상 테이블 중에 누가 외래키를 가질 지 선택해야 함
    - `주 테이블에 외래키`
        - 주 객체가 대상 객체의 참조를 가지는 것처럼 주 테이블에 외래키를 두고 대상 테이블을 참고함
        - 객체지향 개발자가 선호하며 JPA 매핑에 편리함
        - 장점: 주 테이블이 외래키를 가지고 있기 때문에, 주 테이블만 조회해도 대상 테이블에 데이터가 있는지 확인 가능
        - 단점: 값이 없으면 외래키에 null 값을 허용함
    - `대상 테이블에 외래키`
        - 대상 테이블에 외래키가 존재함
        - 전통적인 데이터베이스 개발자가 선호함
        - 장점: 주 테이블과 대상 테이블을 `일대일`에서 `일대다`로 관계를 변경할 때 테이블 구조를 그대로 유지할 수 있음
        - 단점: 무조건 양방향으로 만들어야 하며, 프록시 기능의 한계로 지연로딩으로 설정해도 항상 즉시 로딩됨
- 외래 키에 데이터베이스 유니크(UNI) 제약조건 추가

<br/>

#### 일대일: 주 테이블에 외래키
- 단방향
    - 다대일(`@ManyToOne`) 단방향 매핑과 유사

  <br/>
  <img src="https://github.com/user-attachments/assets/8f138e96-d3d4-48be-ae72-df6cf9a3e4c4" height="200" width="380"/>
  <br/><br/>

    - `Member`를 주테이블이라고 생각(둘 중 어디에 넣어도 됨)
    - DBA 입장에서와 개발자 입장에서의 딜레마 발생
        - DBA 입장
            - 나중에 하나의 `Member`가 여러 개의 `Locker`를 가질 수 있다고 할 때, `Locker`에 두는게 여러 개를 insert할 수 있어 편리함
            - 만약 `Member`에 외래키가 있으면 코드나 기능을 많이 변경해야 함
        - 개발자 입장
            - `Member`에 `Locker`가 있는 것이 성능도 그렇고 여러가지로 유리함
            - `Member`는 거의 필수적으로 조회되는데 조인없이 DB 쿼리 한 방으로 `Member`를 가져오면 `Locker` 데이터가 있는지 없는지 판단하기 쉬움

- 양방향
    - 다대일 양방향 매핑처럼 외래키가 있는 곳이 연관관계의 주인
    - 반대편읜 `mappedBy` 적용

  <br/>
  <img src="https://github.com/user-attachments/assets/b575330e-6c2f-4c10-92cb-00b093f77510" height="200" width="380"/>
  <br/><br/>

    - 반대편 `Locker`에 `Member` 필드를 추가함
    - `MEMBER` 테이블이 외래키를 가지고 있으므로, `Member.locker`가 연관관계의 주인
    - 반대 매핑인 `Locker.member`는 mappedBy를 적용

<br/>

#### 일대일: 대상 테이블에 외래키

- 단방향
    - 단방향 관계는 JPA 지원 x
    - 양방향 관계는 지원

  <br/>
  <img src="https://github.com/user-attachments/assets/ddf38a66-fdb0-420f-a9b7-f7615f04ab86" height="200" width="380"/>
  <br/><br/>

    - `Member` 엔티티에 있는 `locker` 참조로 반대쪽 테이블(`LOCKER` 테이블)에 있는 외래키(`MEMBER_ID`)를 관리할 수 없음
    - 단방향 관계는 JPA에서 지원하지 않고, 방법도 없음
        - 단방향 관계를 `Locker`에서 `Member` 방향으로 수정하거나, 양방향 관계로 만들고 `Locker`를 연관관계의 주인으로 설정해야 함
- 양방향
    - 사실 `일대일 주 테이블에 외래키 양방향`과 매핑 방법이 같음
    - 일대일 관계는 내 엔티티에 있는 외래키를 직접 관리해야 함

  <br/>
  <img src="https://github.com/user-attachments/assets/4daef52e-919e-48c9-a168-e61b3e2a8b8c" height="200" width="380"/>
  <br/><br/>

    - 대상 엔티티의 참조를 외래키와 매핑하면 됨
        - `Locker.member`를 연관관계의 주인으로 잡아서 매핑

<br/>

> 대상 테이블에 외래키가 있는 경우, 프록시 기능의 한계로 지연로딩으로 설정해도 항상 즉시로딩 됨
- JPA 입장에서 프록시 객체를 만들기 위해서는 해당 엔티티의 참조에 매핑되는 값이 테이블에 있는지 없는지 알아야 함
    - 지연로딩 시, 참조에 값이 있으면 프록시로 초기화하고 값이 없으면 null을 넣어주기 때문
- 주 테이블에 외래키가 있는 경우
    - 주 엔티티의 참조 변수에 매핑되는 값이 있는지 확인하기 위해 주 테이블만 검색하면 됨
    - 다른 대상 테이블을 검색할 필요가 없어서 지연로딩 가능
- 대상 테이블에 외래키가 있는 경우
    - 주 테이블을 가져올 때, 해당 참조에 매핑되는 값이 있는지 확인하기 위해서 대상 테이블까지 검색해야 함
        - 주 테이블에 외래키 값이 없고, 외래키 값이 대상 테이블에 있기 때문에 매핑 되는 값을 확인하기 위해 대상 테이블을 검색해야 함
    - 결국 대상 테이블을 검색해야하기 때문에 지연로딩을 해도 즉시로딩이 되어버림(지연로딩으로 세팅하는 것이 의미가 없음)


<br/>


### ◻ 다대다

#### 다대다 관계 [N:M]
- 실무에서 쓰지 않는 것을 권장함
- 관계형 데이터베이스는 정규화된 테이블 2개로 다대다 관계를 표현할 수 없음
    - 연결 테이블을 추가해서 일대다, 다대일 관계로 풀어내야 함

      <br/>
      <img src="https://github.com/user-attachments/assets/3c68db91-c87f-450e-a559-910e7992ca36" height="180" width="420"/>
      <br/><br/>
    
- 객체는 컬렉션을 사용해서 객체 2개로 다대다 관계가 가능함
- `@ManyToMany` 사용하고,`@JoinTable`로 연결 테이블 지정해주어야 함
- 다대다 매핑은 단방향과 양방향 모두 가능

<br/>

#### 다대다 매핑의 한계

- 편리해보이지만 실무에서 사용 x
- 연결 테이블이 단순 연결만 하고 끝나지 않음
    - 주문시간, 수량 같은 데이터가 들어올 수 있음
- 중간 테이블이 숨겨져있기 때문에, 쿼리도 예상하지 못하게 날아감
- 한계 극복
    - 연결 테이블용 엔티티 추가(연결 테이블을 엔티티로 승격)
    - `@ManyToMany` ➡ `@OneToMany`, `@ManyToOne`

      <br/>
      <img src="https://github.com/user-attachments/assets/a18053ab-9902-4e2a-81fa-673e1ac347d5" height="200" width="450"/>
      <br/><br/>

- 연결 테이블에서 PK
    - `MEMBER_ID`와 `PRODUCT_ID`를 복합키로 PK를 잡을 수 있음
        - JPA에서 복합키를 사용하면 Composite ID를 별도로 만들어주어야 함
    - 하지만 별도의 PK를 별도로 생성하는 것을 추천함
        - 객체 입장에서 비식별 관계를 사용하는 것이 단순하고 편리하게 ORM 매핑을 할 수 있음
- 테이블간의 관계 설정
    - 식별 관계: 받아온 식별자는 `기본키 + 외래키`로 사용함
    - 비식별 관계: 받아온 식별자는 `외래키`로만 사용하고, `새로운 식별자`를 추가함

<br>
<br>


### Ref

- [자바 ORM 표준 JPA 프로그래밍 - 기본편](https://www.inflearn.com/course/ORM-JPA-Basic)


