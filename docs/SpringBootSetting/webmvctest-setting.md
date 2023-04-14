---
layout: default
title: WebMvcTest
nav_order: 9
parent: Spring Boot Setting
date: 2023-03-29 12:00:00
last_modified_date: 2023-03-29 12:00:00
---

# WebMvcTest 세팅 #   
spring 에서 Controller 에 설정된 웹을 테스트한다.    
BDD(Behaviour-Driven Development)를 기준으로 테스트코드를 작성한다.       
Given, When, Then (주어진 상태에서 어떤 변화를 가할때 어떤 결과가 나온다.)   

## WebMvcTest ##   
MockMVC와 MockBean을 사용한다.   
MockMVC는 실제 서버를 기동하지 않고 Controller 에 요청, 전송, 응답기능을 하기 위해 만들어진 테스트 클래스이다.      

WebMvcTest 에서는 웹 관련 빈들만 등록하여 테스트하게된다. 
이런 경우 로직이 들어있는 service, db 수행하는 repository 등은 로딩되지 않는다.    
이 때 controller 에서 service를 사용하게 될 경우 빈이 등록되지 않아 테스트를 할 수 없게 된다.    
이런경우 MockBean 을 사용하여 빈으로 등록하고 특정 메소드에 대한 리턴 값을 미리 정의 할 수 있다. 


## 메이븐 설정 ##

spring-boot-starter-test 에 모두 포함된다.        
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

## Sample Code ##
```java

@WebMvcTest
public class BoardControllerTest {

    ObjectMapper objectMapper = new ObjectMapper();
    
    @Autowired
    MockMvc mockMvc;

    @MockBean
    private BoardService boardService;

    @Test
    @DisplayName("게시글 1건 조회")
    void testGetBoard() throws Exception{
        Board board = new Board();
        board.setId(1L);
        board.setTitle("It's title");
        board.setContent("It's content");

        given(boardService.getBoard(1L)).willReturn(board);

        mockMvc.perform(get("/board/{boardId}", 1))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.id").value(1L));

    }

    @Test
    @DisplayName("게시글 작성")
    void testSaveBoard() throws Exception{
  
        Board board = new Board();
        board.setTitle("It's title");
        board.setContent("It's content");

        given(boardService.saveBoard(any(Board.class))).will( (InvocationOnMock invocation) -> {
            Board board1 = (Board)invocation.getArgument(0);
            board1.setId(2L);
            return board1;
        });

        mockMvc.perform(post("/board")
            .content(objectMapper.writeValueAsString(new BoardDTO(0, "It's title", "It's Content", "hhh")))
            .contentType("application/json")
            )
            .andExpect(status().isCreated())
            .andExpect(jsonPath("id").value(2L));

    }
}

```

> @WebMvcTest 어노테이션을 통해 MVC 테스트를 할 것이라고 정의한다.    
> 웹 요청을 테스트하기 위해 MockMvc 객체를 주입한다.   
> WebMvc 에서는 Service 가 주입되지 않기 때문에 BpardService 는 MockBean으로 설정한다.    

> @Test 를 통해 테스트 함수로 설정한다.    
> given, will 을 통해 Service 에 수행되는 메소드의 리턴 값을 미리 설정한다.    
> mockMvc 에 웹 요청을 하고 예상되는 결과에 대해 정의함으로 정상동작되는지 확인할 수 있다.   


##  BDDMockito ##

BDD 의 given(주어진상황에서) when(어떤 일이 발생하면) then(어떠한 결과가 나온다.)의 given 을 설정하는데 자주 사용된다.     

참조 : [BDDMockito bealdung](https://www.baeldung.com/bdd-mockito "BDDMockito bealdung")  

## MockMvc ##
Cotroller 의 웹을 테스트 하기 위한 Mock   
perform을 통해 URL로 요청을 보낼 수 있으며   
andExpect를 통해서 결과 값에 대해 정의할 수 있다.    

참조 : [MockMvc bealdung](https://www.baeldung.com/integration-testing-in-spring "MockMvc bealdung")  