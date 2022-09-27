---
layout: default
title: react js로 spa 시작하기   
nav_order: 5
parent: 개발
---

# React JS 로 SPA 시작하기   
> SPA(Single page application) vs SSR(Server Side Rendering) vs SSG(Static Site Generator)   

> SPA는 웹 페이지 리소스를 모두 받은 후 요청에 대한 데이터만 받아서 클라이언트 상에서 rendering   
> SSR은 요청에 대한 웹 페이지를 서버에서 만들어서 클라이언트로 보내는 방식   
> SSG는 웹 페이지를 모두 미리 만들어 놓고 요청 한 페이지를 보내는 방식   

> UI/UX 가 유연하고 네이티브 어플리케이션 같은 효과를 줄 수 있는 SPA 방식으로 개발   
> angular js, vue js, react js 등이 있지만 React JS를 선택하기로 했다. 

1. Node JS 설치
    > front end 개발에도 필요한 라이브러리들이 있고, 테스트를 실행해볼 수 있어야 한다.    
    > 그런 기능들을 지원해주는 npm 을 사용하기 위해서 nodejs를 설치한다.    
    1. nodejs 다운로드 [Nodejs 다운로드](https://nodejs.org/en/)   
        LTS 혹은 Current 버전을 다운로드 (LTS 가 안정화 버전)
    2. 다운로드 받은 파일 더블클릭 
    3. 넥스트 열심히 누르면 설치 완료   
    4. 설치확인   
   
        ```
        node -v
        npm -v
        ```
        버전 정보가 나오면 설치 완료   

2. react 프로젝트 생성   
    1. 프로젝트를 위한 디렉토리를 생성 후 프로젝트 생성   
        ```sh
        npx create-react-app <app name>
        ex) npx create-react-app hitec-front
        ```
        > npm start : 개발 장비에서 개발 된 페이지를 볼수 있는 서버 동작   
        > npm run build : 운용장비에 적용하기 위한 정적 리소스 생성    
    2. 프로젝트 확인   
        프로젝트 디렉토리에서 터미널을 열고 아래 명령어 입력 후 브라우저 확인   
        ```sh
        npm start
        ```
        ![react init](../image/Dev/react1.png)   

3. 리덕스 설정   
    리액트로 개발할 때 상태관리를 해주면서 프로젝트를 좀 더 효율적으로 구성할 수 있게 도와주는 라이브러리이다.   
    1. 리덕스 설치   
        react 개발 디렉토리에서   
        ```sh
        npm i react-redux
        npm i redux
        ```

4. 화면 설계   

5. 컴포넌트 생성   

6. 서버와 연결   