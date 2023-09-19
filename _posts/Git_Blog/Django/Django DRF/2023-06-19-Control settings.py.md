---
layout: single
title: " [Django DRF] Control settings.py"
categories: Django_DRF_Practice
tags:
  - Python
  - Project
  - eCommerce
  - RESTful
  - API
  - Control
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
## 어플리케이션을 구축할 때 꼭 필요한 것

어플리케이션을 베포할 때 필요한 설정구성이 있다.
실제 서버에 배포하게 되면 운영체제등 다른 환경이 될 수 있다.
따라서 모듈식 접근 방식을 활용해서 서로 다른 설정 파일을 만들 수있다.

우선 동작원리부터 알아보자
Django에는 settings.py 라는 파일이 있는데 이 파일은 어플리케이션에 영향을 미치는 설정을 포함한 주요한 파일이다.
프로젝트를 시작할 때는 manage 파일을 사용하며 이를 통해 어플리케이션을 실행하고 서버에 진입한다.

필요에 따라 모듈화를 진행하면 작업하기 간편하다.

![](https://i.imgur.com/Me3FieK.png)

settings.py를 base.py로 변경한 후 settings 파일에 따로 옮겼다.
manage.py 에서 경로를 바꿔주고 변경된 base.py 에서 DEBUG 값이 true 일 때와 False 일 때 각각 다른 화면페이지가 출력되도록 모듈화 해준다.

```python
local.py

from.base import *
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
```

```python
production.py

from.base import *
ALLOWED_HOSTS = ['*']
```

```python
manage.py

import os
import sys
from drfecommerce.settings import base

def main():
    """Run administrative tasks."""
    if base.DEBUG:
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'drfecommerce.settings.local')
    else:
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'drfecommerce.settings.production')

    try:
        from django.core.management import execute_from_command_line
    except ImportError as exc:
        raise ImportError(
            "Couldn't import Django. Are you sure it's installed and "
            "available on your PYTHONPATH environment variable? Did you "
            "forget to activate a virtual environment?"
        ) from exc
    execute_from_command_line(sys.argv)
```

## Secretkey

장고의 시크릿 키는  자동으로 생성되는데
이 시크릿 키를 활용하여 세션 데이터, CSRF(사이트간 요청 위조)토큰, 비밀번호 해시 등에 사용된다.

보안에 있어서 매우 중요한 부분이지만 처음에 깃에 올리거나 관리할 때 이부분을 까먹고 그냥 올리는 경우가 많다.

시크릿키가 유출되거나 깃에 업로드를 하였다면 시크릿키를 다시 생성해서 관리해야한다.

[장고 시크릿키](https://github.com/django/django/blob/main/django/core/management/utils.py)

![](https://i.imgur.com/LNTBjdg.png)

```python
python manage.py shell
from django.core.management.utils import get_random_secret_key 
```

![](https://i.imgur.com/Q7qBRd3.png)

파이썬 쉘에서 다시 쉽게 생성할 수 있다.
시크릿키나 API키등은 `.env` 파일을 만들어 따로 관리해야한다.

## Configuring Environment Variables

[git ignore 사이트](https://www.toptal.com/developers/gitignore)
![](https://i.imgur.com/R6ohiZT.png)
`.gitignore` 파일을 만들어준후 위 사이트에서 장고를 검색하면 장고에 맞게 알아서 ignore 생성해준다.

해당 텍스트를 .gitignore에 붙여넣어주면 된다 (.env 도 알아서 있음) 

그후 .env 파일을 만든 후
(env를 불러오는 방법은 여러가지가 있음)

python-decouple 라이브러리를 설치해서 관리한다.
`django-environ`  , `python-dotenv` , `python-decouple` 등등 라이브러리는 많다.
`python-decouple` 는 Django 외에 일반적인 Python 프로텍트에서도 사용 가능한데 그냥 마음에 드는거 설치하면 된다.

```python
pip install python-decouple
```

라이브러리를 설치한후

`.env` 파일에는 키값과 벨류값만을 넣고
셋팅에서는 아래와 같이 사용하면 된다.

```python
from decouple import config
SECRET_KEY = config('SECRET_KEY')
```

또한 위와 같이 모듈화를 한 것 처럼 상황에 따라 SECRET KEY를 바꿔 사용할 수 있다.

