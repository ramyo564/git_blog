---
layout: single
title: " [Django] Shopping Mall (2)"
categories: Django_ShoppingMall
tags:
  - Python
  - 모델
  - 커스텀하기
  - Django
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# 모델을 어떻게 커스텀할까?

{% raw %}

## 어드민 페이지 커스텀하기

- 보통 어드민 페이지를 만들고 관리자를 생성해서 로그인 하려고 보면 관리자를 생성할 때 지정한 유저네임으로 로그인한다.
- ![](https://i.imgur.com/gOrR6n4.png)

- 근데 이게 중간중간 테스트를 계속 하다보면 test1, test2 이런식으로 유저를 만드는데 나중에 가면 비슷비슷한 이름들이 은근히 헷갈린다.
- 따로 포스트잇에 적어 놓는 것도 귀찮고 그래서 다른 사람들은 어떻게 하나 찾아보니 이걸 커스텀해서 쓴다.
- 유저네임으로 로그인하는 대신에 이메일로 로그인하면 테스트할 때 좀 더 편하게 할 수 있지 않을까 생각함
	- test@superuser.com
	- test@login.com
	- test@buyer.com
	- test@cart.com 
		- 이렇게 만들어 놓으면 이메일 중복에도 안걸리고 유저이름은 어차피 다 test니 개인적으로 이렇게 하는게 더 편함
- 기본 원리는 기존 모델에서 오버라이딩을 하면 된다.

## accounts 모델 초기 설정

### models.py
```python
from django.db import models
from django.contrib.auth.models import AbstractBaseUser, BaseUserManager
class MyAccountManager(BaseUserManager):

    def create_user(self, first_name, last_name, username, email, password=None):
        if not email:
            raise ValueError('User must have an email address')

        if not username:
            raise ValueError('User must have an username')

        user = self.model(
            email = self.normalize_email(email),
            username = username,
            first_name = first_name,
            last_name = last_name,
        )
        user.set_password(password)
        user.save(using=self._db)
        return user

    def create_superuser(self, first_name, last_name, email, username, password):
        user = self.create_user(
            email = self.normalize_email(email),
            username = username,
            password = password,
            first_name = first_name,
            last_name = last_name,
        )
        user.is_admin = True
        user.is_active = True
        user.is_staff = True
        user.is_superadmin = True
        user.save(using=self._db)
        return user
        
class Account(AbstractBaseUser):
    first_name      = models.CharField(max_length=50)
    last_name       = models.CharField(max_length=50)
    username        = models.CharField(max_length=50, unique=True)
    email           = models.EmailField(max_length=100, unique=True)
    phone_number    = models.CharField(max_length=50)

    # required
    date_joined     = models.DateTimeField(auto_now_add=True)
    last_login      = models.DateTimeField(auto_now_add=True)
    is_admin        = models.BooleanField(default=False)
    is_staff        = models.BooleanField(default=False)
    is_active        = models.BooleanField(default=False)
    is_superadmin        = models.BooleanField(default=False)

    USERNAME_FIELD = 'email'
    REQUIRED_FIELDS = ['username', 'first_name', 'last_name']

    objects = MyAccountManager()
    
    def full_name(self):
        return f'{self.first_name} {self.last_name}'

    def __str__(self):
        return self.email

    def has_perm(self, perm, obj=None):
        return self.is_admin

    def has_module_perms(self, add_label):
        return True
```
>- USERNAME_FIELD = 'email' 로 지정하면 로그인할 때 이메일로 로그인이 된다.
>	- ![](https://i.imgur.com/x1snhGY.png)


>- `normalize_email` 이메일 주소에 대문자가 있다면 다 소문자로 만들어준다.
>- `REQUIRED_FIELDS` 이메일로 로그인을 하니까 해당부분에서 제외한다.
>- `has_perm`(_perm_, _obj=None_) [공식문서](https://docs.djangoproject.com/ko/4.2/topics/auth/customizing/#django.contrib.auth.models.PermissionsMixin.has_perm "이 정의에 대한 퍼머링크")
>	- 특정한 권한을 사용자가 갖는다면,``True`` 를 리턴하고, `perm` 은 "1.2"  안의 포맷이다(permissions 3 참조). :attr:. User.is_active 및 [is_superuser](https://docs.djangoproject.com/ko/4.2/ref/contrib/auth/#django.contrib.auth.models.User.is_superuser "django.contrib.auth.models.User.is_superuser") 가 모두 [``](https://docs.djangoproject.com/ko/4.2/topics/auth/customizing/#id1)True면, 이 메소드는 항상 [``](https://docs.djangoproject.com/ko/4.2/topics/auth/customizing/#id3)True 를 리턴한다.
>	- `obj` 가 전달이 되면, 이 메소드는 모델에 대한 권한이 아니라 특정 객체에 대한 권한을 체크한다.
>- `has_module_perms`(_package_name_)[공식문서](https://docs.djangoproject.com/ko/4.2/topics/auth/customizing/#django.contrib.auth.models.PermissionsMixin.has_module_perms "이 정의에 대한 퍼머링크")
>	- 사용자가 주어진 패키지(django 애플리케이션 레이블)에 대한 모든 권한을 가지고 있으면, [``](https://docs.djangoproject.com/ko/4.2/topics/auth/customizing/#id1)True``를 리턴한다. 만일, [`User.is_active`](https://docs.djangoproject.com/ko/4.2/ref/contrib/auth/#django.contrib.auth.models.User.is_active "django.contrib.auth.models.User.is_active") 및 [`is_superuser`](https://docs.djangoproject.com/ko/4.2/ref/contrib/auth/#django.contrib.auth.models.User.is_superuser "django.contrib.auth.models.User.is_superuser") 모두 [``](https://docs.djangoproject.com/ko/4.2/topics/auth/customizing/#id3)True``라면, 이 메소드는 항상 [``](https://docs.djangoproject.com/ko/4.2/topics/auth/customizing/#id5)True``를 리턴한다.

>- objects 로 MyAccountManager()를 사용한다고 선언해줘야 커스텀한 내용을 가져다 쓸 수 있다.


출처 : https://developer-stories.tistory.com/17
출처 : https://docs.djangoproject.com/ko/4.2/topics/auth/customizing/

### setting.py
`AUTH_USER_MODEL = 'accounts.Account'`
- 커스텀한 포맷을 사용하기 위해 위와 같이 등록해주면 커스텀한 모델이 적용된다.

### admin.py
```python
from django.contrib import admin
from .models import Account
from django.contrib.auth.admin import UserAdmin

# Register your models here.

class AccountAdmin(UserAdmin):

    list_display = ('email', 'first_name', 'last_name', 'username', 'last_login', 'date_joined', 'is_active')
    list_display_links = ('email', 'first_name', 'last_name')
    readonly_fields = ('last_login', 'date_joined')
    ordering = ('-date_joined',)

    filter_horizontal = ()
    list_filter = ()
    fieldsets = ()

  
admin.site.register(Account, AccountAdmin)
```

>- `filter_horizontal` [참고](https://velog.io/@hokim/Django-Admin-6-%EC%BB%A4%EC%8A%A4%ED%85%80-M2M-%ED%95%84%EB%93%9C-filterhorizontal%EB%A1%9C-%EB%82%98%ED%83%80%EB%82%B4%EA%B8%B0)
>	- ![](https://i.imgur.com/yf8LsNg.png)

>- `list_filter` [참고](https://codingdog.tistory.com/entry/django-admin%EC%9D%98-listfilter%EB%A5%BC-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B3%A0-%EA%B0%84%EB%8B%A8%ED%95%98%EA%B2%8C-%EC%A0%81%EC%9A%A9%ED%95%B4-%EB%B4%85%EC%8B%9C%EB%8B%A4)
>	- 필터를 리스트형식으로 만들어준다. 
>- `fieldsets` [참고](https://jhoplin7259.tistory.com/185) 튜플과 딕셔너리 형태로 사용한다. 이것도 링크로 들어가서 사진으로 보는게 더 이해가 쉽다.

```python
from django.contrib import admin

class FlatPageAdmin(admin.ModelAdmin):
    fieldsets = [
        (
            None,
            {
                "fields": ["url", "title", "content", "sites"],
            },
        ),
        (
            "Advanced options",
            {
                "classes": ["collapse"],
                "fields": ["registration_required", "template_name"],
            },
        ),
    ]
```
![](https://i.imgur.com/P2JKxCz.png)

- [출처 및 참고](https://docs.djangoproject.com/en/4.2/ref/contrib/admin/)


> 위의 기능들을 구현했다면 마이그래이션 하기 전에 sqlite랑 기존에 생성했던 마이그레이션 파일들을 싹다 지워준다. 그래야 충돌이 없다.
> 	`__init__.py` 제외

> 관리자를 만들 때 다음과 같은 오류가 날 수 있는데

`$ python manage.py createsuperuser Superuser creation skipped due to not running in a TTY. You can run `manage.py createsuperuser` in your project to create one manually.`

> TTY
> 텔레타이프라이터 (TeleTYpewriter) 의 약자로
> 콘솔의 한 종류로 가상으로 만들어진 콘솔이다.


슈퍼 유저 생성은 TTY 환경 내에서 실행할 수 없기 때문이다.
근데 가상환경을 vscode 내 터미널에서 실행하면 된다.

무튼 해당 오류가 난다면 단순히 명령어 앞에 winpty를 써주면 해결된다.
`winpty python manage.py createsuperuser`
winpty 는 Windows 콘솔과 통신이 가능한 인터페이스를 제공하는 윈도우 소프트웨어 패키지다.
- [참고 및 출처](https://thinkground.studio/django-windows-git-bash-createsuperuser-%EC%83%9D%EC%84%B1-%EB%B6%88%EA%B0%80/)
출처 : https://wikidocs.net/10294
{% endraw %}