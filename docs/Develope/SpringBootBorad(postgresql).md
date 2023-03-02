---
layout: default
title: Spring Boot 게시판 서비스 만들기 (PostgreSql)   
nav_order: 4
parent: 개발
date: 2022-10-31 12:00:00
last_modified_date: 2022-10-31 12:00:00
---

# Spring Boot 게시판 만들기   
    심플한 게시판을 만들어보자.   
    서버 API는 Restful 하게 설계를 하고 화면은 spa 로 개발해보기로 한다.    
    저장소는 postgresql 을 사용하기로 한다.   
    spring webmvc를 사용하고 jpa를 사용해보자

1. PostgreSql 실행 (docker)
    > 개발을 위한 DB 실행으로 운영에서는 운영을 위한 구조가 필요  

    ```
    sudo docker run --name hitecboarddb -p 5432:5432 -e POSTGRES_USER=hitec -e POSTGRES_PASSWORD=1234 -e POSTGRES_DB=hitecboard -d postgres
    ```
    Docker로 PostgreSql DB를 실행시킨다. 

    > POSTGRES_PASSWORD - 데이터베이스 비밀번호   
    > POSTGRES_USER - 데이터베이스 사용자   
    > POSTGRES_DB - 데이터베이스 스키마   

2. 프로젝트 생성

    프로젝트 설정을 하고
    spring web, spring data jpa, postgresql driver를 디펜던시로 설정한다. 

    ![board project init](../image/Dev/board1.png)   

    Generate 버튼을 클릭해서 초기 설정 프로젝트를 다운로드 한다.    

    다운로드 받은 파일을 적당한 위치에 압축을 풀고 VSCode로 연다.   

3. Spring 설정    
    application.properties 파일을 application.yml 파일로 변경   

    ```yaml
    spring:
        datasource:
            url: jdbc:postgresql://192.168.56.1:5432/hitecboard
            username: hitec
            password: 1234
            driver-class-name: org.postgresql.Driver
        jpa:
            show-sql: true
            hibernate:
            ddl-auto: create
            database-platform: org.hibernate.dialect.PostgreSQLDialect
    logging.level:
        org.hibernate.SQL: debug
    ``` 

4. Modeling   
    model 이라는 이름으로 디렉토리를 생성한다.   
    DB 의 테이블과 매칭이 되는 java 클래스 파일을 생성한다. 

    예제에서는 작성한 글을 저장하는 게시판 테이블을 우선 만들어보자

    ```java
    //Board.java
    @Entity
    @Table(name = "board")
    @Getter
    @Setter
    @AllArgsConstructor
    @NoArgsConstructor
    public class Board {

        @Id
        @GeneratedValue(strategy = GenerationType.AUTO)
        private Long id;

        @Column(name = "title")
        private String title;

        @Column(name = "content")
        private String content;

        @OneToOne
        BoardUser boardUser;
    }
    ```

    > BoardUser 는 게시글을 사용하는 사용자로 keycloak 연동 때 사용   

5. Repository   
    repository 라는 이름으로 디렉토리를 생성한다.   
    DB 와 연결해서 모델에 정의된 형태로 CRUD 를 실행한다.    

    ```java
    //BoardRepository.java
    @Repository
    public interface BoardRepository extends JpaRepository<Board, Long>{
    }
    ```

6. Service   
    service 라는 이름으로 디렉토리를 생성한다.    
    Repository 를 통해 CRUD를 수행한다. 

    ```java
    //BoardService.java
    @Service
    public class BoardService {
    
        BoardRepository boardRepository;

        @Autowired
        public BoardService(BoardRepository boardRepository){
            this.boardRepository = boardRepository;
        }

        public List<Board> boardList(){
            return boardRepository.findAll();
        }

        public Board getBoard(Long id){
            return boardRepository.findById(id).orElseThrow();
        }

        public Board setBoard(Board board){
            return boardRepository.save(board);
        }

        public void removeBoard(Long id){
            boardRepository.findById(id).ifPresent(board -> boardRepository.delete(board));
        }

    }
    ```

