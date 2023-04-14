---
layout: default
title: DataJapTest with TestContainer
nav_order: 8
parent: Spring Boot Setting
date: 2023-03-29 12:00:00
last_modified_date: 2023-03-29 12:00:00
---

# DataJpaTest 세팅 #   
spring boot 에서 Repository의 기능을 테스트 할 수 있다.     
Repository 관련 빈만 등록하여 테스트를 진행한다.    
DB에 데이터가 잘 저장이 되는지 조회는 잘 되는지 등을 확인 할 수 있다.    

## TestContainer ##
실제 DB 와의 연결을 통해 DB에 값을 넣고 조회하고 등의 테스트를 수행하게 된다.    
Test DB에 붙어서 테스트를 진행하게되면 개발 중간의 데이터에 대해 오동작 할 수 있다.    
commit을 하지 않고 테스트 종료후 롤백을 하기도 하지만 확실한 테스트가 되지 않는 것 같다.    

테스트 할 때만 DB를 띄웠다가 테스트가 끝나면 DB를 없애면 테스트가 깔끔할 것 같다.    
그래서 TestContainer 에서 테스트 할 동안 관련 DB를 컨테이너로 띄웠다가 종료하면 삭제해준다.    

서버에 Docker 가 기본으로 설치가 되어 있어야한다.    

## 메이븐 설정 ##

유닛 테스트를 위해 의존성을 추가한다.    
```xml
<!-- spring boot test (exclude junit4)-->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-test</artifactId>
  <scope>test</scope>
  <exclusions>
    <exclusion>
      <groupId>org.junit.vintage</groupId>
      <artifactId>junit-vintage-engine</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```
> spring boot starter test를 의존성에 추가한다.    
> junit 5를 사용할 예정이며 junit 4를 제외하기위해 junit-vintage-engine을 제외한다.     

testcontainers를 위해 의존성을 추가한다.    
```xml
<!-- using container for unit test-->
<dependency>
  <groupId>org.testcontainers</groupId>
  <artifactId>junit-jupiter</artifactId>
  <scope>test</scope>
</dependency>

<!-- postgresql container for test -->
<dependency>
  <groupId>org.testcontainers</groupId>
  <artifactId>postgresql</artifactId>
  <scope>test</scope>
</dependency>

<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.testcontainers</groupId>
      <artifactId>testcontainers-bom</artifactId>
      <version>1.17.6</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```
> testcontainers 에서 postgresql의 의존성을 주입했다.    
> 사용하는 data store 에 맞는 의존성을 주입하면 된다.    


## Testcontainers 설정 ##
src 의 main 이 아닌 test 디렉토리의 resources 디렉토리 하위에 application.yml 파일을 생성한다.    
```yaml
spring:
  datasource:
    driver-class-name: org.testcontainers.jdbc.ContainerDatabaseDriver
    url: jdbc:tc:postgresql:///test-database
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
    database: postgresql
```
> driver-class-name 과 url 을 샘플과 같이 작성하여 컨테이너로 생성되는 DB에 연결되도록 한다.    

## Sample Code ##
```java
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
@Rollback(false)
@Testcontainers
public class BoardRepositoryTest {

    @Autowired
    BoardRepository boardRepository;

    @Test
    @DisplayName("Board 생성 테스트")
    void insertBoard(){
        Board board = new Board();
        board.setTitle("hi");
        board.setContent("It's first content");
        board.setUserName("Hitec");
        
        Board savedBoard = boardRepository.save(board);
        Board queryBoard = boardRepository.findById(savedBoard.getId()).orElseThrow();

        Assert.assertEquals(queryBoard.getTitle(), board.getTitle());
    }

}
```

> @DataJpaTest 로 repository 테스트 파일임을 설정한다.     
> AutoConfigureTestDatabase.Replace.NONE 으로 설정함으로써 내부적으로 embeded db로 테스트 하지 않고 실제 DB로 테스트 하도록 설정한다.    
> 새로 생성된 컨테이너에서의 테스트임으로 롤백을 할 필요는 없음으로 Rollback 은 false로 설정한다.   

```sh
mvn test
```
mvn package 를 통해 컴파일, 테스트, 패키징까지 할 수 있다    
mvn test를 통해 테스트만 수행할 수 있다.   
테스트 수행 중 새로 container가 뜨고 테스트가 진행되는 것을 확인할 수 있다.    

* 개발환경이나 테스트 환경이 컨테이너이거나 다른 환경에서 테스트 컨테이너 DB가 실행되게 될 때 컨테이너와 연결이 잘 안될 경우가 있다.    
* 이런경우 환경변수 TESTCONTAINERS_HOST_OVERRIDE 값에 docker 의 IP를 설정하면 연결이 된다.    
```
export TESTCONTAINERS_HOST_OVERRIDE=<host IP>
```
## TestContainers ##
자세한 사항은 다음 참조 
[TestContainers](https://www.testcontainers.org/ "TestContainers")    