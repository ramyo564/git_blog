---
layout: single
title: " [Django] Shopping Mall (10)"
categories: Django_ShoppingMall
tags:
  - Python
  - 로그인
  - Django
  - Project_ShoppingMall
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# 로그인 페이지 연결하기

{% raw %}

#### accounts/views.py

```python
from django.contrib import messages, auth
from django.contrib.auth.decorators import login_required

def login(request):
    if request.method == 'POST':
        email = request.POST['email']
        password = request.POST['password']
        user = auth.authenticate(email=email, password=password)

        if user is not None:
            auth.login(request, user)
            return redirect('home')

        else:
            messages.error(request, 'Invalid login credentials')
            return redirect('login')
    return render(request, 'accounts/login.html')

@login_required(login_url = 'login')
def logout(request):
    auth.logout(request)
    messages.success(request, 'You are logged out')
    
    return redirect('login')
```
>- `auth.authenticate` 를 이용하면 유효성 검사를 쉽게 할 수 있다.
>- [로그인 로그아웃 참고](https://docs.djangoproject.com/en/4.2/topics/auth/default/)
>- `login_required` 은 함수형 뷰에서만 사용 가능하고 클래스형 뷰에서는 사용할 수 없다. 이럴 때는 `LoginRequiredMixin`를 상속 받아서 하면 된다.
>- [login_required](https://sooooooyn.tistory.com/6) 해당내용에 대해서도 위의 공식문서에 `LoginRequiredMixin` 를 포함해서 자세하게 나온다.


## Activation link

#### accounts/views.py
```python
from django.contrib.sites.shortcuts import get_current_site
from django.template.loader import render_to_string
from django.utils.http import urlsafe_base64_encode, urlsafe_base64_decode
from django.utils.encoding import force_bytes
from django.contrib.auth.tokens import default_token_generator
from django.core.mail import EmailMessage


def register(request):
    if request.method == 'POST':
        form = RegistrationForm(request.POST)

        if form.is_valid():
            first_name = form.cleaned_data['first_name']
            last_name = form.cleaned_data['last_name']
            phone_number = form.cleaned_data['phone_number']
            email = form.cleaned_data['email']
            password = form.cleaned_data['password']
            username = email.split("@")[0]
            user = Account.objects.create_user(
                first_name=first_name,
                last_name=last_name,
                email=email,
                username=username,
                password=password
                )
            user.phone_number = phone_number
            user.save()

            # USER ACTIVATION

            current_site = get_current_site(request)
            mail_subject = 'Please activate your account'
            message = render_to_string(
            'accounts/account_verification_email.html', {
                "user" : user,
                "domain" : current_site,
                'uid': urlsafe_base64_encode(force_bytes(user.pk)),
                'token': default_token_generator.make_token(user),
            })

            to_email = email
            send_email = EmailMessage(mail_subject, message, to=[to_email])
            send_email.send()
            messages.success(
            request,
	           'We have sent you a verification email [email@gmail.com]. Please verify it.'
	           )
            return redirect('/accounts/login/?command-verification&email='+email)

    else:  
            form = RegistrationForm()        
    context = {
        'form' : form,
    }
    return render(request, 'accounts/register.html', context)
```
>- `get_current_site`
>	-request를 보낸 site를 알려주고 이를 templates에 넘겨서 이를 통해 url에 동적으로 접근하도록 한다.
>	-[참고](https://docs.djangoproject.com/en/4.2/ref/contrib/sites/)
>- `render_to_string`
>	[참고](https://docs.djangoproject.com/en/4.2/topics/templates/)
>	-template 객체를 반환하면서 동시에 렌더링한다.
> - user.pk는 자연수 값이다 이를 `force_bytes` 를 통해 bytes로 변환해준다.  그 후 `urlsafe_base64_encode` 를 통해 인코딩해서 `uid` 를 만든다.
>- 이전에는 six를 통해 tokens.py 를 만들고 따로 상속받아서 해결했었는데 굳이 그럴 필요 없다.
>- `default_token_generator.make_token` 를 통해 토큰을 만들어준다.
>- `EmailMessage` 를 이용해서 이메일을 보내준다.
>- `'/accounts/login/?command-verification&email='+email` 를 통해 유저가 로그인 페이지에서 패스워드만 입력하면 되게 만든다. (email 주소는 이미 써져있음)

#### account_verification_email.html
```python
{% autoescape off %}

Hi {{user.first_name}},

Please click on below link to confirm your registration.
http://{{domain}}{% url 'activate' uidb64=uid token=token %}

If you think it's not you, please ignore this email.

{% endautoescape %}
```

#### settings.py
```python
EMAIL_HOST = 'smtp.gmail.com'
EMAIL_HOST_USER = MY_EMAIL
EMAIL_HOST_PASSWORD = EMAIL_PASSWORD
EMAIL_PORT = '587'
EMAIL_USE_TLS = 'True'
```

#### accounts/urls.py
```python
from django.urls import path
from . import views

urlpatterns = [
    path('activate/<uidb64>/<token>/', views.activate, name='activate'),
]
```
> 이메일을 링크를 클릭하면 path에 있는 주소로 들어오게 된다. 그럼 views에 있는 activate를 호출하게 된다.

#### accounts/views.py
```python
def activate(reqeust, uidb64, token):
    # return HttpResponse('ok')
    try:
        uid = urlsafe_base64_decode(uidb64).decode()
        user = Account._default_manager.get(pk=uid)
    except(
    TypeError, ValueError, OverflowError, Account.DoesNotExist
    ):
        user = None
    if user is not None and default_token_generator.check_token(
    user, token
    ):
        user.is_active = True
        user.save()
        messages.success(
        reqeust,
        'Thank you! Your account is activated.')
        return redirect('login')

    else:
        messages.error(reqeust, 'Invalid activation link')
        return redirect('register')
```
>- 디코딩한 uid를 `_default_manager` 를 통해 쿼리셋으로 user 를 생성한다
>- [`_default_manager `참고](https://docs.djangoproject.com/en/4.2/topics/db/managers/) -> `MyAccountManager` 로 커스텀 했기 때문에 `_base_manager' 대신 사용한다
>- [참고](https://blog.hwahae.co.kr/all/tech/tech-tech/4108)
>- [참고2](https://www.reddit.com/r/django/comments/1069mt0/what_is_the_difference_between_userobjectsget_and/)

### `_base_manager` vs `_default_manager`

In Django, the `**_default_manager**` attribute is automatically added to your model class and provides a reference to the default manager for that model. The default manager is the one defined in the model's `**Meta**` class using the `**objects**` attribute.      

The purpose of the `**_default_manager**` is to allow easy access to the default manager for the model, even if you have defined custom managers for your model. It ensures that you always have a reference to the default manager, regardless of any customizations you have made.      

Here's a breakdown of the different manager attributes:      

`**_default_manager**`: This attribute provides access to the default manager for the model. It is automatically generated by Django and points to the manager specified in the `**objects**` attribute of the model's `**Meta**` class.      

`**_base_manager**`: This attribute provides access to the base manager for the model. The base manager is the first manager defined in the model's `**Meta**` class. If you haven't defined any custom managers, the default manager is also the base manager.      

So, to answer your question about using custom managers:      

`**_default_manager**` is used to access the default manager, regardless of whether you have defined custom managers or not. It ensures that you can always access the default manager reliably.      

`**_base_manager**` is used when you want to specifically access the base manager, which could be the default manager or a custom manager if you have defined one. This can be useful if you want to bypass any customizations made in the default manager and directly access the base functionality.      

  

`user = Account._default_manager.get(pk=uid)`       

  

In the above code, `**_default_manager**` is used to retrieve a user object based on the decoded `**uid**` value. It provides access to the default manager of the `**Account**` model, allowing you to retrieve the user using the primary key (`**pk**`) value `**uid**`.      

Overall, `**_default_manager**` and `**_base_manager**` provide different ways to access the managers associated with a model, allowing you to work with the default manager or any custom managers you have defined, depending on your needs.      


{% endraw %}