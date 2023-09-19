---

layout: single
title: " [Django DRF] recipe-app-api project (5) "
categories: Django
tag: [Python,"[Django DRF] Project recipe-app-api","[Django DRF] docker 설정"]
toc: true
toc_sticky: true
author_profile: false
sidebar:

---
# 도커 버전 설정

## Create project Dockerjile

우선 도커는 Alpine 을 사용한다.     
Alpine 은 경량화된 Linux 버전으로 Docker 컨테이너를 실행하는데 이상적이다. 불필요한 디펜던시가 없으며 최소한으로 필요한 요소만 포함되어 있어 Docker를 ㅁ매우 가볍게 사용할 수 있다.

Dockerfile
```python
FROM python:3.9-alpine3.13

LABEL maintainer="londonappdeveloper.com"

  

ENV PYTHONUNBUFFERED 1

  

COPY ./requirements.txt /tmp/requirements.txt

COPY ./app /app

WORKDIR /app

EXPOSE 8000

  

RUN python -m venv /py && \

    /py/bin/pip install --upgrade pip && \

    /py/bin/pip install -r /tmp/requirements.txt && \

    rm -rf /tmp && \

    adduser \

        --disabled-password \

        --no-create-home \

        django-user

  

ENV PATH="/py/bin:$PATH"

  

USER django-user
```

라벨은 이 도커 이미지를 유지 관리하는 사람의 정보를 명시하는 게 좋다.
`ENV PYTHONUNBUFFERED 1` 는 도커 컨테이너에서 파이썬을 실행할 때 권장되는 설정이다.     
파이썬에게 출력을 버퍼링하지 않도록 지시하며, 파이썬의 출력이 즉시 콘솔에 표시되어 파이썬 실행 어플리케이션에 콘솔로 메시지 지연 없이 전달된다.     

`COPY` 명령어는 Docer 이미지로 복사하는 개념이다.
`COPY ./app /app` 이 부분은 이후에 생성할 Django 앱을 포함하는 app 디렉토리를 컨테이너의 `/app` 경로로 복사할 수 있다.     


`WORKDIR`는 작업 디렉토리며 도커 이미지에서 명령이 실행되는 기본 디렉토리다.     
`RUN` 은 명령어를 실행하는 부분이다.
- 첫 번째 줄은 가상 황경을 만드는 명령어고
- 두 번째는 pip 업그레이드
- 세 번째는 디펜던시 설치
- 네 번째는 `django-user` 라는 이름의 사용자 생성
- 다섯 번째는 `/env/bin` 디렉토리를 `$PATH` 환경 변수에 추가하는 명령
- 여섯 번째는 `/app` 디렉토리의 소유자를 `django-user`로 변경하는 명령
- 마지막은 이미지를 실행할 때 사용할 사용자를 `django-user` 로 설정하는 명령이다.

도커를 사용할 때는 가상환경을 사용하지 않아도 되는 경우가 대부분이라 이에 대해서는 논란이 있지만 충돌 위험을 줄이기 위해서는 가상 환경을 생성 할 수 있다.     

ADD user 를 사용하는 건 root 사용자를 사용하지 않기 위해서다.    
root 사용자는 모든 엑세스 권한을 갖고 있다. 보안의 이유로 엑세스 권한을 제한한 유저를 새로 만들어서 사용하는게 좋은 방법이다.      

마지막 user 줄은 파일의 마지막 줄이여야 한다. 이렇게 쓰면 사용자가 user로 전환된다.

.dockerignore
```python
# Git
.git
.gitignore

# Docker
.docker

# Python
app/__pycache__/
app/*/__pycache__/
app/*/*/__pycache__/
app/*/*/*/__pycache__/
.env/
.venv/
venv/
```

파이썬 코드가 실행되면 캐시가 생성되며, 이를 .pycache 디렉토리에 저장한다. Docer 컨테이너에는 필요하지 않으며 문제를 일으킬 수도 있다.     
왜냐하면 로컬 컴퓨터에서 생성된 .pycache는 운영 체제에 특정한 것일 수 있고, Alpine 운영 체제에 특화된 것이 아닐 수 있기 때문이다.     

따라서 Docker 컨텍스트에서 이를 제외하기만 하면 컨테이너를 빌드하는 데 더 빠르게 할 수 있다.

app 폴더를 만들어주고 빌드

