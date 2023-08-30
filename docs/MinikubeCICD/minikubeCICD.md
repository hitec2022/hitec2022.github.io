---
layout: default
title: minikubeCICD
nav_order: 5
has_children: true
date: 2023-08-15 12:00:00
last_modified_date: 2023-08-15 12:00:00
---

# minikubeCICD

kubernetes 가 여기저기에서 대세로 쓰여지고있다.

쿠버네티스에서는 CICD 를 어떻게 구축하면 좋을까 하는 생각에 처음으로 구축해본 기록이다. 

개인PC에서 해봤으며 테스트를 해본 PC는 인텔 기반의 맥북이다. 

minikube driver는 docker 상에서 돌아가도록 하려고한다. 

CICD를 위해

소스관리 - gitlab 을 설치    
이미지관리 - minikube 의 registry addon을 사용   
빌드 - jenkins 설치     
배포 - argocd 설치 

를 하려고한다. 

각각의 설치와 기본적인 설정을 통해서 쿠버네티스에서의 CICD환경을 세팅해보고
kubernetes가 아닌 minikube를 사용하기때문에 알아야 할 팁들을 정리하고자한다.   
> 쿠버네티스 공부하려고 minikube까는데 minikube를 잘 몰라서 못하는 경우가 있다. 


1. minikube 설치 및 기동   
2. gitlab설치   
3. minikube registry 설치
4. jenkins 설치 및 기동   
5. argocd 설치 및 기동   
6. gitlab 소스 푸쉬    
7. jenkins pipeline(build and delivery)    
8. deploy to minikube(argocd) 
9. automation (webhook)