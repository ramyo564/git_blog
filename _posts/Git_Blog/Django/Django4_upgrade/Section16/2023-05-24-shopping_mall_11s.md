---
layout: single
title: " [Django] Shopping Mall (11)"
categories: Django_ShoppingMall
tags:
  - Python
  - 비밀번호
  - Django
  - Project_ShoppingMall
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# 비밀번호 리셋

- 비밀번호 리셋은 이전에 아이디를 만들 때의 로직과 매우 유사하다.

{% raw %}

#### accounts/views.py
```python
def resetpassword_validate(request, uidb64, token):
    try:
        uid = urlsafe_base64_decode(uidb64).decode()
        user = Account._default_manager.get(pk=uid)
    except(TypeError, ValueError, OverflowError, Account.DoesNotExist):
        user = None
    if user is not None and default_token_generator.check_token(user, token):
        request.session['uid'] = uid
        messages.success(request, 'Please reset your password')
        return redirect('resetPassword')
    else:
        messages.error(request, 'This link has been expired!')
        return redirect('login')

def resetPassword(request):
    if request.method == 'POST':
        password = request.POST['password']
        confirm_password = request.POST['confirm_password']

        if password == confirm_password:
            uid = request.session.get('uid')
            user = Account.objects.get(pk=uid)
            user.set_password(password)
            user.save()
            messages.success(request, 'Password reset successful')
            return redirect('login')

        else:
            messages.error(request, 'Password do not match!')
            return redirect('resetPassword')

    else:
        return render(request, 'accounts/resetPassword.html')
```

```python
from django.urls import path
from . import views
urlpatterns = [
    path('forgotPassword/', views.forgotPassword, name='forgotPassword'),
    path('resetpassword_validate/<uidb64>/<token>/', views.resetpassword_validate, name='resetpassword_validate'),
    path('resetPassword/', views.resetPassword, name='resetPassword'),

]
```

> 딱히 특별한건 없다. 이전에 가입한 이메일이 일치하면 토큰을 보낸 링크로 들어와서 비밀번호를 다시 셋팅해준다.
> 해당 링크가 아닌 그냥 URL로 접속할 경우 입력이 안된다.

{% endraw %}
