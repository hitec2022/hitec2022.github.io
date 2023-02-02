---
layout: default
title: PrivateCICD
nav_order: 4
has_children: true
---

# PrivateCICD

나만의 CI CD 환경을 만들어보자

회사나 프로젝트의 내부 환경에서만 개발과 빌드 배포를 하고자 한다면
내부에서만 동작하도록 설정한다. 

CI 환경
소스 형상관리, 빌드, 바이너리 저장소

CD 환경
배포는 환경에 따라 달라질 수 있다. 
빌드 툴에서 ssh를 통해 배포할 수도 있고
쿠버네티스의 경우 kubectl 을 통해 배포할 수도 있다. 
컨테이너로만 띄웠을 경우 docker 명령어를 사용할 수도 있다. 
그리고 그런 배포를 도와주는 툴들도 다수 존재한다. 

소스형상관리는 gitlab으로 하고자 한다. 
자동빌드는 jenkins를 쓰자
바이너리 저장소로는 nexus를 사용한다. 

개발자가 개발하면서 
gitlab에 소스를 올리고 
jenkins 에서 빌드를 수행 한 후 
nexus 에 빌드 결과물을 올린다. 






gitlab install  
	project 생성  
	project 소스 올리기  

jenkins install  
	빌드환경설정  
	job 생성  
	gitlab project 설정  

nexus install  
	maven proxy, host, group 저장소 생성  
	docker proxy, host, group 저장소 생성  

파이프라인작성