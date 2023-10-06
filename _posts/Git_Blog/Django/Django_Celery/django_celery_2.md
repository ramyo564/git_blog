---
layout: single
title: " [Django Celery] Celery (2)"
categories: Django_Celery
tags:
  - Python
  - Celery
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# Creating and Registering Celery Tasks in Django

- 도커에 컨테이너를 만들어서 연습해서 셀러리를 실행할 때 도커 컨테이너에 명령을 실행해줘야 한다. 


![](https://i.imgur.com/Apcdswt.png)


![](https://i.imgur.com/8c0ajxB.png)


```
$ docker exec -it django /bin/sh
OCI runtime exec failed: exec failed: unable to start container process: exec: "C:/Program Files/Git/usr/bin/sh": stat C:/Program Files/Git/usr/bin/sh: no such file or directory: unknown
```

- 윈도우 환경에서는 안된다.
- 드디어 WSL2를 실행할 때가 왔다.

![](https://i.imgur.com/KpWiFLU.png)

- 잘된다. 
- 항상 뭐 찾아보면 윈도우에서 명령어가 달라서 또 찾아봐야 했는데 이번에 우분투 연습을! (참고로 윈도우에서도 맥처럼 멀티부팅으로 우분투를 사용 가능하다고 한다!)
- 참고로 윈도우로 실행하려면 아래와 같이 명령어를 손봐야한다.

[참고](https://github.com/docker/for-linux/issues/246)

```
winpty docker exec -it django //bin//sh
```

![](https://i.imgur.com/2VBcagV.png)

- 참고로 도커환경은 이전과 다르게 간단하게 만들었다.
- 그러나저나 WSL이 뭔지 복습을 해볼까?

### WSL

현재 우분투를 사용중이며 WSL2 환경이다.     
WSL2는 리눅스 커널을 사용하는데 이게 무슨 소리냐면 WSL2는 윈도우 시스템에서 리눅스 커널을 가상화해서 실행한다.      
*리눅스 커널은 하드웨어와 소프트웨어 간의 인터페이스 역할을 한다.*

WSL2 는 리눅스와 윈도우간의 상호 운영성을 위한 기술이며 우분투는 리눅스의 배포판중 하나다.      
이번에 알게 된 사실은 우분투 이외에도 Fedora, Debian, Arch Linux등 여러가지 배포판이 있고 각각 특징이 있다.       

그리고 WSL1 과 WSL2가 있는데 WSL1은 윈도우 운영체제와 리눅스 커널 간의 중간 계층을 통해 리눅스 바이너리를 실행한다.     
하지만 호환성이나 성능에 문제가 있고 WSL2는 커널을 가상화하며 윈도우 시스템 위에서 실행한다.      
호환성과 성능면에서 더 좋으며 윈도우 시스템 자원에 직접 엑세스할 수 있도록 할 수 있다. WSL2 가상화는 Hyper-V 기술을 사용하며 리눅스 커널을 포함해서 전체 리눅스 환경을 가상 머신으로 구현한다.     

좀 틀린 비유지만 파이썬 venv 랑 비슷하다(물론 다르다.)
Hyper-V는 하드웨어 가상화 -> 물리적 하드웨어에서 분리된 가상환경
파이썬 venv는 프로젝트 분리 -> 각 라이브러리 및 의존성을 격리해서 패키지의 독립적인 복사본을 갖는다.


## WSL Error

![](https://i.imgur.com/F2jT2n6.png)

파일이 분명히 있는데도 왜 실행을 못하니?

https://sungbumv.tistory.com/12
```
sudo apt-get install libc6-i386 libc32gcc1
```

```
sudo apt-get install ia32-libs g++-multilib
```

![](https://i.imgur.com/hT5r3Zo.png)






![](https://i.imgur.com/aFeVeFC.png)


![](https://i.imgur.com/0WNmii6.png)


https://devicetests.com/fix-docker-service-not-found-error-ubuntu
![](https://i.imgur.com/kVuAwEn.png)
