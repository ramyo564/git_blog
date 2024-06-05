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
