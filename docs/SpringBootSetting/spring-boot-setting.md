---
layout: default
title: Spring Boot Setting
nav_order: 5
has_children: true
date: 2023-03-29 12:00:00
last_modified_date: 2023-03-29 12:00:00
---

# Spring Boot Setting

> spring boot 에서의 기본적인 세팅방법을 설명하려고한다.    
> 상황에 맞는 좀 더 깊이 있는 세팅방법 보다는   
> 기본적인 세팅을 통해 일반적인 형태의 서비스를 만드는 방법을 설명하는데 중점을 두고자 한다.    
> Rest Server를 만들기 위한 기본 적인 세팅방법을 설명한다.    

스프링 부트 프로젝트 시작은 다음 링크 참조   
[spring boot init](../Develope/SpringBootInit.md)   

## 개발을 위한 스프링 기본 세팅 방법 

1. WebMvc 세팅
2. lombok 세팅 - 웹을 통해서 받는 객체 
3. Validation 세팅 - 객체의 validation 체크
4. JPA 세팅 - docker 로 DB 띄우기, DB 에 모델만들기 entity
5. mapstruct 세팅 - dto 와 entity 연결
6. ErrorException 세팅 - exception handler 
7. Logging 세팅 - log4j 로 설정
8. DataJpaTest with testcontainer 세팅 - DB 테스트하기 
9. WebMvcTest세팅 - 웹 테스트 하기
10. RestDocs 세팅 - 웹 테스트 후 문서 만들기

