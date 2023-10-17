---
layout: single
title: " [Django Celery] Celery (3)"
categories: Django_Celery
tags:
  - Python
  - Celery
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# Creating a new standalone Celery Worker

Message Provider (django) -> Message Broker (Redis) -> Celery Worker (Celery) -> Result Backend (DB)

## Reasons for using Django as a Celery:

- Familiarity and Integration
- Shared Codebaase
- Access to Database and ORM
- Task Result Integration

## 여러 개의 셀러리를 만들어보자

- 셀러리는 각각 따로 동작한다.
- 따라서 각각의 셀러리에게 각기 다른 작업을 할당 시킬 수 있다.

![](https://i.imgur.com/cJM7VgE.png)

- 장고안이 아닌 외부 폴더에 셀러리 환경을 만들어준다.

*celeryconfig.py*
```
broker_url = "redis://redis:6379/0"
result_backend = "redis://redis:6379/0"
```


*celerytask.py*
```python
from celery import Celery

app = Celery('tasks')
app.config_from_object('celeryconfig')

@app.task
def add_numbers():
    return
```
- 여기서 tasks 부분은 해당 셀러리의 이름일 뿐 실제 모듈과 같을 필요는 없다. 다만 이게 어느 부분에서 사용되는지 이해하기 쉽게 네이밍 하는게 좋다
- 또한 주의해야하는 부분은 celery.py 이름이 겹치지 않게 하는 것 -> 루트 폴더에 있는 celery.py와 이름이 같으면 안된다.

*Dockerfile*
```
FROM python:3.11.4-alpine
WORKDIR /usr/src/app

# prevent Python from writing .pyc files
ENV PYTHONDONTWRITEBYTECODE 1

# ensure Python output is sent directly to the terminal without buffering
ENV PYTHONUNBUFFERED 1

RUN pip install --upgrade pip
COPY ./requirements.txt /usr/src/app/requirements.txt
RUN pip install -r requirements.txt

COPY . /usr/src/app/
```

*requirements.txt*
```
celery==5.3.0
redis==4.5.5
```


```
  celery1:

    container_name: celery1

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

      - django

  

  celery2:

    container_name: celery2

    build:

      context: ./celeryworker

      dockerfile: Dockerfile

    command: celery -A celerytask worker -l INFO

    volumes:

      - ./celeryworker:/usr/src/app/

    depends_on:

      - redis

      - django
```

- 컨테이너 이름이 중복되지 않도록 변경 및 외부 폴더 경로에 맞춰서 명령어를 수정해주고 -A는 --app 랑 같다

## Task Routing

- Celery Task 라우팅은 어떤 셀러리가 어떤 작업을 실행해야하는지 결정하는 프로세스다.
- 이를 통해 특정 셀러리에 따라 작업 분배를 제어할 수 있다

### Overview of Task Routing:

- Efficient and intelligent distribution of tasks
- Process of determining the destination of tasks
- Allows you to control how tasks are dispatched to worker nodes

### Benefits of Task Routing:

- Improved Scalability
- Load Balancing
- Granular Control

### Advanced Routing Techniques:

- Dynamic Routing based on Runtime Conditions
- Routing based on Task Arguments or Context
- Using External Routing Strategies or Plugins

## Configuring Task Routing

![](https://i.imgur.com/NWx7YzG.png)

### 셀러리를 통해 작업을 나눠보자

*docker-compose.yml*
```
  celery1:

    container_name: celery1

    build:

      context: ./djcelery

    command: celery --app=djcelery worker -l INFO -Q queue1

    volumes:

      - ./djcelery:/usr/src/app/

    environment:

      - DEBUG=1

      - SECRET_KEY=zxzxw2sdsdaas219fj01j9f

      - ALLOWED_HOSTS=localhost,127.0.0.1

    depends_on:

      - redis

      - django

  

  celery2:

    container_name: celery2

    build:

      context: ./celeryworker

      dockerfile: Dockerfile

    command: celery -A celerytask worker -l INFO -Q queue2

    volumes:

      - ./celeryworker:/usr/src/app/

    depends_on:

      - redis

      - django
```


*djcelery/djcelery/celery.py*
```python
import os
from celery import Celery

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'djcelery.settings')

app = Celery("djcelery")

app.config_from_object("django.conf:settings", namespace="CELERY")

app.conf.task_routes = {

    "cworker.tasks.task1": {'queue': 'queue1'}, "cworker.tasks.task2": {'queue': 'queue2'}

    }

app.autodiscover_tasks()
```

- 루트 폴더에서 각 키 값으로 셀러리 작업할당을 명시해준다.

*djcelery/cworker/task.py*
```python
from celery import shared_task

@shared_task
def task1():
    return

@shared_task
def task2():
    return
```

그후 외부 셀러리가 해당 루트의 작업을 인식 할 수 있도록 추가 환경서정을 해준다.

![](https://i.imgur.com/SFbdfF4.png)

*celeryworker/celerytask.py*
```python
from celery import Celery

app = Celery('tasks')
app.config_from_object('celeryconfig')
app.conf.imports = ('cworker.tasks')
app.autodiscover_tasks()
```

![](https://i.imgur.com/snwEoS6.png)

task를 각 셀러리가 나눠서 할당해서 수행

![](https://i.imgur.com/KBCPxTm.png)

![](https://i.imgur.com/gvCgtVs.png)

## Celery Task Prioritization (우선순위)

- 우선순위 작업의 실행 순서와 리소스 할당을 해보자
- 위 방법을 활용하면 중요한 작업을 처리하거나 상대적 중요성 혹은 긴급성에 대해 정의해서 컨트롤이 가능하다.
- 또한 우선순위 값 할당 뿐만 아니라 대기열을 구성해서 처리할 수도 있다.

### Why Prioritize Tasks?:

- Ensure critical tasks are executed first
- Optimize resource utilization
- Meet SLAs and deadlines
- Handle high-priority or time-sensitive requests efficiently


### Setting Task Priority:

- 0 and 9 (0 being the lowest, 9 being the highest)
- Default specified in the task decorator or configuration


### Configuring Worker Queues:

- Define multiple queues representing different priorities
- Associate each queue with a specific priority level
- Celery worker consumes tasks from queues based on their priority

## Configuring Task Prioritization (Redis)

![](https://i.imgur.com/BNaQ9wP.png)

셀러리는 Redis를 메시지 브로커로 사용할 때 기본적으로 작업 우선순위를 지원하지 않는다.     
왜냐하면 Redis 자체에 우선순위에 대한 기본 지원 기능이 없다.       

따라서 우선순위가 따로 필요할 때 브로커를 RabbitMQ 와 같은 우선순위 지원이 가능한 브로커를 사용할 수 있다.     

이를 통해 고사양의 서버와 저사양의 서버에 각각 용도에 맞게 작업을 분배하는 게 가능해진다.     

하지만 셀러리 내에서도 RabbitMQ 와 redis에 대한 우선순위를 설정하는 옵션이 있다.

[셀러리 루팅](https://docs.celeryq.dev/en/stable/userguide/routing.html)

![](https://i.imgur.com/iKtZwBm.png)

*djcelery/djcelery/celery.py*
```python
import os
from celery import Celery
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'djcelery.settings')
app = Celery("djcelery")
app.config_from_object("django.conf:settings", namespace="CELERY")
# app.conf.task_routes = {
#     "cworker.tasks.task1": {'queue': 'queue1'}, "cworker.tasks.task2": {'queue': 'queue2'}
#     }

app.conf.broker_transport_options = {
    'priority_steps': list(range(10)),
    'sep': ':',
    'queue_order_startegy': 'priority',
}

  

app.autodiscover_tasks()
```

![](https://i.imgur.com/8M12QG6.png)

*djcelery/djcelery/tasks.py*
```python
import time
from celery import shared_task

@shared_task
def tp1(queue='celery'):
    time.sleep(3)
    return

@shared_task
def tp2(queue='celery:1'):
    time.sleep(3)
    return

@shared_task
def tp3(queue='celery:2'):
    time.sleep(3)
    return

@shared_task
def tp4(queue='celery:3'):
    time.sleep(3)
    return
```


![](https://i.imgur.com/zyE2nU1.png)

## The Primitives - Task Grouping

![](https://i.imgur.com/RolbGf3.png)

![](https://i.imgur.com/jnfzcf8.png)

`task_group`을 생성하는데 사용된 `s()`는 Celery 작업을 실행하기 위한 특별한 메서드다. `s`는 Celery에서 "signature"를 나타내는 것으로, 작업을 실행하기 위한 작업 서명을 생성한다.     

`tp1.s()`, `tp2.s()`, `tp3.s()`, `tp4.s()`는 각각 `tp1`, `tp2`, `tp3`, `tp4`라는 Celery 작업을 나타내는 서명(signature)을 생성한다. 이 서명은 작업의 이름, 매개변수 등과 같은 정보를 포함하며, 나중에 `apply_async()`를 호출하여 비동기로 실행할 때 사용된다.      

따라서 `task_group`에는 네 개의 서명이 포함되어 있으며, 이 서명들은 순차적으로 실행할 작업을 정의한다. `apply_async()`를 호출하여 이 그룹을 비동기로 실행하면, 그룹 내의 모든 작업이 병렬로 실행되며 작업 결과를 반환하게 된다.     

그룹을 사용하면 여러 작업을 동시에 실행하고 결과를 처리하기에 유용하다. 특정 작업은 예를 들어 다른 작업이 완료된 후에만 실행되도록 보장할 수있다. 병렬 처리 및 작업 관리에 사용되며 결과 집계, 진행 상황 추적, 오류 처리 등에 사용된다.     

작업을 그룹화 하면 여러개의 작은 작업을 결합해서 복잡한 작업 흐름이나 파이프 라인을 구성하는 방법을 만들 수 있다.      

정리하자면 복잡한 작업을 더 작고 관리 가능한 단위로 나누고 정리하는데 유용하게 사용가능하다.      

## The Primitives - Task Chaining

이름처럼 연속적으로 한 개가 끝나고 다음으로 넘어가는걸 생각하면 된다.     
이전과의 차이점이라면 앞에서는 병렬적으로 처리가 되는 로직이라면 테스크 체이닝은 순차적으로 진행된다는 점이다.     


![](https://i.imgur.com/iFhiCoZ.png)

 ![](https://i.imgur.com/o11TeyN.png)

## Task Rate limits

- 실행속도에 제한을 적용하거나 서버 리소스를 제한해서 과부하를 방지할 수 있다.
- 제한을 주는 방법은 task.py 에 코드를 넣거나 혹은 celey.py에 넣으면 된다.

*celery.py*
```python
app.conf.task_default_rate_limit = '1/m'
```

*tasks.py*
```python
@shared_task(task_rate_limit='10/m')
def tp1(queue='celery'):
    time.sleep(3)
    return
```


## Configuring Task Prioritization (RabbitMQ)

```
pip install pika
```

[RabbitMQ](https://docs.celeryq.dev/en/stable/userguide/routing.html#rabbitmq-message-priorities)

```python
from kombu import Exchange, Queue

app.conf.task_queues = [
    Queue('tasks', Exchange('tasks'), routing_key='tasks',
          queue_arguments={'x-max-priority': 10}),
]
app.conf.task_acks_late = True
app.conf.task_default_priority = 5
app.conf.worker_prefetch_multiplier = 1
app.conf.worker_concurrency = 1
```

### app.conf.task_acks_late 

`app.conf.task_acks_late = True` 여기서 True를 설정하면 작업을 수행한 후 작업 처리를 완료한 것으로 표시하며 성공이나 실패여부와 상관 없이 바로 ACK로 보내지 않는다.     

이렇게 late ACK를 사용하면 다음과 같은 장점이 있다.

1. 성능 향상: 늦은 ACK를 사용하면 작업 처리를 완료한 후에만 ACK를 보내므로 작업 큐에 더 적은 네트워크 부하가 발생할 수 있다.
2. 안전성 향상: 작업이 실제로 완료되었음을 확인하기 전에 작업 큐에서 작업을 제거하지 않으므로 작업 처리에 문제가 있는 경우 다시 시도하거나 실패한 작업을 복구하는 데 도움이 될 수 있다.
3. 복잡한 작업 처리 시나리오: 일부 작업이 다른 작업에 의존하는 경우, 늦은 ACK를 사용하여 모든 작업이 성공적으로 완료되었음을 확인하고 다음 단계의 작업을 시작할 수 있다.

그러나 늦은 ACK를 사용하면 작업 처리가 끝나지 않은 작업이 큐에 남을 수 있으므로 주의가 필요하다. 일부 경우에는 늦은 ACK가 유용하지만, 다른 경우에는 기본 설정(즉, 빠른 ACK)이 더 적합할 수 있다. 작업 처리 요구 사항과 상황에 따라 적절한 설정을 선택해야 한다.       

정리하자면 작업이 실행된 후에 승인이 전송된다.      
이렇게 설정하는 이유는 작업을 비동기적으로 처리한 후 나중에 확인할 수 있기 때문이다. (실패했는지 성공했는지 확인)       
셀러리 워커가 작업을 완료하기 전에 충돌이 발생하는 경우를 확인할 수 있다.     

하지만 오류를 처리하는데 오버헤드가 생길 수 있다.      


### app.conf.task_default_priority

`app.conf.task_default_priority = 5` 는 기본적으로 모든 작업 우선순위가 디폴트로 5 값을 갖는다는 말이다.     


`app.conf.worker_prefetch_multiplier = 1`       
`app.conf.worker_prefetch_multiplier`는 Celery 작업자(worker)가 메시지 큐로부터 작업을 미리 가져올 때 사용되는 설정 중 하나다. 이 설정은 Celery 작업자가 얼마나 많은 작업을 동시에 메시지 큐로부터 가져올지 결정하는 데 영향을 미친다.      

기본값으로 `worker_prefetch_multiplier`는 4다. 이는 작업자가 한 번에 메시지 큐로부터 4개의 작업을 가져오도록 설정되어 있음을 의미한다. 이 값은 작업자의 성능 및 메모리 사용에 영향을 미친다.       

`worker_prefetch_multiplier`를 1로 설정하면 작업자는 한 번에 하나의 작업만 가져온다. 이는 작업자가 메모리를 적게 사용하고 작업을 순차적으로 처리하는 데 도움이 된다. 그러나 성능면에서는 일반적으로 더 낮을 수 있다.      

`worker_prefetch_multiplier`를 높게 설정하면 작업자가 한 번에 더 많은 작업을 가져올 수 있으므로 병렬 처리가 향상되지만, 메모리 사용량이 더 높아질 수 있다. 높은 값을 설정하면 작업자가 메시지 큐로부터 미리 가져올 작업 수가 증가하며, 이로 인해 작업자가 보다 더 빠르게 작업을 소진할 수 있다.

`worker_prefetch_multiplier`의 적절한 값을 설정하는 것은 시스템의 성능 및 리소스 사용과 관련이 있으며, 작업자의 효율적인 동작을 위해 고려해야 한다. 따라서 이 값을 조정할 때는 시스템 요구 사항과 성능 목표를 고려해야 한다.


### app.conf.worker_concurrency

`app.conf.worker_concurrency = 1`

1. `app.conf.worker_concurrency`:
    - 이 옵션은 Celery 작업자(worker)의 동시 작업 처리 수를 지정하는데 사용된다.
    - 예를 들어, `app.conf.worker_concurrency = 4`로 설정하면 각 작업자(worker)는 동시에 최대 4개의 작업을 처리할 수 있다.
    - 이 옵션은 작업자가 동시에 처리할 수 있는 작업의 수를 제한하고 병렬 처리를 제어하는 데 사용된다.
      
2. `app.conf.worker_prefetch_multiplier`:
    - 앞서 설명한 대로, 이 옵션은 Celery 작업자(worker)가 메시지 큐로부터 작업을 미리 가져오는 방식을 제어하는데 사용된다.
    - 이 옵션은 작업자(worker)가 한 번에 메시지 큐로부터 몇 개의 작업을 미리 가져올지를 설정한다.
    - 예를 들어, `app.conf.worker_prefetch_multiplier = 4`로 설정하면 작업자는 한 번에 4개의 작업을 메시지 큐로부터 가져온다.

주요 차이점은 `worker_concurrency`는 작업자(worker)의 동시 작업 처리 수를 제어하는 데 사용되며, `worker_prefetch_multiplier`는 작업자가 메시지 큐로부터 미리 가져오는 작업 수를 제어하는 데 사용된다는 점이다. 두 옵션은 작업자의 작업 스케줄링 및 병렬 처리 방식을 다르게 조정한다.

*settings.py*
```python
# CELERY_BROKER_URL = os.environ.get("CELERY_BROKER", "redis://redis:6379/0")

CELERY_BROKER_URL = os.environ.get("CELERY_BROKER", "amqp://guest:guest@rabbitmq:5672/")
```

포트번호를 재설정해주고 도커를 다시 빌드해준다.

![](https://i.imgur.com/Rayftme.png)

![](https://i.imgur.com/CGErTsM.png)

![](https://i.imgur.com/fO1jVcX.png)

```
t2.apply_async(priority=5)
t1.apply_async(priority=6)
t3.apply_async(priority=9)
t2.apply_async(priority=5)
t1.apply_async(priority=6)
t3.apply_async(priority=9)
```

이렇게 우선순위를 다르게 바꿔서 일을 시키면
t3,t3,t1,t1,t2,t2 이런식으로 진행이된다.      

![](https://i.imgur.com/dCfwvxK.png)

## Passing arguments and returning results from Celery tasks

### Task arguments:

- Positional Arguments
- Keyword Arguments

*celery.py*
```python
@app.task(queue='tasks')
def t1(a, b, message=None):
    result = a + b

    if message:
        result = f"{message}: {result}"

    return result
```

![](https://i.imgur.com/VeLL3wX.png)

![](https://i.imgur.com/bl9e1fD.png)

참고로 셀러리에서 돌아가는 부분이라 장고 터미널이 아닌 셀러리 컨테이너의 터미널에서 확인을 해야한다.     

해당 터미널에서 보기 위해서는 객체로 만들어서 불러오면 가능하다.

![](https://i.imgur.com/jEENUfI.png)

### AsyncResult:

- isCompleted(): Checks whether the task associated with the AsyncResult object has completed
- isSuccessful(): Checks whether the task completed successfully
- get(): Blocks the current thread until the task completes
- getResult(): Returns if task has completed successfully
- getException(): Returns the exception or error

> 일반적으로 위와 같은 매서드가 있지만 셀러리 및 파이썬에서는 약간 다르다. 공식문서를 참고하면 좋다.

[셀러리 공식문서](https://docs.celeryq.dev/en/latest/)


*celery.py*
```python
@app.task(queue='tasks')
def t1(a, b, message=None):
    result = a + b

    if message:
        result = f"{message}: {result}"

    return result
    
app.autodiscover_tasks()

def test():
    # Call the task asynchronously
    result = t1.apply_async(args=[5,10], kwargs={"message":"The sum is"})

    # Check if the task has completed
    if result.ready():
        print("Task has completed")
    else:
        print("Task is still running")

    # Check if the task completed successfully
    if result.successful():
        print("Task completed successfully")
    else:
        print("Task encountered an error")

    # Get the result of the task
    try:
        task_result = result.get()
        print("Task result:", task_result)
    except Exception as e:
        print("An exception occurred:", str(e))

    # Get the exception (if any) that occurred during task execution
    exception = result.get(propagate=False)
    if exception:
        print("An exception occurred during task execution:", str(exception))
```

![](https://i.imgur.com/LdFf6MF.png)

위와 같이 상태체크가 가능하다.      

## Excuting tasks synchronously and asynchronously

```python

@app.task(queue='tasks')
def t1(a, b, message=None):
    
    result = a + b
    if message:
        result = f"{message}: {result}"
    return result

# Synchronous task execution
def execute_sync():
    result = t1.apply_async(args=[5,10], kwargs={"message":"The sum is"})
    task_result = result.get()
    print("Task is running synchronously")
    print(task_result)

# Asynchronous task execution
def execute_async():
    result = t1.apply_async(args=[5,10], kwargs={"message":"The sum is"})
    print("Task is running asynchronously")
    print("Task ID:", result.task_id)
```

셀러리에서는 동기, 비동기 모두 지원 가능하다.      
첫 번째 방법은 동기식인데 작업이 완료될 때까지 기다린다.
`result.get()` 부분에서 앞에 작업이 완료될 때까지 기다려야 하기 때문이다.
똑같은 코드지만 두 번째 매서드는 비동기식으로 작업이 완료되는걸 기다리지 않고 바로 task_id를 반환한다.     

## Monitoring Celery Workers and Tasks with Flower

### Purpose of Celery Flower:

- Monitoring
- Management
- Visualization

```
  flower:

    image: mher/flower

    ports:

      - 5555:5555

    environment:

      - CELERY_BROKER_URL=amqp://guest:guest@rabbitmq:5672/
```


![](https://i.imgur.com/WUFmSkM.png)

- 현재는 셀러리 워커가 한 명이다.

![](https://i.imgur.com/BP2K5qq.png)

Celery Flower(플라워)는 Celery 작업 관리자의 시각화 및 모니터링 도구로, Celery 작업의 상태, 성능, 실행 내역 등을 실시간으로 확인할 수 있는 웹 인터페이스를 제공한다. Flower에서 "soft"와 "hard"는 두 가지 중요한 개념으로, Celery 작업의 실패 처리 및 재시도 관련 정보를 제공한다.

1. **Soft Time Limit (soft)**:
    - "soft"는 작업의 소프트 타임 리밋(제한)을 나타낸다. 작업이 일정 시간 동안 실행되고 있는 경우에 대한 경고 또는 제한을 설정할 수 있다.
    - 예를 들어, 만약 소프트 타임 리밋이 설정되어 있다면, 작업이 설정된 시간 제한을 초과할 경우 Flower에서 경고 메시지를 표시할 수 있다. 이는 긴 실행 시간을 가진 작업을 감시하거나 예기치 않은 오류 상황을 검출하는 데 사용할 수 있다.
    - 소프트 타임 리밋은 Celery 작업에 대해 `time_limit` 매개변수로 설정될 수 있다.
      
2. **Hard Time Limit (hard)**:
    - "hard"는 작업의 하드 타임 리밋(제한)을 나타낸다. 작업 특정 시간을 초과하면 Celery가 작업을 종료하도록 하는 제한을 설정한다.
    - 예를 들어, 하드 타임 리밋이 설정되어 있다면, 작업이 하드 타임 리밋을 초과하면 Celery가 해당 작업을 강제로 종료하고 실패로 표시할 수 있다. 무한 루프 또는 장애 상황을 방지하는 데 유용하게 쓰일 수 있다.
    - 하드 타임 리밋은 Celery 작업에 대해 `task_time_limit` 매개변수로 설정될 수 있다.

이러한 "soft"와 "hard" 타임 리밋은 Celery 작업이 얼마나 오랫동안 실행될 수 있는지 제한하고 관리하는 데 사용된다. Flower를 사용하여 이러한 설정과 제한에 관한 정보를 모니터링하고 경고를 받을 수 있다.


## Key Topics:

- Performance Optimization]
- Troubleshooting and issue Resolution
- Resource Management
- Scaling and Load Balancing

