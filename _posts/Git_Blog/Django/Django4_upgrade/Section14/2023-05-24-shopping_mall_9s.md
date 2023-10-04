---
layout: single
title: " [Django] Shopping Mall (9)"
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
# 로그인 페이지 만들기

{% raw %}

## 회원가입 양식 만들기

```python
from django import forms
from .models import Account

class Registrationform(forms.ModelForm):
    password = forms.CharField(widget=forms.PasswordInput(attrs={
        'placeholder': 'Enter Password',
        'class': 'form-control',
    }))

    confirm_password = forms.CharField(widget=forms.PasswordInput(attrs={
        'placeholder': 'Confirm Password',
    }))

    class Meta:
        model = Account
        fields = ['first_name', 'last_name', 'phone_number','email','password']

    def __init__(self, *args, **kwargs):
        super(RegistrationForm, self).__init__(*args, **kwargs)
        self.fields['first_name'].widget.attrs['placeholder'] = 'Enter First Name'
        self.fields['last_name'].widget.attrs['placeholder'] = 'Enter last Name'
        self.fields['phone_number'].widget.attrs['placeholder'] = 'Enter Phone Number'
        self.fields['email'].widget.attrs['placeholder'] = 'Enter Email Address'

        for field in self.fields:  
            self.fields[field].widget.attrs['class'] = 'form-control'

    def clean(self):
        cleaned_data = super(RegistrationForm, self).clean()
        password = cleaned_data.get('password')
        confirm_password = cleaned_data.get('confirm_password')

        if password != confirm_password:
            raise forms.ValidationError(
                "Password does not match!"

            )
```
>- attrs 을 통해 dom 을 컨트롤할 수 있다.
>- ![](https://i.imgur.com/s56KjXv.png)

>	-또한 부트스트랩의 클래스 컨트롤 또한 가능하다.
>	- placeholder 같은 경우 중복되니 `__init__` 에 상속 받아서 반복문으로 처리했다.
>- css 클래스 같은 경우 그냥 반복문으로 처리
>- `cleaned_data = super(RegistrationForm, self).clean()` 는 `RegistrationForm`의 유효성을 검사한다.
>	-   The `clean()` method on a `Field` subclass is responsible for running `to_python()`, `validate()`, and `run_validators()` in the correct order and propagating their errors. If, at any time, any of the methods raise `ValidationError`, the validation stops and that error is raised. This method returns the clean data, which is then inserted into the `cleaned_data` dictionary of the form.
>	- [clean() 자세히](https://docs.djangoproject.com/ko/4.2/ref/forms/validation/)


### 전화번호 정규식 유효성 처리

- 전화번호 유효성을 처리하는데 역시나 장고는 라이브러리가 존재했다.
- [전화번호 정규식](https://velog.io/@mmy789/Django-User-%EB%AA%A8%EB%8D%B8%EC%97%90-%ED%95%B8%EB%93%9C%ED%8F%B0-%EB%B2%88%ED%98%B8-%EC%A0%80%EC%9E%A5%ED%95%98%EA%B8%B0) 이렇게 처리하는 방법도 있지만 난 그냥 라이브러리에서 좀 커스텀해서 쓰는게 낫다고 생각했다 (개인적으로 장고를 쓰는 이유가 빠른 개발이라고 생각하기 때문이다)
- [전화번호 라이브러리](https://django-phonenumber-field.readthedocs.io/en/latest/index.html) 단순 정규식 뿐만 아니라 국가별로도 나눠서 된다.
	- 나라별로 따로 만들어서 사용도 가능하다.


#### 라이브러리 설치
`pip install "django-phonenumber-field[phonenumberslite]"`
> 생각보다 라이브러리 크기가 크기도하고 어차피 한국 번호만 쓰면 되니까 라이트버전을 설치했다.

#### settings.py
```python
INSTALLED_APPS = [
    # Other apps…
    "phonenumber_field",
]
```

#### accounts>models.py
```python
class Account(AbstractBaseUser):
    first_name      = models.CharField(max_length=50)
    last_name       = models.CharField(max_length=50)
    username        = models.CharField(max_length=50, unique=True)
    email           = models.EmailField(max_length=100, unique=True)
    phone_number    = PhoneNumberField(region='KR',max_length=13)
```
> - 우선 해당 라이브러리를 사용하기 위해서는 필드를 해당 라이브러리로 변경해줘야한다.
> - region 부분에 ISO 3166 로 정규화된 코드를 쓰면 된다. 
> - 요즘 대부분 집 전화가 없는 경우가 많으니 최대 길이를 13으로 하면 집 전화 부터 핸드폰 번호까지 가능하다
> 	![](https://i.imgur.com/EdCmj82.png)





### Register view 처리
```python
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
    else:  
            form = RegistrationForm()        
    context = {
        'form' : form,
    }

    return render(request, 'accounts/register.html', context)
```
>- 유저네임을 따로 받아도 되지만 그냥 이메일 앞 주소를 유저네임으로 썼다
>	- 보통 도메인을 제외하면 거의 고유 값이나 다름 없으니 중복이 될 확률도 적다고 생각했다.
>- 커스텀한 `MyAccountManager`에 전화번호가 따로 없기 때문에 전화번호는 마지막에 넣었다.
>- 이메일로 인증을 해서 활성화를 시킬 예정이다. 일단 더미로 메세지를 만들어 놓았다.

## 에러 다루기
- 에러부분 커스텀은 장고 내장 기능일 이용해서 구현했다.
- [공식문서 보기](https://docs.djangoproject.com/en/4.2/ref/contrib/messages/)

#### settings.py
```python
from django.contrib.messages import constants as messages
MESSAGE_TAGS = {
    messages.ERROR: 'danger',
}
```

#### alerts.html
```python
{% if messages %}
    {% for message in messages %}
    <div id="message" class="container">
    <div {% if message.tags %} class="alert alert-{{ message.tags }}"{% endif %} role="alert">
      <button type="button" class="close" data-dismiss="alert"><span aria-hidden="true">&times;</span></button>
        {% if message.level == DEFAULT_MESSAGE_LEVELS.ERROR %}Error: {% endif %}
        {{ message }}
    </div>
    </div>
    {% endfor %}
{% endif %}
```


>- 베이직 템플릿에서 커스텀해서 사용하면 된다.
>- 해당 템플릿을 연동하기 위해서 이전에 register.html 안에 static 을 불러오는 것처럼 ` {% include 'includes/alerts.html' %}` 를 상단에 넣어준다.


```python
def register(request):

    if request.method == 'POST':
        form = RegistrationForm(request.POST)

        if form.is_valid():
			 .....
			 ...
			 ..
			 .
            messages.success(request,
                             'Thank you for registering with us. We have sent you a verification email to your email address [rathan.kumar@gmail.com]. Please verify it.')

    else:  
            form = RegistrationForm()        
    context = {
        'form' : form,
    }

    return render(request, 'accounts/register.html', context)
```
>- 조건에 맞게 메세지를 호출해주면 된다.



{% endraw %}
