---
layout: single
title: " [Django DRF] React DjangoDRF project (1) "
categories: Django_Chat_Service
tags:
  - Python
  - "[Django"
  - DRF]
  - DjangoDRF
  - +
  - React
  - chat
  - project
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# Chat Server Administaration API

## Build : Initiate Chat Server Administration App

```python
python manage.py startapp server
```

## Build : Creating a Django Custom User Model(AbstractUser)


```python
python manage.py startapp account
```

models.py
```python
from django.db import models
from django.contrib.auth.models import AbstractUser

class Account(AbstractUser):

    pass
```

settings.py
```python
AUTH_USER_MODEL = "account.Account"
```

## Build : Chat Server Administration Models (Database Tables and Fields)

models.py
```python
from django.db import models

from django.conf import settings

import unicodedata

  

# Create your models here.

class Category(models.Model):

    name = models.CharField(max_length=100)

    description = models.TextField(blank=True, null=True)

    def __str__(self):

        return self.name

class Server(models.Model):

    name = models.CharField(max_length=100)

    owner = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE, related_name="server_owner")

    category = models.ForeignKey(Category, on_delete=models.CASCADE, related_name="server_category")

    description = models.CharField(max_length=250, null=True)

    member = models.ManyToManyField(settings.AUTH_USER_MODEL)

    def __str__(self):

        return self.name

class Channel(models.Model):

    name = models.CharField(max_length=100)

    owner = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE, related_name="channel_owner")

    topic = models.CharField(max_length=100)

    server = models.ForeignKey(Server, on_delete=models.CASCADE, related_name="channel_server")

    def save(self, *args, **kwargs):

        self.name = unicodedata.normalize('NFKD', self.name).lower()

        super(Channel, self).save(*args, **kwargs)

    def __str__(self):

        return self.name
```

`self.name = unicodedata.normalize('NFKD', self.name).lower()` 이 부분은 채널명을 만들 때 영어와 한글이 섞여 들어가거나 한글만 사용했을 경우에도 문제가 없게 하기 위해서 설정했다.

## API Documentation : Configurung DRF-Spectacular with Swagger UI : Installation and Initialisation

```python
pip install drf-spectacular
```

