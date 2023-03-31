---
layout: default
title: JPA 세팅
nav_order: 4
parent: Spring Boot Setting
date: 2023-03-29 12:00:00
last_modified_date: 2023-03-29 12:00:00
---


# JPA 세팅 #   
Spring 에서 RDB 에 접근해서 데이터를 저장하고 조회하는 방법으로는   
Mybatis 와 JPA 방법이 많이 쓰이고있다.    
Mybatis는 SQL을 직접 작성하고 결과를 객체와 매핑하는 방식 이며   
JPA 는 java 객체가 바로 DB의 테이블이 되어 매핑되는 방식이다.    

DB가 복잡하고 쿼리도 복잡하게 짜여져야하는 경우 Mybatis 가 어울리고    
DB 스키마가 단순하거나 잘 짜여져 있는 경우 JPA 가 어울리는 것 같다.   

DB가 발전하면서 서비스의 로직까지 DB로 내려가는 경우가 많다.    
그럼으로 인해 DB의 자원 할당이 점점 커져가고 있으며 무거워 지고 있다.    
그에 따라 로직부분을 개발쪽으로 올려야한다고 하는 얘기들을 종종 듣는다.    
MSA가 보편화되면서 DB에서 로직을 처리할 수 없는 상황이 점점 많아지는 것 같다.    


## 메이븐 설정 ##  

pom.xml 파일에 dependency 추가
```xml
<!-- using jpa -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<!-- postgresql driver -->
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
    <scope>runtime</scope>
</dependency>
```
> Spring에서 JPA를 사용하기 위해 spring-boot-starter-data-jpa를 추가한다.    
> postgresql driver를 추가하였다. 샘플에서는 postgresql db를 사용할 예정이다.   
> DB 의 driver는 사용하는 DB 에 맞게 설정하면 된다.    

## Sample Code ##
DB 의 테이블에 매핑되는 Board 이름의 Entity를 만든다.    
```java
@Entity(name="board")
@Getter
@Setter
public class Board {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "title")
    private String title;

    @Column(name = "content")
    private String content;

    @Column(name = "username")
    private String userName;
}
```
> @Entity 어노테이션을 통해 entity 로 정의한다.    
> @Id 는 테이블의 키를 설정하며   
> @GeneratedValue 는 ID 의 순번을 정하는 방법을 정의한다.    
> @Column 은 테이블의 컬럼에 대한 다양한 정의를 할 수 있다.    

DB 에 접근하여 데이터를 저장 및 조회 하는 Repository를 만든다. 
```java
@Repository
public interface BoardRepository extends JpaRepository<Board, Long>{
    @Query(value="select * from board", nativeQuery=true)
    List<Board> selectAll();
}
```
> @Repository 어노테이션을 통해 Repository로 설정한다.    
> 인터페이스이며 기본적인 find, findAll, save 등의 함수는 자동으로 생성된다.    
> @Query 어노테이션을 사용하여 JPQL로 쿼리를 작성할 수 있다.    
> @Query 의 인자 nativeQuery=true를 통해 쿼리를 직접 만들어서 매핑할 수도 있다.    

Controller 에서 직접적으로 호출하는 Service 를 만든다.    
Service 는 Repository를 통해 데이터 저장 조회 등과 함께 기본적인 로직을 수행한다.    

```java
@Service
public class BoardService {
    BoardRepository boardRepository;

    @Autowired
    public BoardService(BoardRepository boardRepository){
        this.boardRepository = boardRepository;
    }

    public List<Board> getBoardList(){
        return boardRepository.findAll();
    }

    public Board getBoard(Long id){
        Optional<Board> boardOpt = boardRepository.findById(id);
        return boardOpt.orElse(new Board());
    }

    public Board saveBoard(Board board){
        return boardRepository.save(board);
    }
    
}
```
> @Service를 통해 서비스로 정의한다.    
> @Autowired를 통해 boardRepository를 이입하여 사용한다.    
> boardRespository 에 board 정보를 입력 조회 등을 수행한다.    

## Spring 설정 ##
application.yml 파일에 다음과 같이 설정한다.    
```yaml
spring:
  datasource:
    url: jdbc:postgresql://<ip>:5432/<dbname>
    username: <username>
    password: <password>
    driver-class-name: org.postgresql.Driver
  jpa:
    show-sql: true
    hibernate:
      ddl-auto: update
    database-platform: org.hibernate.dialect.PostgreSQLDialect
```
> database-platform 항목은 버전에 따라 필요없을 수도 있다.    
> 기본적인 설정이며 필요에따라 다양한 설정이 있다.    

## spring JPA ##
자세한 사항은 다음 참조 
[Spring Data JPA](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#project "Spring Data JPA")    

entity 와의 관계는 @OneToOne, @OneToMany, @ManyToOne, @ManyToMany 로 나타내며, 다양한 상세 설정들이 있다.    
JPA 만으로 두꺼운 책이 있을 정도로 많은 내용이 있다.    

## RUN PostgresqlDB ##
개발 할 때 필요한 Database를 docker container로 실행한다. 

```sh
sudo docker run --name db -p 5432:5432 -e POSTGRES_USER=hitec -e POSTGRES_PASSWORD=1234 -e POSTGRES_DB=boarddb -d postgres
```
Docker로 PostgreSql DB를 실행시킨다. 

> POSTGRES_PASSWORD - 데이터베이스 비밀번호   
> POSTGRES_USER - 데이터베이스 사용자   
> POSTGRES_DB - 데이터베이스 스키마   