![](https://i.imgur.com/eEeFaip.png)

## Create Docker Compose configuration

docker-compose.yml
```python
|   |
|---|
|version: "3.9"|
||||
|||services:|
|||app:|
|||build:|
|||context: .|
|||ports:|
|||- "8000:8000"|
|||volumes:|
|||- ./app:/app|
|||command: >|
|||sh -c "python manage.py runserver 0.0.0.0:8000"|
```

프로젝트에 Docker Compose 구성 파일을 생성했다.
Docker-compose.yml

상단에 version이 있다.     
이는 사용할 Docker Compose 구문의 버전이다.     
Docker Compose가 새로운 버전의 구문을 릴리스하는 경우, 여기에 지정한 구문과 일치하는지 확인하는 버전 메커니즘이다.      
새로운 버전이 릴리스되더라도 이를 사용하여 구성이 깨지지 않도록 한다.

그리고 services를 지정한다. 이는 Docker Compose 파일의 주요 블록이다.
일반적으로 Docker Compose 파일은 애플리케이션에 필요한 하나 이상의 서비스로 구성된다.     

services에서는 AMP를 서비스의 이름으로 지정하며, 이 서비스는 우리의 Docker 파일을 실행한다.      
build context와 현재 디렉토리도 있다.     
이는 Docker Compose 파일 내의 현재 디렉토리에서 Docker 파일을 빌드하도록 지정한다.     
즉, app 서비스에 대한 컨텍스트는 현재 명령을 실행하는 루트 디렉토리다.
이는 여기서 사용된 마침표(.)의 의미입니다. 현재 Docker Compose를 실행하는 디렉토리를 사용한다는 의미다.     

그리고 포트 매핑이 있다.      
이를 통해 로컬 머신의 8000 포트를 Docker 컨테이너 내의 8000 포트에 매핑한다.
이를 통해 서버에 연결할 때 네트워크에 접근할 수 있다.     

다음으로 volumes가 있다. volumes는 시스템의 디렉토리를 Docker 컨테이너에 매핑하는 방법이다.
프로젝트에서 생성한 app 디렉토리를 컨테이너 내의 /app에 매핑하고 있다.      

이를 추가한 이유는 로컬 프로젝트에서 코드를 업데이트하면 실행 중인 컨테이너에 코드 변경 내용이 실시간으로 반영되도록 하기 위해서다.     
컨테이너를 재빌드할 필요 없이 코드 변경 내용을 자동으로 동기화할 수 있다.     

마지막으로 command가 있다. 이것은 서비스를 실행하는 데 사용되는 명령이다.
이 명령은 우리가 많이 사용할 Docker Compose run 명령을 통해 재정의할 수 있다. 그러나 기본적으로 명령을 지정하지 않으면 Docker Compose 파일에서 정의한 명령이 사용된다.     

터미널에서 `Docker-compose build`를 실행합니다. 이를 통해 Docker 이미지를 빌드할 수 있다.
![](https://i.imgur.com/Cae0ddE.png)

## Configure flake8

requirements.dev.txt
```python
flake8 >= 3.9.2,< 3.10
```

docker-compose.yml
```python
version: "3.9"

  

services:

  app:

    build:

      context: .

      args:

        - DEV=true

    ports:

      - "8000:8000"

    volumes:

      - ./app:/app

    command: >

      sh -c "python manage.py runserver 0.0.0.0:8000"
      # 참고로 윈도우는 127.0.0.1:8000
```

Dockerfile
```python
FROM python:3.9-alpine3.13

LABEL maintainer="londonappdeveloper.com"

  

ENV PYTHONUNBUFFERED 1

  

COPY ./requirements.txt /tmp/requirements.txt

COPY ./requirements.dev.txt /tmp/requirements.dev.txt

COPY ./app /app

WORKDIR /app

EXPOSE 8000

  

ARG DEV=false

RUN python -m venv /py && \

    /py/bin/pip install --upgrade pip && \

    /py/bin/pip install -r /tmp/requirements.txt && \

    if [ $DEV = "true" ]; \

        then /py/bin/pip install -r /tmp/requirements.dev.txt ; \

    fi && \

    rm -rf /tmp && \

    adduser \

        --disabled-password \

        --no-create-home \

        django-user

  

ENV PATH="/py/bin:$PATH"

  

USER django-user
```

도커파일에서는DEV=false 이지만 docker-compose에서 true 면 
`then /py/bin/pip install -r /tmp/requirements.dev.txt ; \` 코드가 실행된다.

이렇게 하면 배포 어플리케이션에서는 flake 8 이 깔리지 않고 의존성을 분리할 수 있다.

Docker Compose 에서 dev=true 로 바꾼다면 도커 이미지에도 함께 설치된다.


![](https://i.imgur.com/rRNgVL2.png)

```python
docker-compose run --rm app sh -c "flake8"
```
터미널에서 위의 명령어를 실행해서 빌드해주면 된다.

## Create Django project

도커 이미지안에 장고는 이미 생성되었으니 다음 커맨드를 입력하면 자동으로 장고 앱이 생성된다.

```python
docker-compose run --rm app sh -c "django-admin startproject app ."
```
![](https://i.imgur.com/7yRdpAz.png)

```python 
docker compose up
```
를 실행하면 장고가 실행된다.
참고로 터미널에서는 
![](https://i.imgur.com/33MXbeJ.png)
아래와 같이 0.0.0.0 으로 나오더라도 127.0.0.1:8000 으로 들어가야 페이지를 확인 할 수 있다.

![](https://i.imgur.com/zULqgya.png)
