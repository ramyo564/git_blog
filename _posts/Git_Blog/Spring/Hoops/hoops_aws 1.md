---
layout: single
title: "[Hoops PJ] 트러블 슈팅 & TIL (1)"
categories: Spring_Project_Hoops
toc: true
toc_sticky: true
author_profile: false
sidebar: 
tags:
  - TIL
  - 개발스터디
  - 99클럽
  - 99일지
  - 항해
---

# AWS

https://velog.io/@pds0309/EC2-key%EB%A1%9C-ssh%EC%A0%91%EC%86%8D-Permission-Denied-public-key-%ED%95%B4%EA%B2%B0%ED%95%98%EA%B8%B0

![](https://i.imgur.com/w3Nq0sN.png)

터미널에서 엑세스 접근이 금지라서 다시 .pem 키를 받았으나
아마 서버에 있는 내용과 맞지 않아서 접근이 안되는거 같았다.
-> 정보를 바꿔줌

## db 연결
일단 보안에서 퍼블릭으로 열어줘야함


test 코드에서 멈충
-> 스왑메모리 올려줌
https://diary-developer.tistory.com/32

## cms
sudo dd if=/dev/zero of=/swapfile bs=128M count=32
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
sudo free

sudo apt update
sudo apt upgrade -y
sudo apt install openjdk-17-jre-headless -y

sudo apt-get install redis-server -y
// 레디스 서버 확인
redis-cli ping

git clone --branch managaer-testcode https://github.com/hoops-project/backend.git

vi application.yml


그래들 
chmod +x ./gradlew

./gradlew clean build


sudo apt install net-tools -y
netstat -ntl

ubuntu@ip-172-31-42-1:~/backend/src$ cd ..
ubuntu@ip-172-31-42-1:~/backend$ ls
README.md  build  build.gradle  gradle  gradlew  gradlew.bat  pull_request_template.md  settings.gradle  src
ubuntu@ip-172-31-42-1:~/backend$ cd build
ubuntu@ip-172-31-42-1:~/backend/build$ cd libs
ubuntu@ip-172-31-42-1:~/backend/build/libs$ ls
hoops-0.0.1-SNAPSHOT-plain.jar  hoops-0.0.1-SNAPSHOT.jar

sudo java -jar hoops-0.0.1-SNAPSHOT.jar

utf - 8 오류

![](https://i.imgur.com/eNxOMBj.png)

https://ko.ihoctot.com/post/how-to-fix-mariadb-incorrect-string-value




db 테스트 문제

![](https://i.imgur.com/M2vKstB.png)


거부
![](https://i.imgur.com/OO0wOS2.png)



### 변경사항
<!-- 이 PR에서 어떤점들이 변경되었는지 기술해주세요. 가급적이면 as-is, to-be를 활용해서 작성해주세요.  -->
**AS-IS**
기존에 채팅 기능 오류 -> 웹소켓 끊김
**TO-BE**
- 기존 채팅 서비스 기능 주석 처리
- Vscode Live Server 로 프론트 테스트 
- 현재 테스트를 위해 채팅 메세지는 캐시로만 구현

[웹소켓 프론트 테스트.zip](https://github.com/hoops-project/backend/files/15446953/default.zip)

## 테스트 방법

### 1. vscode의 extentions의 live server를 검색해서 설치
![liveServer](https://github.com/hoops-project/backend/assets/103474568/c0af16e4-73a1-48e7-ac60-eb25a2a756bf)

### 2. 설치 후 톱니바퀴 클릭 -> Extentions settings 클릭
![liveServer설정](https://github.com/hoops-project/backend/assets/103474568/24ba4741-85a0-478a-9455-47c9c7c4df1a)

### 3. json 설정 클릭
![liveServerJson 설정](https://github.com/hoops-project/backend/assets/103474568/bfaed3bf-684b-4d69-a680-8d102b3671ff)

### 4. port 번호 5000 으로 고정
![image](https://github.com/hoops-project/backend/assets/103474568/720c86b1-6305-446d-9bb5-0e2072fd9bf1)

### 5. 실행
![liveServer 실행](https://github.com/hoops-project/backend/assets/103474568/9d04f394-2922-416a-b29a-cf27b988c6b2)

### 6. 로그인
![image](https://github.com/hoops-project/backend/assets/103474568/6989af84-f840-487e-9dbb-ae3d4b7a5ace)
- 스프링 서버 + 로컬 db 연결
- redis 서버 구동 
- 이후에 로그인 진행 -> db에 저장되어있는 값이 맞을 경우만 로그인 됨
- 웹소켓 확인을 위해 익스플로창 여러개 준비 (크롬의 경우 시크릿모드로 짆애)

![image](https://github.com/hoops-project/backend/assets/103474568/70a4d387-3585-4966-aa28-289179297da3)


### 7. gameId 입력
![image](https://github.com/hoops-project/backend/assets/103474568/f656d8f4-c538-489c-ae36-60f75bd5af27)
- 로그인 -> 게임 id 입력
- 현재 구현된 프론트 페이지에서는 내가 참여하고 있는 각 경기마다 gameId 정보가 함께 있습니다.
- 실제 프론트단에서는 각 경기에 담겨있는 gameId 값으로 자동 처리하지만 현재 간이 테스트에서는 실제 경기를 만든 후 생성되는 pk 값을 수동으로 넣어주면 됩니다.
- 





### 테스트
<!-- 본 변경사항이 테스트가 되었는지 기술해주세요 --> 
- [ ] 테스트 코드
- [ ] API 테스트 