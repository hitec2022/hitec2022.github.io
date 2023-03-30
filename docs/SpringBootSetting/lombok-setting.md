---
layout: default
title: lombok 세팅
nav_order: 2
parent: Spring Boot Setting
date: 2023-03-29 12:00:00
last_modified_date: 2023-03-29 12:00:00
---


# lombok 세팅 #   
lombok 은 클래스의 맴버변수에 대해 어노테이션을 통해   
getter, setter 등의 코드를 자동으로 생성해준는 java 라이브러리이다.    
소스가 간결해지며, 생산성을 높일 수 있다. 

## 메이븐 설정 ##  

pom.xml 파일에 dependency 추가
```
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>
```

pom.xml 파일의 build plugins 중 spring-boot-maven-plugin 에서 제외 
```
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <excludes>
            <exclude>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
            </exclude>
        </excludes>
    </configuration>
</plugin>
```

## Web Sample ##

보드 정보를 입력받는 DTO 클래스를 만든다. 
```
@Getter
@Setter
@AllArgsConstructor
public class BoardDTO {
    private long id;
    private String title;
    private String content;

    private String userName;
}
```
> @Getter, @Setter 등의 어노테이션을 통해 getId(), setId() 등의 메소드를 자동 생성한다.    

## lombok ##

자세한 사항은 롬복 홈페이지 참조 [Lombok Homepage](https://projectlombok.org/ "Lombok Homepage")    

주요 lombok 어노테이션   
* @Log - 로그 작성을 위한 어노테이션   
* @Getter/@Setter - getter, setter 함수를 자동으로 생성   
* @ToString - toString 함수를 자동으로 생성   
* @NoArgsConstructor, @RequiredArgsConstructor and @AllArgsConstructor - 생성자를 자동으로 생성   
* @Data - @ToString, @EqualsAndHashCode, @Getter/@Setter, @RequiredArgsConstructor 이 한꺼번에 적용   
* @Builder - builder 패턴으로 객체에 값을 세팅할 수 있도록 해주는 어노테이션   