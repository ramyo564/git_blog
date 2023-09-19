---
layout: single
title: "[Django] admin 보안 어디까지 강화해봤?"
categories: Django_Basic
tags:
  - Python
  - admin
  - 보안
  - Django
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# 장고의 admin 보안기능 강화

장고는 관리자 페이지가 기본적으로 {도메인주소}/admin 이 디폴트 값이다.
인스타그램도 장고로 만들어졌다고해서 단순히 https://www.instagram.com/admin 에 접속해봤는데 안되었다 
그래서 인스타그램에서는 이걸 어떻게 했을지가 궁금했는데
단순히 어드민 주소를 바꾸면 되는거였다.

```python
urlpatterns = [
	path('이 주소를 바꾸면 되는거 였음/', admin.site.urls),
]
```

근데 악의를 품고 admin에 접속하려는 사람이 있을 수도 있다.
크롤링을 통해 계속해서 접속을 시도하거나 아이디와 비번을 알아내려는 공격을 할 수 도 있다.

물론 캡차를 통해 방지를 할 수 있지만 admin 주소가 노출되지 않는 것도 중요하다.
이에 대해서 재밌는 라이브러리를 알게 되었고 해당 라이브러리를 설치하면서 겪은 오류를 공유하면 좋을 것 같다고 생각했다.

[django-admin-honeypot](https://pypi.org/project/django-admin-honeypot/)
`pip install django-admin-honeypot`

장고 어드민 허니포트라는 라이브러리는 admin 사이트를 복제하고 해당 사이트에 접속을 시도하려는 사람이 있으면 해당 아이피를 블락시켜버린다.

```python
INSTALLED_APPS = [

    "admin_honeypot",
]
```

라이브러리를 설치하고 INSTALLED_APPS 에 등록해준다.

```python
urlpatterns = [

    path('admin/',include('admin_honeypot.urls', namespace='admin_honeypot')),
    path('진짜 어드민 주소/', admin.site.urls),
    
    ]
```

그후 `python manage.py migrate` 를 하면 끝

이렇게 하면 admin url로 접속하려는 ip 주소를 기록하고 일정 시도가 넘어가면 해당 아이피를 블락시킨다.

근데 문제가 하나 있었는데 나는 마이그레이션이 안되었었다.

## 첫 번째 에러

![](https://i.imgur.com/M8K9eTJ.png)

`ImportError: cannot import name 'ugettext_lazy' from 'django.utils.translation' (C:\Users\user\Documents\GitHub\Upgrade_Django4\venv\Lib\site-packages\django\utils\translation\__init__.py)`

[참고](https://stackoverflow.com/questions/70656495/importerror-cannot-import-name-ugettext-lazy)

대충 오류 문제를 찾아보니 장고 4.0 에서는 안된다는 점..
`ugettext_lazy` 는 4.0에서 더 이상 사용하지 않아서 `gettext_lazy` 으로 변경해줘야 한다.

그래서 해당내용을 찾아서 변경했다.

```python
from django.contrib import admin
from django.utils.translation import gettext_lazy  as _
-->ugettext_lazy 부분을 위와 같이 gettext_lazy 로 변경해주면 된다.

from admin_honeypot.models import LoginAttempt
class LoginAttemptAdmin(admin.ModelAdmin):
	.....
```

그리고 다시 `python manage.py migrate` 를 실행했지만 역시나 안되었다.

## 두 번째 에러 

![](https://i.imgur.com/MkJhbTo.png)


[참고](https://stackoverflow.com/questions/70466886/typeerror-init-got-an-unexpected-keyword-argument-providing-args)

근데 어차피 원인 자체가 장고 버전에 대한 문제이므로 문제가 되는 부분만 수정하면 쓸 수 있다고 생각했다.

```python
from django.dispatch import Signal

# 해당 라이브러리는 3.x 기준으로 만들어져 있어서 문법만 바꾸면 된다.
honeypot = Signal(providing_args=["request"])

# 위에 를 지우고 아래 형식으로 변경해주면 된다.
honeypot = Signal('request')
```


## 세 번째 에러

`ImportError: cannot import name 'ugettext' from 'django.utils.translation' (C:\Users\user\Documents\GitHub\Upgrade_Django4\venv\Lib\site-packages\django\utils\translation\__init__.py)
(venv) `

![](https://i.imgur.com/Bax2327.png)

```python
import django
from admin_honeypot.forms import HoneypotLoginForm
from admin_honeypot.models import LoginAttempt
from admin_honeypot.signals import honeypot
from django.contrib.auth import REDIRECT_FIELD_NAME
from django.contrib.auth.views import redirect_to_login
from django.shortcuts import redirect
from django.urls import reverse
from django.utils.translation import ugettext as _
ugettext 부분을 gettext 로 변경
from django.views import generic

  
class AdminHoneypot(generic.FormView):
    template_name = 'admin_honeypot/login.html'
    form_class = HoneypotLoginForm
```

이전에도 ugettext_lazy 부분이 문제였으니 ugettext 부분을 변경
둘 이 뭐가 다른지 궁금해서 찾아보니 아래와 같은 글이 있으니 궁금하면 참고
요약하자면 Translation 모듈을 사용할 때 사용하는데
`ugettext` 는 views.py 처럼 매번 새로 실행되는 경우에 사용되고
`ugettext_lazy` 는 Django start-up 때 한 번만 실행되는 코드라고 한다.

[ugettext vs ugettext_lazy](https://iam.namjun.kim/django/2019/01/29/django-for-international-service/)


## 네 번째 에러

![](https://i.imgur.com/MGVkGgZ.png)
`ImportError: cannot import name 'url' from 'django.conf.urls' (C:\Users\user\Documents\GitHub\Upgrade_Django4\venv\Lib\site-packages\django\conf\urls\__init__.py)`

마찬가지로 버전문제로 url을 아래와 같이 변경해주면 된다.

```python

from django.conf.urls
위에 코드를 아래와 같이 변경

-> from django.urls import re_path as url
from myapp.views import home

urlpatterns = [
    url(r'^$', home, name="home"),
    url(r'^myapp/', include('myapp.urls'),
]
		
```

[참고](https://stackoverflow.com/questions/70319606/importerror-cannot-import-name-url-from-django-conf-urls-after-upgrading-to)

그럼 이제 admin 주소로 접근해서 로그인을 시도하면 admin에 해당 경로로 시도한 사람의 ip 주소등이 기록된다.