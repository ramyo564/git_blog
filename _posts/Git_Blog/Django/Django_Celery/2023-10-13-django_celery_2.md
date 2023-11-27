---
layout: single
title: " [Django Celery] Celery (2)"
categories: Django_Celery
tags:
  - Python
  - Celery
  - WSL2
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# Creating and Registering Celery Tasks in Django
`셀러리 초기설정하기!`

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
- 항상 뭐 찾아보면 윈도우에서 명령어가 달라서 또 찾아봐야 했는데 이번에 우분투 연습을! (윈도우에서도 맥처럼 멀티부팅으로 우분투를 사용 가능하다고 한다!)
- 윈도우로 실행하려면 아래와 같이 명령어를 손봐야한다.

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

```
python manage.py startapp cworker
```

우분투에서는 위와 같은 방식으로 진행하면된다.   
(근데? 실제 우분투에서는 python3 로 명령해야한다. 근데? vs코드 같은 에디터에서 가상환경을 만들어놓고 실행할 때는 그냥 python 도 된다. 뭔가 안되면 적절하게 그 때 그 때 찾아보면 됨)


## Celery

루트 폴더에 셀러리파일을 만들어준다.

*celery.py*
```python
import os
from celery import Celery

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'djcelery.settings')

app = Celery("djcelery")
app.config_from_object("django.conf:settings", namespace="CELERY")

@app.task
def add_numbers():
    return

app.autodiscover_tasks()
```

이렇게 설정하면 셀러리가 루트폴더의 셋팅에 CELERY로 시작하는 파일을 알아서 찾아준다.     

뷰처럼 함수 또는 클래스로 작업을 만들 수 있다.     

```
  celery:
    container_name: celery
    build:
      context: ./djcelery
    command: celery --app=djcelery worker -l INFO
    volumes:
      - ./djcelery:/usr/src/app/
    environment:
      - DEBUG=1
      - SECRET_KEY=zxzxw2sdsdaas219fj01j9f
      - ALLOWED_HOSTS=localhost,127.0.0.1
    depends_on:
      - redis
```

- 초기 설정 이후 셀러리를 실행할 수 있게 커맨드 라인을 심어준다.

*tasks.py*
```python
from celery import shared_task

@shared_task
def sharedtask():
    return
    
```

### `__init__.py`
루트 폴더에 있는 celery 파일에서 셀러리 인스턴스를 만들고 장고 인스턴스 내부에 메시지 브로커 (레디스) 로 작업을 보낸다.

따라서 루트 폴더에 있는 `__init__.py` 파일에 셀러리와 장고 프로젝트를 연결시켜서 장고 프로젝트가 실핼 될 때마다 해당 파일을 읽으면서 샐러리 앱에 대한 엑세스를 초기화 한다.

```python
from .celery import app as celery_app

__all__ = ("celery_app",)
```

이렇게 해서 settings.py에서 Celery 어플리케이션을 설정하고 관리할 수 있으며 celery_app 이라는 이름으로 작업을 시킬 수 있다.

등록한 cworker 앱에 tasks.py 를 만들어준 후 터미널에서 도커에 진입한뒤 파이썬 쉘에서 명령하면 된다.

```
docker exec -it django /bin/sh
python manage.py shell
```

![](https://i.imgur.com/Hq0ohnt.png)

![](https://i.imgur.com/gUhPVQJ.png)

에러가 발생했을 경우 이슈를 찾아서 해결하면 되는데 깜빡하고 기록을 안했다.      
문제 없이 환경설정을 제대로 한다면 위와 같이 잘 작동한다.