---
layout: default
title: put request cors 에러발생 시 
nav_order: 2
parent: 기술팁
---

## CORS   

> 서버의 리소스가 다른 서버를 호출 할 때 보안 상의 이슈가 발생하여 호출하지 못하게 막아놓음   
> 허용하기 위해서는 서버에서 호출해도 되는 서버를 알려줘야한다. (Access-Control-Allow-Origin) 

1. 스프링 프레임워크 사용 시 Controller 클래스에 @CrossOrigin 설정   
2. 기본적으로 GET, POST, HEAD 만 허용한다. 다른 메소드를 허용하려면 crossorigin 에 methods 설정   
3. 요청 하기 전에 되는지 먼저 확인 하기도 한다. (preflight)
4. preFlight 는 OPTIONS 메소드를 사용한다. OPTIONS 도 허용   
5. preflight 로 받은 내용을 얼마동안 가지고 있을 지 설정할 수 있다. (maxAge)
    ```
    @CrossOrigin(origins = {"http://localhost:3000"}, maxAge=3600, methods = {RequestMethod.GET, RequestMethod.POST, RequestMethod.HEAD, RequestMethod.OPTIONS, RequestMethod.PUT} )
    ```
6. 클래스에 설정하면 클래스에 적용되고 함수에 설정하면 함수에만 적용된다. 
7. 전체 적용을 하려면 config 파일을 만들어서 적용한다. 
    ```java
    @Configuration
    public class WebMvcConfig implements WebMvcConfigurer {
        @Override
        public void addCorsMappings(CorsRegistry registry) {
            registry.addMapping("/**")
                    .allowedOrigins("http://localhost:3000")
                    .allowedMethods("*") // 기타 설정
                    .allowedHeaders("*");
        }
    }
    ```
