---

layout: single
title: "[Django] 씹고 뜯고 맛보고 리팩토링"
categories: Django
tag: [Python,"[Django] 리팩토링 "]
toc: true
toc_sticky: true
author_profile: false
sidebar:

---
# 코드 리팩토링하기

프로젝트를 하면서 많이 배웠지만 배운만큼 또 까먹는다.
전체적으로 복습도 할겸 다음에 봤을 때 좀 더 기억하기 쉽게 간단하게라도 리팩토링을 했다.

## Accounts.py

### register
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

            # Create a user profile
            profile = UserProfile()
            profile.user_id = user.id
            profile.profile_picture = 'default/avatar.webp'
            profile.save()

            # USER ACTIVATION
            current_site = get_current_site(request)
            mail_subject = 'Please activate your account'
            message = render_to_string('accounts/account_verification_email.html', {

                "user" : user,
                "domain" : current_site,
                'uid': urlsafe_base64_encode(force_bytes(user.pk)),
                'token': default_token_generator.make_token(user),

            })

            to_email = email
            send_email = EmailMessage(mail_subject, message, to=[to_email])
            send_email.send()

            return redirect('/accounts/login/?command=verification&email='+email)

    else:  
            form = RegistrationForm()        
    context = {
        'form' : form,
    }
    return render(request, 'accounts/register.html', context)
```

>1. `form.save(commit=False)`를 사용하여 개별적으로 필드에 접근하는 대신 사용자 객체를 만들었다. 이렇게 하면 코드가 간소화되고 클린 데이터를 사용자 필드에 수동으로 할당하는 필요가 없앨 수 있다.
>2. 패스워드를 직접 할당하는 대신, `set_password` 메서드를 사용하여 사용자의 패스워드를 안전하게 설정했다.
>3. `UserProfile` 객체는 `objects.create` 메서드를 사용하여 직접 데이터베이스에 저장되도록 만들었다. 코드는 아래와 같다.

```python

def register(request):
    if request.method == 'POST':
        form = RegistrationForm(request.POST)
        if form.is_valid():
            user = form.save(commit=False)
            email = form.cleaned_data['email']
            password = form.cleaned_data['password']
            user.set_password(password)
            user.username = email.split("@")[0]
            user.save()
            
            # 사용자 프로필 생성
            profile = UserProfile.objects.create(user=user, profile_picture='default/avatar.webp')
            
            # 사용자 활성화
            current_site = get_current_site(request)
            mail_subject = '계정 활성화를 위해 확인해주세요'
            message = render_to_string('accounts/account_verification_email.html', {
                "user": user,
                "domain": current_site,
                'uid': urlsafe_base64_encode(force_bytes(user.pk)),
                'token': default_token_generator.make_token(user),
            })
            
            to_email = email
            send_email = EmailMessage(mail_subject, message, to=[to_email])
            send_email.send()
            
            return redirect('/accounts/login/?command=verification&email=' + email)
    else:
        form = RegistrationForm()
        
    context = {
        'form': form,
    }
    return render(request, 'accounts/register.html', context)

```

