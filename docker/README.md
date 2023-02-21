// docker record

1. 도커 설치

2. wsl --update

// image download // 패키지 매니저 다운로드
3. docker pull ubuntu:14.04

// /bin/bash 파일을 실행
// docker의 가상환경이 아닌 가상환경으로 접속
4. docker run -i -t ubuntu:14.04 /bin/bash

4-1. docker ps 확인
4-2. docker ps -a 종료된 컨테이너 확인

// 도커 실행
5. exit; 

// 도커 컨테이너 접속
5-1. docker exec -it '컨테이너명' /bin/bash

// 컨테이너 실행
6. docker start 'container 이름'

7-1. shell -> ctrl + d = 컨테이너 정지 
7-2. shell -> ctrl + p, ctrl + q = 컨테이너 정지하지 않고 컨테이너에서 빠져나옴

8. docker stop 'container 이름'

// docker 컨테이너 삭제
9. docker rm 'container 이름'

// docker 이미지 삭제
10. docker rmi '이미지 명'

// 가장 최신버전의 이미지 설치는 :latest
11. docker pull nginx:latest

// -d : 백그라운드에서 실행
// --name : 이미지 이름 지정
12. docker run -d --name test nginx:latest 