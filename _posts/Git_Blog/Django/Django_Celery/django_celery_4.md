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

- Coverage:
	- Try Except Block
	- Logging / Customizing Output
	- Create and Inspect a Failed Task in Flower

[셀러리 에러 공식문서](https://docs.celeryq.dev/en/main/reference/celery.exceptions.html)

```python
import logging
from djcelery.celery_config import app

"""
from djcelery.celery_tasks.ex_name import my_task
my_task.delay()
"""

logging.basicConfig(
    filename="app.log", level=logging.ERROR, format="%(actime)s %(levelname)s %(message)s"
)

  
@app.task(queue="tasks")
def my_task():
    try:
        raise ConnectionError("Connection Error Occured...")
    except ConnectionError:
        logging.error("Connection error occurred....")
        raise ConnectionError()
```

![](https://i.imgur.com/zkYyIcc.png)

- 일부러 에러를 발생시키고 플라워에서 어떻게 나오는지 확인

![](https://i.imgur.com/tmT6Fu1.png)


## Handling Errors in Celery Tasks with Custom Task Classes

- Key topics covered in the tutorial:
	- Creating a custom task class in Celery
	- Overriding the on_failure method to handle errors
	- Differentiating and handling specific types of exceptions
	- Logging error messages using the logging module
	- Performing additional error handling actions

*ex2.py*
```python
import logging
from celery import Task
from djcelery.celery_config import app

"""
from djcelery.celery_tasks.ex2_custom_tasks_class import my_task
my_task.delay()
"""

logging.basicConfig(
    filename="app.log", level=logging.ERROR, format="%(actime)s %(levelname)s %(message)s"
)

  
class CustomTask(Task):
    def on_failure(self, exc, task_id, args, kwargs, einfo):
        if isinstance(exc, ConnectionError):
            logging.error("Connection error occurred....Admin Notified")
        else:
            print("{0!r} failed: {1!r}".format(task_id, exc))
            # Perform additional error handling actions if needed

app.Task = CustomTask

@app.task(queue="tasks")
def my_task():
    try:
        raise ConnectionError("Connection Error Occured...")
    except ConnectionError:
        logging.error("Connection error occurred....")
        raise ConnectionError()
    except ValueError:
        # Handle value error
        logging.error("Value error occurred...")

        # Perform specific error handling actions
        perform_specific_error_handing()

    except Exception:
    
        # Handle generic exceptions
        logging.error("An error occured")

        # Notify administrators or perform fallback action
        notify_admins()

        perform_specific_error_handing()

def perform_specific_error_handing():

    # Logic to handle a specific error scenario
    pass

def notify_admins():

    # Logic to send notifications to administrators
    pass
```

- Celery 라이브러리에서 Task 클래스를 가져온 후 `app.Task = CustomTask` 로 오버라이드 할 수 있다.
- 이런 방식으로 코드를 만들면 객체지향적으로 중복된 코드를 줄일 수 있다.

## Implementing Automatic Retries

```python
import logging
from celery import Task
from djcelery.celery_config import app

"""
from djcelery.celery_tasks.ex_name import my_task
my_task.delay()
"""

logging.basicConfig(
    filename="app.log", level=logging.ERROR, format="%(actime)s %(levelname)s %(message)s"

)



class CustomTask(Task):
    def on_failure(self, exc, task_id, args, kwargs, einfo):
        if isinstance(exc, ConnectionError):
            logging.error("Connection error occurred....Admin Notified")
        else:
            print("{0!r} failed: {1!r}".format(task_id, exc))
            # Perform additional error handling actions if needed

app.Task = CustomTask

  
@app.task(
    queue="tasks",
    autoretry_for=(ConnectionError,),
    default_retry_delay=5,
    retry_kwargs={"max_retries": 5},
)

def my_task():
    raise ConnectionError("Connection Error Occured...")
    return
```

- 이렇게 사용하면 try, except를 사용하지 않고도 예외 처리를 할 수 있다.
- 매개변수 부분을 통해 재시도 횟수 등을 설정할 수 있다.
- 가벼운 네트워크 장애등에 사용하면 좋을 것 같다.

## Towards error handling in groups

[공식문서](https://docs.celeryq.dev/en/stable/userguide/canvas.html#groups)

```python
from celery import group
from djcelery.celery_config import app

"""
from djcelery.celery_tasks.ex4_error_handling_groups import run_tasks
run_tasks()
"""


@app.task(
    queue="tasks",
)
def my_task(number):
    if number == 3:
        raise ValueError("Error Number is Invalid")
    return number * 2

def handle_result(result):

    if result.successful():
        print(f"Task Completed:{result.get()}")

    elif result.failed() and isinstance(result.result, ValueError):
        print(f"Task failed: {result.result}")

    elif result.status == "REVOKED":
        print(f"Task was revoked: {result.id}")

  
  

def run_tasks():

    task_group = group(my_task.s(i) for i in range(5))
    result_group = task_group.apply_async()
    result_group.get(disable_sync_subtasks=False, propagate=False)


    for result in result_group:
        handle_result(result)
```

![](https://i.imgur.com/AHrmqv6.png)

![](https://i.imgur.com/HgaBew3.png)

- task_group 에서 여러잡업을 그룹화 해서 동시에 실행할 수 있다.
- result_group 는 그룹화된 작업을 비 동기적으로 실행하고 이 작업은 백그라운드에서 실행된다.
- disable_sync_subtasks 가 false 고 propagate가 false인 경우 작업이 완료되지 않았더라도 작업의 결과를 확인할 수 있다.
- 이전에 만들어 둔 handle_result를 반복문으로 돌리면서 작업 상황을 확인할 수 있게 만들 수 있다.
- 여기서 s 는 group 또는 chain등 여러 잡업을 그룹화 하거나 연결해서 작업을 시리얼라이즈 하고 연결하는데 사용된다.

## Towards Error Handling in Task Chains

- 개별적인 처리가 아닌 순서대로 처리하기 위해서는 서로 연결시켜서 작업을 하면 된다.


```python
from celery import chain
from djcelery.celery_config import app

"""
from djcelery.celery_tasks.ex5_error_handling_chain import run_task_chain
run_task_chain()
"""

@app.task(queue="tasks")
def add(x, y):
    return x + y

@app.task(queue="tasks")
def multiply(result):
    if result == 0:
        raise ValueError("Error: Division by zero.")
    return result * 2

def run_task_chain():
    task_chain = chain(add.s(2, 3), multiply.s())
    result = task_chain.apply_async()
    result.get()
```

![](https://i.imgur.com/3HbNynh.png)

- chain 방식은 `add.s(2, 3)`는 `add` 작업을 시리얼라이즈하고 인수로 2와 3을 전달한 작업을 생성한다. 따라서 이 부분은 두 숫자를 더한 결과인 5를 반환한다.
- 이후 `chain` 함수를 사용하여 `add.s(2, 3)`과 `multiply.s()`를 연결한다. `chain`은 각 작업의 결과를 다음 작업의 입력으로 전달한다.
- 따라서 `add.s(2, 3)`의 결과인 5가 `multiply.s()`에 전달되고, `multiply.s()` 함수는 결과를 받아 0이 아닌 경우에는 입력값을 2배로 곱하고 예외가 발생하면 예외를 처리한다.
- 이 경우 5가 `multiply.s()`로 전달되고, 0이 아니므로 5 * 2를 계산하여 10을 반환한다.
- 결과적으로 `result`에는 10이 저장되며 `result.get()`을 호출하면 10이 반환된다.

- if result == 0: 부분을 if result == 5: 로 바꾼다면 에러가 발생하고 그 뒤에 multifly() 함수는 실행되지 않는다.

## Towards Dead-letter Queues : Handling Failed Tasks

![](https://i.imgur.com/AESxkXs.png)

- 실패한 작업을 데드 레터(dead letter)로 전달하거나 보내려고 하는 이유는 여러가지 있다.
- 오류처리이므로 대기열을 설정하거나 오류처리 방법을 제공할 수 있다.
- 오류나 실패로 인해 성공적으로 처리할 수 없는 메시지를 처리한다. 따라서 이러한 메시지를 버려서 손실되는 대신 별도의 경로로 라우팅 할 수 있다. 추가 처리 또는 분석을 위해 대기하거나 교환한다.
- 이렇게 설정하면 오류 처리의 이점을 모두 한 곳에서 처리할 수 있다.