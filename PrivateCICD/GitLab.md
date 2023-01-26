---
layout: default
title: gitlab설치
nav_order: 1
parent: PrivateCICD
---

# GitLab 설치   

## 1. gitlab install with Docker

1. gitlab을 Docker로 기동 (출처 : [gitlabDoc][gitlab doc docker]  )

   [gitlab doc docker]: https://docs.gitlab.com/ee/install/docker.html "Gitlab Docker"   

    1. devops home 디렉토리를 만든다. 
        ```sh
        mkdir <devops_home>
        ex) mkdir devops
        ``` 
    2. GITLAB_HOME 설정   
        홈 디렉토리의 .bashrc 파일을 열어서 가장 아래에 다음 추가   
        ```
        export GITLAB_HOME=<gitlab_home_path>
        ex) export GITLAB_HOME=/home/ubuntu/devops/gitlab
        ```
        

        설정한 환경변수를 적용   
        ```sh
        . .bashrc
        ```

    3. GitLab 을 컨테이너로 실행   
        ```sh
        sudo docker run --detach \
            --hostname 172.30.1.9 \
            --publish 443:443 --publish 80:80 --publish 8085:8085 \
            --name gitlab \
            --restart always \
            --volume $GITLAB_HOME/config:/etc/gitlab \
            --volume $GITLAB_HOME/logs:/var/log/gitlab \
            --volume $GITLAB_HOME/data:/var/opt/gitlab \
            --shm-size 256m \
            gitlab/gitlab-ee:latest
        ```
        > hostname : 설치되는 서버의 IP 혹은 도메인   
        > publish : 사용 포트에 대한 포트포워딩   
            >> 22번포트 제외 (ssh를 통한 git 연결은 하지 않을 예정 )   
            >> 8085포트 추가 (내재되어 있는 mattermost 사용 시)
