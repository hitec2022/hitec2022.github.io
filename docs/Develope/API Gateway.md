---
layout: default
title: API Gateway (Kong)    
nav_order: 3
parent: 개발
date: 2022-10-31 12:00:00
last_modified_date: 2022-10-31 12:00:00
---

# API Gateway   

> MAS 정신에 따라 서비스가 여러개가 생기게 되면 URL 이 너무 많고 어지러워진다   
> 한 곳에서 분배해주면 좋겠다   
> 같은 서비스의 ha 구성에 로드 발란스도 해주면 좋겠다.   
> 로그인 확인도 해주면 좋겠다.   
> 그래서 API Gateway 가 필요하고, 직접 개발해도 되겠지만   
> zuul, krakend, kong 등 만들어져 있는게 있다.    
> spring 에도 spring cloud gateway 라는게 있다.    
> 만들어져 있는거 깔아서 쓰자.... kong으로 해보자..

1. kong docker로 실행   
    > kong gateway 와 kong 을 설정할 수 있는 konga 설치   
    > db 없이 개발용으로 설치 가능하나 db 없이 설치할 경우 서비스 생성이 되지 않음   
    > db 는 postgresql db 로 설치    


    1. postgresql db 실행 (docker)

        ```
        sudo docker run --name hitec-kong-db -d -e "POSTGRES_DB=kong" -e "POSTGRES_USER=kong" -e "POSTGRES_PASSWORD=kong" postgres:9.6
        ```

        > 컨테이너 이름, db 이름, db 사용자, db 비밀번호 설정   
        > virtualbox 의 네트워크 설정에서 5432 포트 포트포워딩 설정   

        > postgres 9.6버전에서 테스트 12,13,14 버전에서는 동작하지 않음   
    
    2. kong 실행 (docker)   
        1. kong 실행을 위한 db 초기화    
   
            ```sh
            sudo docker run --link hitec-kong-db:hitec-kong-db --rm -e "KONG_DATABASE=postgres" -e "KONG_PG_HOST=hitec-kong-db" -e "KONG_PG_DATABASE=kong" -e "KONG_PG_USER=kong" -e "KONG_PG_PASSWORD=kong" -e "KONG_CASSANDRA_CONTACT_POINTS=192.168.56.1" kong/kong-gateway kong migrations bootstrap
            ```

            > kong_pg_host 에 로컬 IP, kong_database는 postgres 로 입력   
            > db 실행 시 입력한 DB 이름, 사용자이름, 비밀번호 입력    

        2. kong 실행   
   
            ```sh   
            sudo docker run -d --name hitec-kong \
            --link hitec-kong-db:hitec-kong-db \
            -e "KONG_DATABASE=postgres" \
            -e "KONG_PG_HOST=hitec-kong-db" \
            -e "KONG_PG_USER=kong" \
            -e "KONG_PG_PASSWORD=kong" \
            -e "KONG_CASSANDRA_CONTACT_POINTS=192.168.56.1" \
            -e "KONG_PROXY_ACCESS_LOG=/dev/stdout" \
            -e "KONG_ADMIN_ACCESS_LOG=/dev/stdout" \
            -e "KONG_PROXY_ERROR_LOG=/dev/stderr" \
            -e "KONG_ADMIN_ERROR_LOG=/dev/stderr" \
            -e "KONG_ADMIN_LISTEN=0.0.0.0:8001, 0.0.0.0:8444 ssl" \
            -e "KONG_ADMIN_GUI_URL=http://192.168.56.1:8002" \
            -p 8000:8000 \
            -p 8443:8443 \
            -p 8001:8001 \
            -p 8444:8444 \
            -p 8002:8002 \
            -p 8445:8445 \
            -p 8003:8003 \
            -p 8004:8004 \
            kong/kong-gateway
            ```

            > virtualbox 의 네트워크설정 > 포트포워딩 설정에서 8000, 8443, 8001, 8444, 8002 설정 확인   
            
            ```sh
            curl http://192.168.56.1:8000
            ```

            > 요청 시 message:no Route matched with those values 가 나오면 설치 완료   

    3. Kong Manager   
        http://192.168.56.1:8002 로 브라우저에서 열면 Kong GUI 화면이 나온다.    
        ![kong manager](../image/Dev/kongman1.png)    

2. konga docker로 실행 ([pantsel konga](https://github.com/pantsel/konga "Pantsel Konga"))  
    > Kong Manager 와 유사한 GUI 툴이 있다. Kong Manager 가 부족한 것 같으면 깔아보자   
    > kong 은 admin 포트(기본 8001) 를 통해 웹 호출로 설정 할 수 있다.    
    > 하지만 보기에 불편하여 웹 페이지 형태의 설정 어드민 툴인 konga를 지원한다.    

    1. konga db init   
   
        ```
        sudo docker run --link hitec-kong-db:hitec-kong-db --rm pantsel/konga:latest -c prepare -a postgres -u postgresql://kong:kong@hitec-kong-db/kong
        ```

    2. konga 실행    
        ```
        sudo docker run -d -p 1337:1337 \
        --link hitec-kong-db:hitec-kong-db \
        -e "TOKEN_SECRET=123456789" \
        -e "DB_ADAPTER=postgres" \
        -e "DB_HOST=hitec-kong-db" \
        -e "DB_PORT=5432" \
        -e "DB_USER=kong" \
        -e "DB_PASSWORD=kong" \
        -e "DB_DATABASE=kong" \
        -e "NODE_ENV=production" \
        --name=hitec-konga \
        pantsel/konga
        ```

        > virtualbox 의 네트워크설정 > 포트포워딩 에 1337 포토 설정 확인    

    3. konga admin user 설정   
   
        > local pc의 브라우저에서 다음과 같이 입력   

        ```
        http://192.168.56.1:1337
        ```

        ![konga admin](../image/Dev/kong1.png)    

        > username, email, password 설정   

    4. konga 에서 kong 연결 설정   
   
        > 설정한 username password로 로그인   
        > 첫 로그인 시 konga 와 kong 연결 설정 화면이 나옴   

        ![konga connection](../image/Dev/kong2.png)    

        > 이름을 설정하고, 설치된 kong 의 admin url을 입력 (ex http://192.168.56.1:8001)   

        ![konga dashboard](../image/Dev/konga3.png)    