---
layout: post
title:  Docker로 실행한 Jenkins 버전 업그레이드   
nav_order: 3
parent: 기술팁
---

## Jenkins Docker 실행 업그레이드       

> Jenkins를 Docker로 실행 후 새로운 버전으로 업그레이드 하고자 할 경우   

1. 컨테이너의 Jenkins_home을 로컬PC로 복사한다.    
    ```sh
    sudo docker cp <container>:/var/jenkins_home <local dir>
    ex) sudo docker cp hitec-jeknins:/var/jenkins_home ./
    ```
2. 새버전의 Jenkins docker 버전을 받는다.    
    ```sh
    sudo docker pull jenkins/jenkins:jdk11
    ```
3. 실행중인 컨테이너를 Stop 한다.    
    ```sh
    sudo docker stop <container>
    ex) sudo docker stop hitec-jenkins-old
    ```
4. 새로운 Jenkins 버전으로 실행한다.    
    ```sh
    sudo docker run -d -p 8080:8080 -p 50000:50000 -v <local dir>:/var/jenkins_home --restart=on-failure --name <new container name> jenkins/jenkins:jdk11
    ex) sudo docker run -d -p 8080:8080 -p 50000:50000 -v /home/ubuntu/jenkins/jenkins_home:/var/jenkins_home --restart=on-failure --name hitec-jenkins jenkins/jenkins:jdk11
    ```

    > 로컬PC 에 복사해 놓은 위치를 volume 마운트 해서 새 버전의 docker를 실행한다. 


> 초기 Docker container 가 jenkins_home을 로컬PC 의 디렉토리로 설정했다면 복사할 필요는 없다.    
> 로컬PC 의 디렉토리를 볼륨마운트 하고 싶지 않은 경우 새로운 버전의 Docker 이미지를 base 이미지로 하고 복사 해 둔 디렉토리를 이미지로 복사하는 Dockerfile 을 만들어서 실행 할 수 있다.    
