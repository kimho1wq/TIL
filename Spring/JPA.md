# JPA(Java Persistence API)

### JPA 란
- 자바 어플리케이션에서 관계형 데이터베이스를 사용하는 방식을 정의한 인터페이스이다
- JPA는 자바 진영에서 ORM 기술 표준으로 사용되며 구현된 클래스와 매핑을 해주기 위해 사용되는 프레임워크이다
- JPA 표준 명세를 구현한 구현체로 Hibernate 등이 있다
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/91a8b578-1786-447d-a087-22905260c6e9)
- ORM(Object-Relational Mapping)
  - OOP(Object Oriented Programming)에서 쓰이는 객체라는 개념을 구현한 클래스와 RDB(Relational DataBase)에서 쓰이는 데이터인 테이블 자동으로 매핑(연결)하는 것을 의미한다
  - SQL문이 아닌 Method를 통해 DB를 조작할 수 있어, 개발자는 객체 모델을 이용하여 비즈니스 로직을 구성하는데만 집중할 수 있음
  - 내부적으로는 쿼리를 생성하여 DB를 조작하기 때문에 DBMS(DataBase Management System) 변경에 따른 종속성(Query 변경 의존도)이 낮아짐
  - Query와 같이 필요한 선언문, 할당 등의 부수적인 코드가 줄어들고 객체지향적인 코드 작성이 가능하다
  - 다만, 복잡하고 무거운 Query는 속도를 위해 별도의 튜닝이 필요하기 때문에 결국 SQL문을 써야할 수도 있고, 학습 비용이 비싸다


### JPA와 DBTable 관계
- 상속 관계
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/ae5d5d6f-d094-40c5-ae10-df299a241e85)
  - 위의 구조에서 Album 클래스를 저장
    - ```
      // Album 객체저장
      jpa.persist(album);
      ```
  - 그러면 JPA는 위의 코드를 아래의 쿼리로 변환해서 실행한다
    - ```
      INSERT INTO ITEM (ID, NAME, PRICE) .....
      INSERT INTO ALBUM (ARTIST) .....
      ```
  - 위처럼 저장한 후 조회할 때도 두 테이블을 엮어서 가져온다
    - ```
      // JAVA 코드
      String albumId = "id100";
      Album album = jpa.find(Album.class, albumId);
      
      // 변환된 쿼리
      SELECT I.*, A.*
        FROM ITEM I
        JOIN ALBUM A ON I.ITEM_ID = A.ITEM_ID
      ```
- 연관 관계
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/13ab28f8-033f-4697-b72c-aaf4c373e600)
  - 다른 객체를 참조하고 있는 객체의 저장
    - ```
      // Member 객체저장
      Member member = new Member(
          id="001",
          userName="dbjh",
          team=new Team(name="dev_team");
      )
      jpa.persis(member);
      ```
  - 그러면 JPA는 위의 코드를 아래의 쿼리로 변환해서 실행한다
    - ```
      INSERT INTO MEMBER (ID, TEAM_ID, USERNAME) ....
      INSERT INTO TEAM (ID, NAME) ....
      ```
  - 저장 후 Member 객체를 조회하면, Team 객체 정보도 가져와서 필드에 주입해 준다
    - ```
      // JAVA 코드
      Member member = jpa.find(Member.class, memberId);
      Team team = member.getTeam();

      // 변환된 쿼리
      SELECT M.*, T.*
        FROM MEMBER M
        JOIN TEAM T ON M.TEAM_ID = T.TEAM_ID 
      ```

### Spring Data JPA
- 스프링에서는 JPA를 이용하는 spring-data-jpa 프레임워크를 주로 사용한다
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/a6c514b8-c6e1-40c6-9c11-bcf3d8b04779)
  - ```implementation('org.springframework.boot:spring-boot-starter-data-jpa')```
  - ```
    import org.springframework.data.jpa.repository.JpaRepository;
    public interface DataJpaRepository extends JpaRepository<Type, Id> {
        //  Type: 사용할 Repository의 기준이 되는 Entity의 이름
        //  Id: 기입된 Entity의 PK 자료형
        ...
    }
    ```
  - ```
    public interface PostsRepository extends JpaRepository<Posts, Long> {
        
        Optional<Posts> findByEmail(String email);
    
        // 쿼리를 직접 맵핑할 수도 있다
        @Query("SELECT p FROM Posts p ORDER BY p.id DESC")
        List<Posts> findAllDesc();
    }
    ```






















