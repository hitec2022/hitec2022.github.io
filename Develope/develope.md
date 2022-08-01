---
layout: default
title: 개발
nav_order: 3
has_children: true
---

# Develop

hitec develop

개발환경 설정 페이지 세팅

개발자가 로컬PC의 환경에서 개발환경을 쉽게 세팅할 수 있는 방법이 무엇일까?
내 로컬 PC에 이것저것 막 깔리는게 싫다.

virtualbox를 설치해서 리눅스를 깔고
리눅스에 docker를 깔고
리눅스에도 이것저것 막 깔리는게 싫다. 

docker를 통해 개발 환경을 설정한다.    

어떻게 개발 할 것인가???
MSA 

개발을 하기 위해 처음 고민하는 것들
회원가입과 로그인 체계...
MSA 시대에 따로따로...
좋은 솔루션이 많이 나와있다. 
솔루션 검토를 해보자

keycloak
kong(konga)

회원가입 로그인 끝

frontend는 어떤걸 쓸까??
아무래도 대세는 spa
spa를 지원하는 frontend 프레임워크는??

vuejs, angular, react
react를 써보자

react 와 keycloak, kong 연동

기본 세팅은 끝난듯..

비지니스로직을 구현해 보자
spring으로 application을 만들고 gateway에 등록하고 react 로 화면 만들고...

간단한 게시판을 만들어볼까?

젠킨스를 설치해서 CICD를 해보자
젠킨스 설치하고
빌드할때 어플리케이션도 dockernizer 하고 (빌드 서버는 docker로 따로 띄우자)
docker 컨테이너 실행하는 것까지 만들고

postgresql을 docker로 실행

spring jpa 로 모델링 후 기본 api 만들고
react에서 화면 만들고 kong에 등록


댓글을 달아볼까??
MSA 시대에 맞게 댓글은 몽고DB를 써보자

mongdb를 docker로 실행

spring jap로 모델링 후 기본 api 만들고
react에서 화면 만들고 kong에 등록

react에서 댓글 불러본다. 

마이크로서비스가 2개인 MSA 다.

여기까지는 데이터 스토어도 싱글이고 관리도 개발자가 직접하는 정말 개발을 위한 환경이다

서비스가 많아지면 어떡하지??
ha 구성하고 제대로 설치하고 운영해보려면 어떡하지?
어떡하긴 우리에겐 kubernetes가 있자나

kubernetes는 다음에 이어서