7.  Controller   
    web 이라는 이름으로 디렉토리를 생성한다.    
    web을 통해 api를 요청을 처리한다. 
    ```java
    //BoardController.java
    @CrossOrigin(origins = {"http://localhost:3000"}, maxAge=3600, methods = {RequestMethod.GET, RequestMethod.POST, RequestMethod.HEAD, RequestMethod.OPTIONS, RequestMethod.PUT} )
    @RestController
    public class BoardController {

        BoardService boardService;
        BoardUserService boardUserService;

        @Autowired
        public BoardController(BoardService boardService, BoardUserService boardUserService){
            this.boardService = boardService;
            this.boardUserService = boardUserService;
        }

        @GetMapping("/boards")
        public List<BoardVo> getBoardList(){
            List<Board> boardList = boardService.boardList();

            List<BoardVo> rtnList = new ArrayList<>();
            for(Board board : boardList){
                rtnList.add(new BoardVo(board.getId(), board.getTitle(), board.getContent(), board.getBoardUser().getUsername()));
            }

            return rtnList;
        }

        @GetMapping("/board/{boardId}")
        public BoardVo getBoard(@PathVariable Long boardId){
            Board board = boardService.getBoard(boardId);
            return new BoardVo(board.getId(), board.getTitle(), board.getContent(), board.getBoardUser().getUsername());
        }

        @PostMapping("/board")
        @Transactional
        public BoardVo setBoard(@RequestBody BoardVo boardVo){
            BoardUser boardUser = boardUserService.setUser(new BoardUser(null, boardVo.getUserName()));
            Board board = boardService.setBoard(new Board(null, boardVo.getTitle(), boardVo.getContent(), boardUser));
            return new BoardVo(board.getId(), board.getTitle(), board.getContent(), board.getBoardUser().getUsername());
        }

        @PutMapping("/board")
        public BoardVo modifyBoard(@RequestBody BoardVo boardVo){
            Board board = boardService.setBoard(new Board(boardVo.getId(), boardVo.getTitle(), boardVo.getContent(), boardUserService.getUserByName(boardVo.getUserName())));
            return new BoardVo(board.getId(), board.getTitle(), board.getContent(), board.getBoardUser().getUsername());
        }
        
    }
    ```   

    > 리스트 조회, 한 건 조회, 생성, 수정 에 대한 함수 작성   
    > 외부와의 통신을 위해서 Model 을 직접 사용하지 않고 Vo를 생성하였음 --> 직접 쓰는 것과 장단점이 있음, 상황에 따라 적절한 걸 써야....
    > react 에서 개발 할 때 localhost:3000 으로 띄워서 테스트를 진행하기 때문에 CrossOrigin 을 설정    
       

   

# 빌드 및 배포   
1. docker 이미지로 만들기   
    board java application도 Docker로 만들어서 배포한다.    
    1. Dockerfile 생성   
        Dockerfile 을 만든다. base 이미지는 jre 서버로 하고 빌드 된 바이너리를 복사한 다음 Docker 컨테이너가 시작 할 때 수행 될 명령어를 입력한다.    

        ```Dockerfile
        FROM openjdk:11.0.16-jre

        ARG APP_FILE

        RUN echo ${APP_FILE}
        RUN mkdir /logs
        RUN chown 1000:1000 /logs
        RUN mkdir /app
        RUN chown 1000:1000 /app

        USER 1000:1000
        WORKDIR /app
        COPY  --chown=1000:1000 ${APP_FILE} /app/app.jar

        EXPOSE 8899
        ENTRYPOINT ["java", "-jar", "/app/app.jar"]
        ```
    2. 이미지 만들기   
        ```sh
        docker build --build-arg APP_FILE=target/board*.jar -t blogboard:0.0.1 .
        ```

        > build-arg 는 Dockerfile에 설정된 APP_FILE 이라는 변수에 값을 세팅하는 옵션   
        > t 옵션은 이미지의 이름과 버전정보를 세팅   

2. Jenkins를 통한 배포   
    Jenkins 를 통해 빌드하고 배포한다.    
    Jenkins 파일을 생성 하고 Jenkins에서 Job을 생성한다.    

    ```groovy
    pipeline {
        agent any
        stages {
            stage('BUILD') {
                steps {
                    sh 'mvn package -DskipTests'
                }
            }
            stage('STOP App') {
                steps {
                    script {
                        try {
                            sh 'docker stop blogboard'
                            sh 'docker rm blogboard' 
                        } catch (err) {
                            echo err.getMessage()
                            echo 'Stop App Failed'
                        }
                    }
                }
            }
            stage('Dockernizer') {
                steps {
                    sh 'docker build --build-arg APP_FILE=target/board*.jar -t blogboard:0.0.1 .'
                }
            }
            stage('Deployment') {
                steps {
                    sh 'docker run -d --name blogboard blogboard:0.0.1'
                }
            }
        }
    }
    ```

    > mvn을 통해 빌드를 수행 -> 동작중인 docker container를 멈추고 삭제 -> 빌드한 바이너리를 다커 이미지로 빌드 -> 새로운 다커 이미지로 컨테이너 실행