---
layout: default
title: Log4j 세팅
nav_order: 7
parent: Spring Boot Setting
date: 2023-03-29 12:00:00
last_modified_date: 2023-03-29 12:00:00
---

# Log4j 세팅 #   
springboot에는 기본적으로 slf4j와 logback이 포함되어 있다.    
slf4j는 로그라이브러리들(logback, log4j2, java.util.logging 등) 을 통합해서 사용할 수 있게 해준다.    
slf4j로 로그를 남기게 되면 로그라이브러리를 변경해도 소스 수정이 따로 필요가 없이 적용이 된다.    

기본 로그라이브러리가 logback 이지만 Log4j2로 적용하려고 한다. 

## 메이븐 설정 ##

```xml
<!-- using spring mvc (exclude logback) -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>

<!-- log4j2 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```
> logback 이 기본으로 설정되어 있으므로 기본 log 의존성을 제외한다.    
> spring boot 에서 제공하는 log4j2 의존성을 추가한다.    

## Log4j2 설정 ##
log4j2 의 설정은 application.yml 과 같은 위치인 resources 에 log4j2.xml 파일로 설정한다.    
```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
  <Appenders>
    <Console name="Console" target="SYSTEM_OUT">
      <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
  </Appenders>
  <Loggers>
    <Root level="info">
      <AppenderRef ref="Console"/>
    </Root>
  </Loggers>
</Configuration>
```
> 단순히 info 레벨로 system.out으로 로그를 남기는 설정이다.    
> 설정파일을 통해 다양하게 로그를 남길 수 있다.    
## Sample Code ##
```java
@Slf4j
public class testClass {
    public void logtest(){
        log.debug("Debug log message");
        log.info("Info log message");
        log.error("Error log message");log.info();
    }
}
```
> lombok 에는 Slf4j를 지원하는 @Slf4j 어노테이션이 있다.    
> 로그를 남기고자 하는 위치에 log 객체를 사용하여 로그 작성   
> 남기고자 하는 로그 레벨로 로그 작성   

## Log4j2 ##

자세한 사항은 다음 참조 
[Log4j2 manual](https://logging.apache.org/log4j/2.x/manual/index.html "Log4j2 manual")    

