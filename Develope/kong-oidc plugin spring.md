---
layout: default
title:  Kong OIDC Spring 사용자 정보           
nav_order: 10 
parent: 개발
---

# Kong OIDC Spring 사용자 정보       

> Kong의 OIDC 플러그인을 통해 인증을 받은 경우     
> spring 서비스에서는 인증 된 사용자의 정보를 알아야 할 경우가 있다. (게시글 작성자가 누구인지)   
> 앞에서 사용했던 (https://github.com/revomatico/kong-oidc.git) 의 경우    
> HTTP 의 HEADER 에 X-USERINFO 항목에 base64로 인코딩 된 채로 추가해서 보내진다.    

1. USERINFO 헤더정보 읽기   
    헤더정보를 읽는 방법은 크게 3가지가 있을 듯 하다.    
    1. Controller 에서 직접 읽는 방법 (@RequestHeader 사용)    
    2. interceptor 를 만들어서 URL 별로 등록하는 방법   
    3. Annotation 을 만들고 argument resolver를 만들어서 controller 에 적용하는 방법    

    > base64로 디코딩하고 json 파싱을 후 java 객체를 만드는 작업이 매번 동일하게 발생   
    > 적용 URL 을 controller 파일에서 바로 컨트롤 하는게 가독성이 좋을 듯   
    > 3번 방식으로 결정   

2. Annotation 생성   
    파라미터 타입의 어노테이션을 생성   
    ```java
    @Retention(RetentionPolicy.RUNTIME)
    @Target(ElementType.PARAMETER)
    public @interface AuthUser {}
    ```

3. argument resolver 생성   
    HandlerMethodArgumentReslover를 implements 하여 생성   
    AuthUser annotation 에 대해 AuthUserVo 클래스인 경우 적용   
    헤더에서 x-userinfo 의 값을 가져와서 base64로 디코딩 후 jackson으로 파싱하고 값을 가져와서 AuthUserVo 의 객체를 생성   

    ```java
    public class AuthUserArgumentResolver implements HandlerMethodArgumentResolver{
        private final ObjectMapper objectMapper = new ObjectMapper();

        @Override
        public boolean supportsParameter(MethodParameter parameter) {
            boolean isRegUserAnnotation = parameter.getParameterAnnotation(AuthUser.class) != null;
            boolean isAuthUserVo = parameter.getParameterType().equals(AuthUserVo.class);
        
            return isRegUserAnnotation && isAuthUserVo;
        }

        @Override
        public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {

            String xUserInfo = webRequest.getHeader("X-USERINFO");
            if(xUserInfo!=null){
                Base64.Decoder decoder = Base64.getUrlDecoder();
                String userInfo = new String(decoder.decode(xUserInfo));
                JsonNode userInfoNode = objectMapper.readTree(userInfo);

                String name = userInfoNode.get("name").textValue();
                String preferred_username = userInfoNode.get("preferred_username").textValue();
                String username = userInfoNode.get("username").textValue();
                String email = userInfoNode.get("email").textValue();
                String id = userInfoNode.get("id").textValue();
                
                return new AuthUserVo(name, preferred_username, username, email, id);
            }
            return null;
        }
    }
    ```

4. spring 설정   
    WebMvc 설정에서 AuthUserArgumentResolver 를 추가한다.    
    ```java
    @Configuration
    public class BlogBoardWebConfig implements WebMvcConfigurer{

        @Override
        public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers){
            resolvers.add(new AuthUserArgu서entResolver());
        }

    }
    ```

5. Controller 에서 사용    
    메소드의 파라미터 부분에 @AuthUser 어노테이션과 AuthUserVo 클래스로 설정을 하면   
    authUser 객체에 인증된 사용자의 username, email, id 등을 구할 수 있음   
    ```java
    @PostMapping("/board")
    @Transactional
    public BoardVo setBoard(@AuthUser AuthUserVo authUser, @RequestBody BoardVo boardVo){

        BoardUser boardUser = null;
        if(boardUserService.userExist(authUser.getId())){
            boardUser = boardUserService.getUser(authUser.getId());
        }else{
            boardUser = new BoardUser(authUser.getId(), authUser.getUsername(), authUser.getName(), authUser.getPreferred_username(), authUser.getEmail());
            boardUser = boardUserService.setUser(boardUser);
        }

        Board board = boardService.setBoard(new Board(null, boardVo.getTitle(), boardVo.getContent(), boardUser));
        return new BoardVo(board.getId(), board.getTitle(), board.getContent(), board.getBoardUser()!=null?board.getBoardUser().getUsername():null);
    }
    ```