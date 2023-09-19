---
layout: single
title: " [Django] Shopping Mall (1)"
categories: Django_ShoppingMall
tags:
  - Python
  - 초기설정
  - templates
  - Django
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# 초기설정 편하게 하기

`[Django] 장고에서 템플릿 쉽게 관리하기`

> 이번 토이 프로젝트의 목표는 다음과 같다.

1. 이전에 개발하면서 불편했던 부분 개선 
2. 구현하면서 대충 넘어갔었던 기본 원리에 좀 더 집중

## 장고에서 templates 폴더 관리하기
{% raw %}

장고에서 templates 관리의 디폴트는 본인이 만든 app 폴더에 templates -> app 이름의 폴더다.

![](https://i.imgur.com/LOg7EKR.png)


### 문제점

하지만 토이 프로젝트를 할 당시 기능 추가를 할 수록, 앱의 종류가 많아질수록 너무 헷갈렸다.
이 부분을 해결하려고 다른 사람들은 어떻게 하나 구글링을 해보니 대부분 templates 폴더를 따로 만들어서 관리하는 사람들이 많았다.

### settings.py
```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': ['templates'],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```
- 생각보다 방법은 간단하다.  기존의 디폴트 설정을 바꿔주려면 되는데 TEMPLATES 부분에서  `DIRS : []` 이부분의 경로를 설정 해주면 끝이다.
- ![](https://i.imgur.com/G7ibWz1.png)

- 예를 들어 위처럼 templates라는 폴더로 경로를 설정해 두었다면  `return render(request, 'ddd/ddd.html')` 이렇게 호출하면된다.
- 진짜 앱 5개만 넘어가도 헷갈려서 머리아프니 templates도 한 곳에 모아서 관리하는게 정신건강에 이롭다.


## static 폴더 설정

### Django collectstatic 

- 웹 어플리케이션은 웹 서버가 받은 요청을 넘겨주면 로직에 맞추어 데이터를 처리한 후 웹 서버에게 돌려주어 사용자에게 응답하게 하는 역할을 한다.
- 하지만 사실 사용자에게 응답하는 것은 웹서버 자체에서 할 수 있기 때문에 굳이 웹 어플리케이션에서 처리할 필요가 없다.
- 오히려 웹 어플리케이션을 거치게 되면 wsgi(웹서버와 웹 애플리케이션 간의 통신을 위한 이터페이스)를 거쳐야하므로 비효율적인 로직이 하나 더 늘어난다.
- 이러한 이유로 Django에서는 static 파일을 직접 처리하는 기능을 담고있지 않다.

>- 따라서 프로젝트를 실제로 배포할 때 collectstatic을 통해 프로젝트에 흩어져 있는 파일들을 한 곳에 모은 뒤에 서버로 복사해줘야 한다.

### settings.py 설정
```python
BASE_DIR = Path(__file__).resolve().parent.parent

STATIC_URL = 'static/'
STATIC_ROOT = BASE_DIR /'static'
STATICFILES_DIRS = [
    'greatkart/static',
]
```
우선 static 파일 설정부터 해주는데 기존과 동일하다.
상황에 따라 `STATICFILES_DIRS = [BASE_DIR / 'static']` 이렇게 리스트 형태로 담아줘도 상관 없다.
참고 : https://blog.hannal.com/2015/04/start_with_django_webframework_06/
- 최종 배포 전에 `python manage.py collectstatic ` 를 해서 파일을 한 곳에 모아두기만 하면 된다.
- 그럼 알아서 해당 경로에 static 파일들이 모아진 폴더가 만들어지고 `{% load static %}` 만 잘 호출해주면 된다.
- ![](https://i.imgur.com/ouGUZhe.png)





{% endraw %}
