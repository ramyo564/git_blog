---
layout: single
title: " [Django Celery] Celery (4)"
categories: Django_Celery
tags:
  - Python
  - Celery
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# Commin types of exceptions and Errors in Celery tasks

## Network Erros:

- Connection timeouts
- DNS resolution failures
- Network connectivity issues

## Database Connection Issues:

- Database server unavailability
- Authentication errors
- Connection pool exhaustion

## Types of exceptions and Errors (contd.):

- External Service Failures
- Custom Application-Specific Errors

## Dynamic Task Discovery in Celery: Auto-discovering Tasks in a Directory

![](https://i.imgur.com/SsjndZj.png)

*djcelery/celery_config.py*
```python
import os
from celery import Celery
from kombu import Exchange, Queue

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "djcelery.settings")
app = Celery("djcelery")
app.config_from_object("django.conf:settings", namespace="CELERY")

app.conf.task_queues = [
    Queue(
        "tasks",
        Exchange("tasks"),
        routing_key="tasks",
        queue_arguments={"x-max-priority": 10},
    ),
]


app.conf.task_acks_late = True
app.conf.task_default_priority = 5
app.conf.worker_prefetch_multiplier = 1
app.conf.worker_concurrency = 1

base_dir = os.getcwd()
task_folder = os.path.join(base_dir, "djcelery", "celery_tasks")

if os.path.exists(task_folder) and os.path.isdir(task_folder):
    task_modules = []
    for filename in os.listdir(task_folder):
        if filename.startswith("ex") and filename.endswith(".py"):
            module_name = f"djcelery.celery_tasks.{filename[:-3]}"
            module = __import__(module_name, fromlist=["*"])
            for name in dir(module):
                obj = getattr(module, name)

                if callable(obj):
                    task_modules.append(f"{module_name}.{name}")

    app.autodiscover_tasks(task_modules)
```

- `os.getcwd()` 는 현재 디렉토리를 반환한다. 여기서 task_folder의 경로를 잡아준다.
- 그 후 샐러리 작업들 파일을 ex로 시작하는 걸로 걸러준 뒤 `__import__`를 사용해 모듈을 불러온다. `fromlist` 는 import 함수에서 사용되는 옵션중 하나로 모듈에서 가져올 식별자 (변수,함수,클래스등)의 리스트를 지정하는데 사용한다. 예를 들어 모듈안에 변수 var1, 함수 func1, 클래스 MyClass등이 정의 되어 있다면 `"*"` 는 모든 식별자를 갖고 오므로 아래와 같이 상죵이 가능하다.      
	- import my_module -> print(my_module.var1, my_module.func1())
	- obj = my_module.MyClass()

1. `dir(module)` 함수:
    - `dir()` 함수는 모듈, 클래스 또는 객체에 정의된 모든 이름(식별자)을 나열하는 데 사용한다.
    - 주어진 모듈(`module`) 내에 정의된 모든 이름(변수, 함수, 클래스 등)을 나열하여 반환한다.
2. `getattr(module, name)` 함수:
    - `getattr()` 함수는 객체에서 특정 이름(식별자)에 해당하는 속성을 가져오는 데 사용된다.
    - `module`은 객체(이 경우 모듈)를 나타내며, `name`은 속성의 이름(모듈 내에서 정의된 함수 또는 변수의 이름)이다.
    - `getattr(module, name)`은 `module`에서 `name`에 해당하는 속성을 반환한다.
3. `callable(obj)` 함수:
    - `callable()` 함수는 주어진 객체(`obj`)가 호출 가능한지 여부를 확인하는 데 사용된다.
    - 호출 가능한 객체는 함수 또는 메서드다. `callable()`은 함수, 메서드 또는 다른 호출 가능한 객체가 주어진 경우 `True`를 반환하고, 그렇지 않으면 `False`를 반환한다.

주어진 코드 블록은 다음 작업을 수행한다:

- `dir(module)`을 사용하여 모듈 내에 정의된 모든 이름을 나열 -> 이러한 이름은 모듈 내의 변수, 함수, 클래스 등을 포함한다.
- 모듈 내에 있는 각 이름에 대해 `getattr(module, name)`을 사용하여 해당 이름에 해당하는 객체(변수 또는 함수)를 가져온다.
- `callable(obj)`를 사용하여 가져온 객체가 호출 가능한 함수인지 확인한다.
- 만약 객체가 호출 가능한 함수인 경우, 해당 함수의 이름을 모듈 이름과 함께 `task_modules` 리스트에 추가한다. 이렇게 하면 Celery 작업 모듈의 호출 가능한 함수 목록이 `task_modules` 리스트에 저장된다.

*celery_tasks/ex-name.py*
```python
from djcelery.celery_config import app

@app.task(queue="tasks")
def my_task1():
    pass

@app.task(queue="tasks")
def my_task2():
    pass
```

![](https://i.imgur.com/6LosyTU.png)

이렇게 하면 동적으로 특정 작업 모듈의 리스트만 실행을 할 수 있다.

## Error Handling: Try Except Blocks