settings.py
```python
INSTALLED_APPS = [
    # ALL YOUR APPS
    'drf_spectacular',
]

REST_FRAMEWORK = {
    # YOUR SETTINGS
    'DEFAULT_SCHEMA_CLASS': 'drf_spectacular.openapi.AutoSchema',
}
SPECTACULAR_SETTINGS = {
    'TITLE': 'Your Project API',
    'DESCRIPTION': 'Your project description',
    'VERSION': '1.0.0',
    'SERVE_INCLUDE_SCHEMA': False,
    # OTHER SETTINGS
}

```
[링크](https://drf-spectacular.readthedocs.io/en/latest/readme.html#installation)

```python
python manage.py spectacular --color --file schema.yml
```

urls.py
```python
from drf_spectacular.views import SpectacularAPIView, SpectacularRedocView, SpectacularSwaggerView
urlpatterns = [
    # YOUR PATTERNS
    path('api/schema/', SpectacularAPIView.as_view(), name='schema'),
    # Optional UI:
    path('api/schema/swagger-ui/', SpectacularSwaggerView.as_view(url_name='schema'), name='swagger-ui'),
    path('api/schema/redoc/', SpectacularRedocView.as_view(url_name='schema'), name='redoc'),
]
```

SpectacularRedocView 혹은 SpectacularSwaggerView 로 사용할 수 있다.     
아니면 둘 다 만들어서 사용 가능하다.
![](https://i.imgur.com/YWjEycX.png)

![](https://i.imgur.com/8pvsnnf.png)

## Build : Configuring Default Authentication Classes in DRF
settings.py
```python
REST_FRAMEWORK = {

    'DEFAULT_SCHEMA_CLASS': 'drf_spectacular.openapi.AutoSchema',

    'DEFAULT_AUTHENTICATION_CLASSES': [

        'rest_framework.authentication.SessionAuthentication',

    ]

}
```

## Build : Creating an API Endpoint for Filtering Servers by category

urls.py
```python
from drf_spectacular.views import SpectacularAPIView, SpectacularRedocView, SpectacularSwaggerView

from django.contrib import admin

from django.urls import path

from rest_framework.routers import DefaultRouter

from server.views import ServerListViewSet

  

router = DefaultRouter()

router.register("api/server/select", ServerListViewSet)

  

urlpatterns = [

    path('admin/', admin.site.urls),

    path('api/schema/', SpectacularAPIView.as_view(), name='schema'),

    path('api/schema/ui/', SpectacularSwaggerView.as_view()),

] + router.urls
```

class나 functin 둘다 사용 가능하지만 class 로 view를 만들게 되면 router 등록해서 편하게 사용가능하다.


serializer.py
```python
from rest_framework import serializers

from .models import Server, Category

  

class ServerSerializer(serializers.ModelSerializer):

    class Meta:

        model = Server

        fields = "__all__"
```



views.py
```python
from django.shortcuts import render

from rest_framework import viewsets

from rest_framework.response import Response

  

from .models import Server

from .serializer import ServerSerializer

  
  

class ServerListViewSet(viewsets.ViewSet):

    queryset = Server.objects.all()

    def list(self, request):

        category = request.query_params.get("category")

        if category:

            self.queryset = self.queryset.filter(category=category)

            # self.queryset = self.queryset.filter(category__name=category)

        serializer = ServerSerializer(self.queryset, many=True)

        return Response(serializer.data)
```

![](https://i.imgur.com/ilrax2h.png)


## Build : Creating an API Endpoint for Filtering Servers by User and Quantity

admin.py
```python
from django.contrib import admin

from django.contrib.auth.admin import UserAdmin

  

from .models import Account

  

admin.site.register(Account, UserAdmin)
```

views.py
```python
from django.shortcuts import render

from rest_framework import viewsets

from rest_framework.response import Response

  

from .models import Server

from .serializer import ServerSerializer

  
  

class ServerListViewSet(viewsets.ViewSet):

    queryset = Server.objects.all()

    def list(self, request):

        category = request.query_params.get("category")

        qty = request.query_params.get('qty')

        by_user = request.query_params.get('by_user') == "true"

        if category:

            self.queryset = self.queryset.filter(category=category)

            # self.queryset = self.queryset.filter(category__name=category)

        if by_user:

            user_id = request.user.id

            self.queryset = self.queryset.filter(member=user_id)

        if qty:

            self.queryset = self.queryset[: int(qty)]

        serializer = ServerSerializer(self.queryset, many=True)

        return Response(serializer.data)
```

![](https://i.imgur.com/jpTOcc0.png)

## Build : Creating an API Endpoint for Filtering Servers by Server ID


views.py
```python
from django.shortcuts import render

from rest_framework import viewsets

from rest_framework.exceptions import AuthenticationFailed, ValidationError

from rest_framework.response import Response

  

from .models import Server

from .serializer import ServerSerializer

  
  

class ServerListViewSet(viewsets.ViewSet):

    queryset = Server.objects.all()

    def list(self, request):

        category = request.query_params.get("category")

        qty = request.query_params.get('qty')

        by_user = request.query_params.get('by_user') == "true"

        by_serverid = request.query_params.get("by_serverid")

        if by_user or by_serverid and not request.user.is_authenticated:

            raise AuthenticationFailed()

        if category:

            self.queryset = self.queryset.filter(category__name=category)

        if by_user:

            user_id = request.user.id

            self.queryset = self.queryset.filter(member=user_id)

        if qty:

            self.queryset = self.queryset[: int(qty)]

        if by_serverid:

            try:

                self.queryset = self.queryset.filter(id=by_serverid)

                if not self.queryset.exists():

                    raise ValidationError(detail=f"Server with id {by_serverid} not found")

            except ValueError:

                raise ValidationError(detail=f"Server value Error")

        serializer = ServerSerializer(self.queryset, many=True)

        return Response(serializer.data)
```

![](https://i.imgur.com/4jDMVri.png)

```python
if by_user or by_serverid and not request.user.is_authenticated:

	raise AuthenticationFailed()
```

사용자가 확인이 될 때만 나오게 출력 아닐 경우 403 오류 출력

## Build : Returning Related Data - Server Associated Channels

![](https://i.imgur.com/ZmgUOJN.png)


```python
from rest_framework import serializers

from .models import Server, Category, Channel

  

class ChannelSerializer(serializers.ModelSerializer):

    class Meta:

        model = Channel

        fields = "__all__"

  

class ServerSerializer(serializers.ModelSerializer):

    channel_server = ChannelSerializer(many=True)

    class Meta:

        model = Server

        fields = "__all__"
```

## Build : Creating an API Endpoint for Filtering Servers and Returning Annotation of the Number of Members

![](https://i.imgur.com/yvCqewT.png)

현재 api를 보면 등록된 member가 전부 출력이 된다. 프론트 입장에서 이런 내용은 불필요하다. 따라서 해당 서버에 사람이 몇 명이 있는지 출력하고 만약 사람이 없다면 출력하지 않게 만드는 게 좋다.

serializer.py
```python
from rest_framework import serializers

from .models import Server, Category, Channel

  

class ChannelSerializer(serializers.ModelSerializer):

    class Meta:

        model = Channel

        fields = "__all__"

  

class ServerSerializer(serializers.ModelSerializer):

    num_members = serializers.SerializerMethodField()

    channel_server = ChannelSerializer(many=True)

    class Meta:

        model = Server

        exclude = ("member",)

    def get_num_members(self, obj):

        if hasattr(obj, "num_members"):

            return obj.num_members

        return None

    def to_representation(self, instance):

        data = super().to_representation(instance)

        num_members = self.context.get("num_members")

        if not num_members:

            data.pop("num_members", None)

        return data
```

views.py
```python
from django.shortcuts import render

from rest_framework import viewsets

from rest_framework.exceptions import AuthenticationFailed, ValidationError

from rest_framework.response import Response

from django.db.models import Count

from .models import Server

from .serializer import ServerSerializer

  
  

class ServerListViewSet(viewsets.ViewSet):

    queryset = Server.objects.all()

    def list(self, request):

        category = request.query_params.get("category")

        qty = request.query_params.get('qty')

        by_user = request.query_params.get('by_user') == "true"

        by_serverid = request.query_params.get("by_serverid")

        with_num_members = request.query_params.get('with_num_members') == "true"

        if by_user or by_serverid and not request.user.is_authenticated:

            raise AuthenticationFailed()

        if category:

            self.queryset = self.queryset.filter(category__name=category)

        if by_user:

            user_id = request.user.id

            self.queryset = self.queryset.filter(member=user_id)

        if with_num_members:

            self.queryset = self. queryset.annotate(num_members=Count("member"))

        if qty:

            self.queryset = self.queryset[: int(qty)]

        if by_serverid:

            try:

                self.queryset = self.queryset.filter(id=by_serverid)

                if not self.queryset.exists():

                    raise ValidationError(detail=f"Server with id {by_serverid} not found")

            except ValueError:

                raise ValidationError(detail=f"Server value Error")

        serializer = ServerSerializer(self.queryset, many=True, context={"num_members": with_num_members})

        return Response(serializer.data)
```

annotate 는 sql의 group by 와 유사한 동작을 하지만 sql 과 달리 별도의 데이터베이스 쿼리를 작성하지 않고 메모리 내에서 처리되므로 성능 개선에 도움이 될 수 있다.
위에서는 `num_members` 로 필드를 만들어서 동작을 수행한다.
파라미터 `with_num_members` 가 true 가 될 경우 num_members 를 리턴하고 그게 아닐 경우 해당 필드는 직렬화에서 표시 되지 않는다.

![](https://i.imgur.com/fkzhNSn.png)

## ChatGPT : Creating DocStrings with Chat GPT

build a docstring in the google style
add more detail to docstring

## API Documentation : Creating an API Endpoint Decorator for Detailing Endpoints with Query Parameters and OpenAPI Schema Extension


schema.py
```python
from drf_spectacular.types import OpenApiTypes

from drf_spectacular.utils import OpenApiParameter, extend_schema

  

from .serializer import ChannelSerializer, ServerSerializer

  

server_list_docs = extend_schema(

    responses=ServerSerializer(many=True),

    parameters=[

        OpenApiParameter(

            name="category",

            type=OpenApiTypes.STR,

            location=OpenApiParameter.QUERY,

            description="Category of servers to retrieve",

        ),

        OpenApiParameter(

            name="qty",

            type=OpenApiTypes.INT,

            location=OpenApiParameter.QUERY,

            description="Number of servers to retrieve",

        ),

        OpenApiParameter(

            name="by_user",

            type=OpenApiTypes.BOOL,

            location=OpenApiParameter.QUERY,

            description="Filter servers by the current authenticated user (True/False)",

        ),

        OpenApiParameter(

            name="with_num_members",

            type=OpenApiTypes.BOOL,

            location=OpenApiParameter.QUERY,

            description="Include the number of numbers for each server in the response",

        ),

        OpenApiParameter(

            name="by_serverid",

            type=OpenApiTypes.INT,

            location=OpenApiParameter.QUERY,

            description="Include server by id",

        ),

    ]

)
```

![](https://i.imgur.com/G4LovFq.png)


## Build : Configuring Django to Handle Storing Images

```python
from django.conf import settings

from django.conf.urls.static import static

from django.contrib import admin

from django.urls import path

from drf_spectacular.views import (SpectacularAPIView, SpectacularRedocView,

                                   SpectacularSwaggerView)

from rest_framework.routers import DefaultRouter

  

from server.views import ServerListViewSet

  

router = DefaultRouter()

router.register("api/server/select", ServerListViewSet)

  

urlpatterns = [

    path('admin/', admin.site.urls),

    path('api/schema/', SpectacularAPIView.as_view(), name='schema'),

    path('api/schema/ui/', SpectacularSwaggerView.as_view()),

] + router.urls

  

if settings.DEBUG:

    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

![](https://i.imgur.com/S3NxAqJ.png)

## Build : Model Refactor for Icons and Banners in Django Model

[구글 아이콘](https://fonts.google.com/icons?selected=Material+Symbols+Outlined:home:FILL@0;wght@400;GRAD@0;opsz@48)


models.py
```python
def category_icon_upload_path(instance, filename):

    return f"category/{instance.id}/category_icon/{filename}"

  

class Category(models.Model):

    name = models.CharField(max_length=100)

    description = models.TextField(blank=True, null=True)

    icon = models.FileField(upload_to=category_icon_upload_path, null=True, blank=True)

    def save(self, *args, **kwargs):

        if self.id:

            existing = get_object_or_404(Category, id=self.id)

            if existing.icon != self.icon:

                existing.icon.delete(save=False)

        super(Category, self).save(*args, **kwargs)

    @receiver(models.signals.pre_delete, sender='server.Category')

    def category_delete_files(sender, instance, **kwargs):

        for field in instance._meta.fields:

            if field.name == "icon":

                file = getattr(instance, field.name)

                if file:

                    file.delete(save=False)

    def __str__(self):

        return self.name
```

`category_icon_upload_path` 함수: 이 함수는 파일 필드에 업로드 된 파일의 저장 경로를 정의하는 함수다. `instance` 인자는 모델 인스턴스(카테고리 객체)를 나타내며, `filename` 인자는 업로드 된 파일의 원래 이름이다. 함수 내부에서는 `instance.id`를 사용하여 카테고리의 ID를 가져와서 해당 ID 폴더 내에 `category_icon` 폴더를 만들고, 원래 파일 이름과 함께 저장 경로를 반환한다.     

`save` 메서드: 이 메서드는 카테고리 객체를 저장할 때 호출되는 메서드다. 기존 카테고리 객체가 이미 존재하는 경우(기존 객체의 ID가 있는 경우), 해당 아이콘이 변경되었는지 확인하고 변경되었다면 이전 아이콘 파일을 삭제한다. `get_object_or_404` 함수는 해당 ID로 카테고리를 조회하고, 존재하지 않는 경우 404 에러를 발생시키는 함수다. 이후 `super()`를 호출하여 기존 `save` 메서드를 실행한다.     

`category_delete_files` 함수: 이 함수는 카테고리 객체가 삭제되기 전에 실행되도록 신호를 연결하는 메서드다. 카테고리 모델 객체가 삭제되기 전에 아이콘 파일을 삭제한다. `models.signals.pre_delete` 신호를 활용하여 객체가 삭제되기 직전에 실행되는 코드를 작성한다.     
`@receiver` 데코레이터는 Django에서 시그널(Signal)과 연결하여 특정 이벤트가 발생할 때 특정 함수를 실행할 수 있게 해주는 기능이다. 위의 코드에서 `@receiver(models.signals.pre_delete, sender='server.Category')`는 `pre_delete` 신호가 발생할 때 `server.Category` 모델과 연결된 함수인 `category_delete_files`를 실행하도록 설정하고 있다.

Django에서 시그널은 다양한 이벤트를 나타내며, 모델에서의 생성, 수정, 삭제 등과 관련된 이벤트들이 있다. `pre_delete` 신호는 객체가 삭제되기 전에 발생하는 시그널로, 해당 객체가 삭제되기 전에 어떤 동작을 실행하고 싶을 때 유용하게 사용된다.

`@receiver` 데코레이터는 다음과 같은 인자를 사용한다:

- `signal`: 연결할 시그널 객체를 지정합니다. 예를 들어, `models.signals.pre_delete`는 `pre_delete` 시그널을 의미한다.
- `sender`: 시그널을 보내는 모델 클래스를 지정한다. 위의 코드에서는 `server.Category` 모델과 연결하여 해당 모델 객체가 삭제될 때 시그널을 발생시킨다.
- `**kwargs`: 추가적인 인자를 지정할 수 있다.

`category_delete_files` 함수는 `@receiver` 데코레이터와 함께 사용될 때, `pre_delete` 시그널이 발생하면 해당 함수가 실행된다. 함수 내부에서는 카테고리 객체의 `icon` 필드를 삭제합니다. 이로 인해 카테고리 객체가 삭제되기 전에 해당 카테고리의 아이콘 파일이 함께 삭제되도록 처리할 수 있다.

### Django Signal
Django에서는 다양한 시그널(Signal)이 제공되고 있다. 각 시그널은 특정 이벤트가 발생할 때 미리 정의된 동작을 실행할 수 있도록 한다. 아래는 Django에서 자주 사용되는 시그널들의 목록이다:

1. `pre_save`: 객체가 데이터베이스에 저장되기 전에 발생하는 시그널로, `save()` 메서드 호출 시 실행된다.
    
2. `post_save`: 객체가 데이터베이스에 저장된 후에 발생하는 시그널로, `save()` 메서드 호출 이후 실행된다.
    
3. `pre_delete`: 객체가 데이터베이스에서 삭제되기 전에 발생하는 시그널로, `delete()` 메서드 호출 전에 실행된다.
    
4. `post_delete`: 객체가 데이터베이스에서 삭제된 후에 발생하는 시그널로, `delete()` 메서드 호출 이후 실행된다.
    
5. `m2m_changed`: Many-to-Many 관계 필드에서 관계가 변경될 때 발생하는 시그널입이다.
    
6. `pre_init`: 객체가 초기화되기 전에 발생하는 시그널로, `__init__` 메서드 호출 이전에 실행된다.
    
7. `post_init`: 객체가 초기화된 후에 발생하는 시그널로, `__init__` 메서드 호출 이후 실행된다.
    
8. `request_started`: HTTP 요청이 시작될 때 발생하는 시그널이다.
    
9. `request_finished`: HTTP 요청이 완료될 때 발생하는 시그널이다.
    
10. `got_request_exception`: HTTP 요청 처리 중 예외가 발생할 때 발생하는 시그널이다.
    

위의 시그널들은 Django의 `django.db.models.signals` 모듈에서 제공됩니다. 이러한 시그널들을 활용하여 모델의 상태 변화나 애플리케이션의 다양한 이벤트에 반응하여 특정 동작을 실행할 수 있다. `@receiver` 데코레이터를 사용하여 시그널과 특정 함수를 연결하여 이벤트 처리를 할 수 있다.

settings.py
```python 
STATIC_URL = "static/"
MEDIA_ROOT = os.path.join(BASE_DIR, "media")
NEDIA_URL = "media/"

```

## Build : Model Refactor for Icons and Banners in Django Model (Server)
```python 
pip install pillow
```

```python
from django.db import models

from django.conf import settings

import unicodedata

from django.shortcuts import get_object_or_404

from django.dispatch import receiver

  

def server_icon_upload_path(instance, filename):

    return f"server/{instance.id}/server_icons/{filename}"

  

def server_banner_upload_path(instance, filename):

    return f"server/{instance.id}/server_banners/{filename}"

  

def category_icon_upload_path(instance, filename):

    return f"category/{instance.id}/category_icon/{filename}"

  

class Category(models.Model):

    name = models.CharField(max_length=100)

    description = models.TextField(blank=True, null=True)

    icon = models.FileField(upload_to=category_icon_upload_path, null=True, blank=True)

    def save(self, *args, **kwargs):

        if self.id:

            existing = get_object_or_404(Category, id=self.id)

            if existing.icon != self.icon:

                existing.icon.delete(save=False)

        super(Category, self).save(*args, **kwargs)

    @receiver(models.signals.pre_delete, sender='server.Category')

    def category_delete_files(sender, instance, **kwargs):

        for field in instance._meta.fields:

            if field.name == "icon":

                file = getattr(instance, field.name)

                if file:

                    file.delete(save=False)

    def __str__(self):

        return self.name

  

class Server(models.Model):

    name = models.CharField(max_length=100)

    owner = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE, related_name="server_owner")

    category = models.ForeignKey(Category, on_delete=models.CASCADE, related_name="server_category")

    description = models.CharField(max_length=250, null=True)

    member = models.ManyToManyField(settings.AUTH_USER_MODEL)

    def __str__(self):

        return f"{self.name} - {self.id}"

  

class Channel(models.Model):

    name = models.CharField(max_length=100)

    owner = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE, related_name="channel_owner")

    topic = models.CharField(max_length=100)

    server = models.ForeignKey(Server, on_delete=models.CASCADE, related_name="channel_server")

    banner = models.ImageField(upload_to=server_banner_upload_path, null=True, black=True)

    icon = models.ImageField(upload_to=server_icon_upload_path, null=True, blank=True)

    def save(self, *args, **kwargs):

        if self.id:

            existing = get_object_or_404(Category, id=self.id)

            if existing.icon != self.icon:

                existing.icon.delete(save=False)

            if existing.banner != self.banner:

                existing.banner.delete(save=False)

        super(Category, self).save(*args, **kwargs)

    @receiver(models.signals.pre_delete, sender='server.Server')

    def category_delete_files(sender, instance, **kwargs):

        for field in instance._meta.fields:

            if field.name == "icon" or field.name == "banner":

                file = getattr(instance, field.name)

                if file:

                    file.delete(save=False)

    def __str__(self):

        return self.name
```

## Building : Creating a Django Model Validation Class for Image Field Creation and Updates

validators.py
```python
import os

  

from django.core.exceptions import ValidationError

from PIL import Image

  
  

def validate_icon_image_size(image):

    if image:

        with Image.open(image) as img:

            if img.width > 70 or img.height > 70:

                raise ValidationError(

                    f"The maximum allowed dimensions for the image are 70x70 - size of image you uploaded: {img.size}"

                )

  
  

def validate_image_file_exstension(value):

    ext = os.path.splitext(value.name)[1]

    valid_extensions = [".jpg", ".jpeg", ".png", ".gif"]

    if not ext.lower() in valid_extensions:

        raise ValidationError("Unsupported file extension")
```

![](https://i.imgur.com/2PNH2mA.png)

이미지 형식이나 사이즈가 안 맞을 경우 오류 생성
필로우 라이브러리를 통해 이미지파일 크기를 확인하고 파일의 확장자를 추출해 유효성 검사를 진행한다.     
유효성 검사에 실패하면 이미지 업로드가 실패한다.