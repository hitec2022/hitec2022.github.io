---
layout: default
title: gitlab설치
nav_order: 1
parent: PrivateCICD
---

# GitLab 설치   

## gitlab install with Docker

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

    4. 로그확인   
        설치하는 동안 혹은 운영 중에 로그를 확인해 볼 수 있다.    
        ```sh
        sudo docker logs -f gitlab
        ```

    5. 설치 확인 및 로그인   
        ![gitlab 설치](../image/PrivateCICD/gitlab1.png)   

        * username : root
        * password
            ```sh
            sudo docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password
            ```
        
        > 초기 로그인 후에 비밀번호 변경   

## gitlab 설정 변경 및 재시작   
1. gitlab 의 설정파일은 /etc/gitlab/gitlab.rb 파일이다.   
    1. 컨테이너에 들어가서 수정할 수도 있으며, 
        ```sh
        sudo docker exec -it gitlab /bin/bash
        vi /etc/gitlab/gitlab.rb
        ```
    2. 컨테이너에 바로 수정 명령어를 보낼 수 있다. 
        ```sh
        sudo docker exec -it gitlab editor /etc/gitlab/gitlab.rb
        ```
2. 설정파일 수정 후 컨테이너 재기동을 통해 설정파일을 적용한다.   
    ```sh
    sudo docker restart gitlab
    ```


## permission problems   
> 컨테이너로 host 의 디렉토리를 mount 하였기 때문에   
> host 에서의 작업 등으로 인해 디렉토리 및 파일의 권한이 변경될 수 있다.    
> 이런 경우 gitlab에서 permission 문제가 발생한다.    

```sh
sudo docker exec gitlab update-permissions
sudo docker restart gitlab
```