---

layout: single
title: " [Django DRF] project (1)"
categories: Django
tag: [Python,"[Django DRF] Project eCommerce RESTful API","[Django DRF] 테이블 연결시키기"]
toc: true
toc_sticky: true
author_profile: false
sidebar:

---
# 새로운 테이블을 추가

![](https://i.imgur.com/HCYYswy.png)


![](https://i.imgur.com/G7gYUmX.png)


Product 1개는 한 개의 브랜드를 갖지만 한 개의 브랜드는 여러개의 Product를 갖을 수 있다.    
-> one to many , 그리고 Brand의 FK를 갖는다.      

[장고 트리구조 만들기](https://django-mptt.readthedocs.io/en/latest/)

Django-mptt는 Django 웹 프레임워크를 사용하는 애플리케이션에서 트리 구조를 효율적으로 처리하기 위한 라이브러리다.     
MPTT는 "Modified Preorder Tree Traversal"의 약어로, 트리 구조를 데이터베이스에 저장하고 관리하는 방식이다.     


일반적으로 트리 구조를 데이터베이스에 저장하는 방법은 노드의 부모-자식 관계를 사용하는 것이다.     
하지만 이러한 방식은 트리의 깊이가 깊어질수록 성능 저하가 발생할 수 있다.     
Django-mptt는 이러한 문제를 해결하기 위해 MPTT 알고리즘을 사용한다.      


Django-mptt를 사용하면 트리 구조를 간단하게 조작하고 쿼리할 수 있다.      
특히, 트리 내의 노드의 순서를 유지하면서 삽입, 삭제, 이동 등의 작업을 수행할 수 있다.       
또한, Django-mptt는 트리 구조를 쿼리하는 데 사용되는 메소드와 도우미 함수를 제공하여 개발자가 트리 구조를 쉽게 다룰 수 있도록 도와준다.       

Django-mptt는 다양한 프로젝트에서 카테고리, 댓글, 메뉴 등과 같은 트리 구조 데이터를 효과적으로 관리하기 위해 사용된다.      
Django-mptt는 Django 웹 프레임워크와 통합되어 사용되며, Django의 모델과 관리자 인터페이스와 함께 잘 작동한다.

Regenerate response


## Build: Creating the category seializer

![](https://i.imgur.com/LfvvOAI.png)


serializer 는 Python 객체를 가져온다.     
예를 들어 Django에서는 쿼리셋이나 모델 인스턴스와 같은 것을 가져와서, 이를 객체나 호환 가능한 콘텐츠 유형으로 변환하는 작업을 한다.     

Django API와 상호 작용하는 프론트엔드 클라이언트가 있다고 가정해볼 때 클라이언트는 서버에 요청을 보낼 것이며, Django는 그것을 받아들일 것이고, 데이터베이스에서 일부 데이터를 가져올 수도 있다.      
일반적으로 데이터가 데이터베이스에서 반환될 때는 쿼리셋 형태로 반환된다.     

문제는 여기서 프론트엔드, 클라이언트가 데이터를 요구한다는 점이다.      

이 시점에서 데이터의 형식은 JavaScript와 같은 프론트엔드 기술과 호환되지 않는다.      
따라서 Python 객체, 쿼리셋, 모델 인스턴스를 JSON과 같은 형식으로 변환하여 프론트엔드 클라이언트가 해당 데이터를 사용할 수 있도록 해야한다.     

serializer는 이러한 과정을 지원하여 데이터를 변환시켜준다.     
예를 들어, Python 객체, Django 쿼리셋을 JSON으로 변환하거나 반대로 변환하는 작업을 수행한다.      
이렇게 변환된 데이터를 프론트엔드 클라이언트에게 다시 전달하고, 클라이언트는 해당 데이터를 적절하게 렌더링할 수 있다.     

### serializer 구축

먼저, `rest_framework`에서 `serializers`를 가져온다.     
이전에 생성한 테이블인 `Brand`, `Category`, `Product`를 가져온다음
serializer를 생성해야 한다. 

여기서는 테이블 이름, 즉 `Category` 클래스의 이름을 사용하여 스털리저를 생성한다.      
이를 통해 스털리저임을 명확하게 식별할 수 있다.      

그리고 `serializers.ModelSerializer`를 상속하여 시리얼라이저를 정의한다.      

여기서 필드를 정의하는 것이 필요하다.     
예를 들어 `Category`에 10개의 필드가 있다고 상상해본다면 시리얼라이저 에서는 한 개의 필드만 정의할 수 있다.      

그러므로 프론트엔드가 쿼리를 만들어 카테고리 테이블에서 모든 데이터를 수집하지만, 그 중 한 필드만을 스털리저로 직렬화한다.     

모델을 사용하려면 `model` 또는 `model = Category`와 같이 모델을 지정하면 된다.      

그리고 시리얼라이저에 포함시킬 필드를 지정할 수 있다. 이는 실제로 클라이언트에게 반환될 데이터를 설명한다.      
이 경우에는 모든 필드를 포함하도록 했다.      
필드를 개별적으로 지정할 수도 있지만, 처음에는 간단하게 모든 필드를 선택하도록 했다.     

```python
serializers.py

from rest_framework import serializers
from .models import Brand, Category, Product

class CategorySerializer(serializers.ModelSerializer):
    class Meta:
        model = Category
        fields = "__all__"

class BrandSerializer(serializers.ModelSerializer):
    class Meta:
        model = Brand
        fields = "__all__"

class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = "__all__"
```

 다음으로, 뷰셋(viewsets)을 살펴보고 라우터(router)를 정리하면 된다.     
 정리 후에는 데이터베이스에서 데이터를 수집하고 프론트엔드로 반환할 수 있게 된다.



## Build: Define the Category Viewset

[DRF BeiwSets](https://www.django-rest-framework.org/api-guide/viewsets/)

공문을 보면 ViewSwet을 생성하는 예시가 나와있다.    

ViewSet은 View의 실제 로직이 처리되는 곳으로, 클라이언트로부터의 요청을 처리하고 데이터베이스에서 데이터를 수집한 후 클라이언트에게 반환하는 역할을 한다.     

views.py
```python
from django.shortcuts import render
from rest_framework import viewsets
from rest_framework.response import Response

from .models import Category
from .seializers import CategorySerializer

# Create your views here.

class CategoryView(viewsets.ViewSet):

    '''
    A simple Viewset for viewing all categories
    '''

    queryset = Category.objects.all()

    def list(self, request):
        serializer = CategorySerializer(self.queryset, many=True)
        return Response(serializer.data)
```


`many=True`는 `CafeSerializer`가 다수의 객체를 직렬화(serialize)해야 함을 나타내는 매개변수다.     
`CafeSerializer`는 `Cafe` 모델의 단일 객체를 직렬화할 수 있지만, `many=True`를 설정하면 다수의 `Cafe` 객체를 직렬화할 수 있도록 해준다.     
즉, `queryset`에 포함된 모든 `Cafe` 객체를 직렬화하고, 해당 데이터를 리스트 형태로 반환합니다. 이를 통해 다수의 `Cafe` 객체를 한 번에 처리할 수 있다.      



데이터를 반환해야하므로 list 함수를 사용하여 데이터를 반환하는 방법을 정의했다.     
데이터를 직렬화하기 위해 Serializer를 정의하고, Serializer에는 self와 queryset을 전달한다.     
queryset은 우리가 데이터베이스에서 수집한 데이터다.     

마지막으로, 직렬화된 데이터를 응답으로 보내기 위해 rest_framework의 response 모듈에서 Response를 가져오는 것으로 코드가 마무리된다.     

이제 직렬화된 데이터를 클라이언트로 반환하고 있다.     

요청을 받아 처리하고 데이터를 직렬화하여 프론트엔드 또는 클라이언트에서 사용할 수 있도록 준비하는 과정이다.


## Build: Implementing Django DRF Router

> 라우팅은 웹 어플리케이션에서 들어오는 요청을 해당하는 핸들러 또는 컨트롤러로 연결하는 프로세스다.
> 클라이이언트로부터의 요청을 받으면 해당 요청이 어떤 핸들러 또는 컨트롤러에서 처리되어야 하는지를 결정하기 위해 라우팅을 사용한다.

- 라우팅은 URL 패턴과 요청 매서드 (GET,POST,PUT,DELETE 등)을 기반으로 요청을 적절한 핸들러 또는 컨트롤러로 매핑한다.
- 이렇게 하면 요청이 들어올 때 해당 요청을 처리할 수 있는 적절한 코드 부분으로 전달된다.
- 라우팅은 웹 어플리케이션의 기능과 동작을 결정하는 중요한 부분이며, 어플리케이션의 URL 구조와 요청 처리 방식을 정의하는데 사용된다.


DRF 프레임 워크에서는 관련된 뷰들의 로직을 단일 클래스인 ViewSet으로 결합할 수 있다.    
다른 프레임워크에서는 이와 개념적으로 유사한 '리소스(Resources)'s나 컨트롤러(Controllers) 와 같은 구현체를 찾을 수도 있다.     

ViewSet 클래스는 .get() 혹은 .post()와 같은 메서드 핸들러를 제공하지 않고, 대신 .list() 나 .create() 와 같은 액션을 제공하는 클래스 기반 뷰의 한 유형이다.

ViewSet의 메서드 핸들러들은 .as_view() 메서드를 사용하여 뷰의 최종화 시점에 해당 액션들에 연결된다.

일반적으로 뷰셋(viewset)의 뷰를 명시적으로 urlconf에 등록하는 대신, 자동으로 urlconf를 결정해주는 라우터 클래스에 뷰셋을 등록한다.

(root) urls.py
```python
from django.contrib import admin
from django.urls import include, path
from rest_framework.routers import DefaultRouter

from product import views

router = DefaultRouter()
router.register(r"category", views.CategoryView)

urlpatterns = [

    path('admin/', admin.site.urls),
    path('api/', include(router.urls))

]
```

여기서 CategoryView 가 뷰셋의 역할을 하는데 여기서 두 가지의 장점이 있다.    
1. 반복되는 로직을 단일 클래스로 결합할 수 있다.
2. 쿼리셋을 한 번만 지정하면 여러 뷰에서 사용 가능하다.
3. 라우터를 사용함으로써 URL conf를 직접 다룰 필요가 없어진다.
4. 일반적인 뷰와 URL conf를 사용하는 것은 명시적이며 더 많은 제어력이 제공된다.
5. ViewSet은 빠르게 시작하거나 큰 API에서 URL 구성을 일관되게 적용하고 싶을 때 유용하다.

위에서 새로운 라우터를 생성했고 이제 카테코리 뷰 셋 내의 정보를 활용해서 엔드포인트를 구축한다.    
여기서 볼 수 있듯이, 이제 API를 통해 접근할 수 있고, 생성된 모든 라우터 URL이 포함될 것이다.


## Build: API Documentation with DRF-spectacular Schema Generation and Swagger

[drf-spectacular.](https://drf-spectacular.readthedocs.io/en/latest/)

DRF spectacular 는 API에 대한 모든 정보를 모아주고 해당 정보를 프론트엔드 패키지인 Swaggy UI 와 같은 형식으로 제공하여 다른 개발자나 API와 상호 작용하려는 사용자에게 정보를 제공해준다.      

DRF-Spectacular은 Django REST framework(DRF)를 위한 강력한 API 문서화 도구다. DRF-Spectacular은 OpenAPI(이전의 Swagger) 스펙을 사용하여 API 스키마를 자동으로 생성하고, DRF의 기능과 설정을 자동으로 탐지하여 문서화한다.     

DRF-Spectacular의 주요 기능과 장점은 다음과 같다:     

1. 자동 스키마 생성: DRF-Spectacular은 DRF의 serializers, viewsets, routers 등을 스캔하여 자동으로 API 스키마를 생성한다. 이를 통해 개발자는 수동으로 API 스키마를 작성하지 않고도 빠르고 정확한 문서를 생성할 수 있다.     
    
2. 상세한 문서화: DRF-Spectacular은 API 엔드포인트, 요청 및 응답 객체, 인증 및 권한 설정 등에 대한 상세한 문서를 제공한다. 개발자와 API 사용자는 각 엔드포인트의 기능, 매개변수, 응답 형식 등을 명확히 이해할 수 있다.     
    
3. 커스터마이즈 가능: DRF-Spectacular은 다양한 커스터마이즈 옵션을 제공하여 문서화된 API의 외형과 동작을 조정할 수 있다. 테마 설정, 필드 숨기기, 태그 추가 등을 통해 문서화를 개발자의 요구에 맞게 조정할 수 있다.     
    
4. 인터랙티브한 UI: DRF-Spectacular은 기본적으로 Swagger UI와 ReDoc를 지원하여 API 문서를 시각적으로 보기 쉽게 제공한다. 이를 통해 API 사용자는 엔드포인트를 테스트하고 예상되는 응답을 확인할 수 있다.     
    

DRF-Spectacular은 DRF 프로젝트에 간단하게 통합되며, 설정이 간단하고 사용자 친화적이다. 따라서 DRF 기반의 프로젝트에서 API 문서화에 대한 요구 사항이 있는 경우 DRF-Spectacular을 고려할 수 있다.      

```
pip install drf-spectacular
```

```python
settings.py

INSTALLED_APPS = [
	....
    "drf_spectacular",
    ...
]
REST_FRAMEWORK = {
    'DEFAULT_SCHEMA_CLASS': 'drf_spectacular.openapi.AutoSchema',
}

SPECTACULAR_SETTINGS = {
    "TITLE": "Django DRF Ecommerce",
}
```

```python
python manage.py spectacular --file schema.yml
```

설정을 마치고 터미널에서 위의 명령문을 실행시켜주면 파일이 생성된다.     

```python
from django.contrib import admin
from django.urls import include, path
from drf_spectacular.views import SpectacularAPIView, SpectacularSwaggerView
from rest_framework.routers import DefaultRouter
from product import views

router = DefaultRouter()
router.register(r"category", views.CategoryView)

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include(router.urls)),
    path('api/schema/', SpectacularAPIView.as_view(), name="schema"),
    path("api/schema/docs/", SpectacularSwaggerView.as_view(url_name="schema")),

]
```

`SpectacularAPIView, SpectacularSwaggerView` 2개의 패키지를 불러온 후 아래와 같이 url 을 설정해주면 schema에 접속하면 이전에 로컬로 만든 데이터가 다운로드되고     
docs 까지 치면 웹에서 데이터를 확인할 수 있다.      

이 때 시리얼라이저를 설정하지 않아서 오류가 나지만 우선 아래와 같이 작동하는 부분은 확인이 가능하다.    

![](https://i.imgur.com/7Z8lWo7.png)

### 추가작업
`unable to guess serializer` 오류가 나는 이유는 어떤 시리얼 라이저를 사용하는 지에 대한 여부를 확인 할 수 없어서다.     

view set을 사용할 때 적어도 어떤 시리얼라이저를 사용할지 지정해줘야 한다.     
그렇지 않으면 Seialize와 DRF Spectacular가 데이터를 모을 때 이전에 설정했던 list 함수에서 어떤 serialize를 사용하는지 알 수 없다.     
이 때 `extend_schema` 패키지를 이용해서 엔드포인트의 동작과 관련된 많은 세부 정보를 문서로 기술할 수 있다.

### `@extend_schema`
1. 요청(Request) 및 응답(Response)에 대한 설명: `@extend_schema`를 사용하여 특정 API 엔드포인트의 요청과 응답의 구조, 필드, 형식 등을 문서화할 수 있다.
2. 쿼리 매개변수(Query Parameters)에 대한 문서화: 엔드포인트에 사용되는 필터링, 정렬, 페이지네이션 등의 쿼리 매개변수에 대한 문서화를 추가로 제공할 수 있다.
3. 예시 요청과 응답의 정의: `@extend_schema`를 사용하여 API 엔드포인트의 예시 요청과 응답을 제공하고, 실제 데이터가 어떻게 보일지 예시를 통해 보여줄 수 있다.
4. 세부 정보 추가: API 엔드포인트에 대한 자세한 설명, 사용 예제, 비즈니스 로직 설명 등과 같은 세부 정보를 추가할 수 있다.

이러한 방식으로 `@extend_schema`를 사용하면 API 문서를 보다 상세하게 작성하고, API 사용자에게 더 친화적으로 제공할 수 있다.

```python
product/views.py

from drf_spectacular.utils import extend_schema
class CategoryView(viewsets.ViewSet):

    '''
    A simple Viewset for viewing all categories

    '''

    queryset = Category.objects.all()
    @extend_schema(responses=CategorySerializer)
    def list(self, request):
        serializer = CategorySerializer(self.queryset, many=True)
        return Response(serializer.data)
```


![](https://i.imgur.com/Jz0v201.png)


### Serialize
- `Serialize`은 Django REST Framework(DRF)의 일부로서, 데이터 직렬화를 위한 클래스를 제공한다.
- DRF에서 사용되는 Serializer 클래스는 Django 모델 인스턴스나 쿼리셋과 같은 데이터를 JSON이나 다른 형식으로 변환할 수 있도록 도와준다.
- Serializer는 데이터 유효성 검사, 모델 인스턴스 생성 및 업데이트 등의 기능도 지원한다.
- `Serialize`는 DRF의 주요 구성 요소 중 하나이며, RESTful API 개발에서 데이터 직렬화 및 역직렬화를 수행하는 데 사용된다.

### DRF Spectacular
 - `DRF Spectacular`은 Django REST Framework(DRF)용 자동 API 문서 생성 도구다.
 - Swagger 및 OpenAPI 사양을 기반으로 하며, DRF 기반의 API를 자동으로 문서화하여 API 개발자가 API의 사용법과 엔드포인트를 쉽게 이해하고 사용할 수 있도록 도와준다.
 - `DRF Spectacular`은 DRF의 기본 Serializer 클래스를 사용하여 API 스키마를 생성하므로, Serializer와 밀접한 관련이 있다.
 - `DRF Spectacular`은 DRF의 다양한 기능을 지원하며, API 문서에 주석, 예시 요청 및 응답, 필터링 및 정렬 매개변수 등을 포함시킬 수 있다.

요약하면, `Serialize`은 DRF의 일부로서 데이터 직렬화를 처리하는 데 사용되는 클래스를 제공하는 반면, `DRF Spectacular`은 DRF용 자동 API 문서 생성 도구로, API의 문서화를 간편하게 수행할 수 있도록 도와준다.


## Build: Implementing Brand, Product

![](https://i.imgur.com/smSY8U5.png)


Product 와 Brand의 관계를 다루기 위해 BrandSerializer를 사옹해야한다.     
이 필드는 실제로 외래 키(fk) 로 연결되는데, Product 데이터를 조회할 때 Brand의 테이블과 연결되는 데이터를 직렬화해야 하기 때문이다.     
Category 에 대해서도 동일하게 작업해준다.     


```python
serializers.py

from rest_framework import serializers
from .models import Brand, Category, Product

class CategorySerializer(serializers.ModelSerializer):
    class Meta:

        model = Category
        fields = "__all__"

class BrandSerializer(serializers.ModelSerializer):
    class Meta:

        model = Brand
        fields = "__all__"

class ProductSerializer(serializers.ModelSerializer):

    brand = BrandSerializer()
    category = CategorySerializer()

    class Meta:

        model = Product

        fields = "__all__"

```

![](https://i.imgur.com/U5qJee1.png)

![](https://i.imgur.com/y7TB4ku.png)

해당 데이터에서

![](https://i.imgur.com/kVV04PN.png)

이런식으로 컨트롤이 가능하다.

![](https://i.imgur.com/QoLdrjO.png)

## Testing : Coverage HTML Reporting

파이썬 패캐지 coverage는 코드의 테스트 커버리지(테스트가 얼마나 많은 코드를 실행하는지)를 측정하는 도구다.
코드 커버리지는 소프트웨어의 품질과 안정성을 평가하는 데 도움이 되며, 코드가 테스트 되었는지를 확인하는 데 사용된다.     

coverage 패키지는 어떤 부분이 테스트 되지 않았는지를 확인하고 추가적인 테스트 케이스를 작성해 코드 커버리지를 높일 수 있다.    

테스트 코드가 얼마나 충족되었는가를 쉽게 알 수 있게 해준다.     
또한 coverage 패키지는 단위 테스트 프레임워크와 함께 사용될 때 가장 효과적이다.     
대표적인 단위 테스트 프레임워크로는 unittest, pytest, nose 등이 있다.     
이러한 프레임워크와 결합하여 코드 커버리지를 정하고 보고서를 작성할 수 있다.

[Python Coverage](https://coverage.readthedocs.io/en/7.2.7/)

```python 
pip install coverage
```

coverage 패키지는 어디에서 테스트가 필요한지 알려준다.

```python 
coverage run -m pytest
```

![](https://i.imgur.com/E0sYnun.png)

![](https://i.imgur.com/nJ3gD4P.png)

위와 같이 `.coverage` 라는 폴더가 생긴다.    
해당 파일은 테스트를 실행하는 동안 코드의 실행 여부를 추적하고, 각 라인과 브런치의 커버리지 정보를 저장한다.     
이 파일에는 테스트가 실행된 후에 프로그램 이 어떤 코드를 실행했는지에 대한 세부 정보가 포함되어 있다.     

폴더가 만들어 진후 아래의 명령어를 터미널에서 실행해주면  htmlcov 폴더가 만들어진다.    

```python 
coverage html
```


![](https://i.imgur.com/KiE9uXg.png)

```python
pip install pytest-cov
```

```python
pytest --cov
```


## Unit and End-to-End Testing

### Unit tests

유닛은 코드 한 줄, 함수, 메서드, 클래스 등이 될 수 있다.     
일반적으로 테스트하는 유닛이 작을 수록 좋다.    
유닛 테스트를 개발하기 위해서는 다른 어플리케이션으로부터 격리시켜야한다.     
다른 코드나 다른 유닛에 의존하는 경우,     
예를 들어 모듈화를 통해 유닛테스트를 하려고 하는 함수가 다른 리소스가 필요한 경우가 있을 수 있다.     
이러한 유닛 테스트를 구축하기 위해서 일종의 모의(mocking) 과정을 거친다.    

### End to End Test

End to End 테스트는 소프트 웨어의 흐름을 테스트한다.    
전체 소프트웨어를 처음부터 끝까지 테스트하는 것을 의미한다.     
또한 다른 외부 인터페이스나 자원과의 상호작용을 포함한다.      
예를 들어 API 테스트를 할 경우 쿼리등을 테스트하여 올바른 데이터가 반환되는지를 확인할 때 이러한 과정은 End to end 테스트로 간주될 수 있다.


## What to Test in Django

| Unit Tests                | End to End Test |
| ------------------------- | --------------- |
| Models (Methods/Managers) | API Endpoints   |
| URL Configurations        |                 |
| View / ViewSet            |                 |
| Serializer                |                 |

## Testing : Model factories

모델 테스트를 진행할 때 데이터가 필요하다.     
팩토리는 테스트에 사용할 실제 객체를 빠르고 쉽게 생성하는데 유용하다.

```python
pip install pytest-factoryboy
```

[파이테스트 - 팩토리보이](https://pypi.org/project/pytest-factoryboy/)

pytest-factoryboy는 Python의 테스트 프레임워크인 pytest와 함께 사용되는 라이브러리다.     


FactoryBoy는 테스트 데이터를 생성하는 데 사용되는 강력한 라이브러리로, 테스트 케이스에서 모델 인스턴스나 객체를 쉽게 생성하고 조작할 수 있다.     
일반적으로 테스트 데이터를 생성하고 초기화하는 과정은 번거로울 수 있지만, FactoryBoy는 이를 간단하게 처리할 수 있도록 도와준다.     

pytest-factoryboy는 pytest 테스트 함수에서 FactoryBoy 팩토리를 사용할 수 있도록 해주며, 테스트 데이터의 생성과 관련된 보일러플레이트 코드를 줄여준다.      
이를 통해 테스트 케이스를 작성하는 데 더욱 효율적이고 간결한 코드를 작성할 수 있다.     

따라서 pytest와 FactoryBoy를 함께 사용하면 테스트 데이터의 생성과 관리가 더욱 간편해지며, 테스트 코드를 더욱 깔끔하고 가독성 있게 작성할 수 있다.

![](https://i.imgur.com/Y1zOIwf.png)


test 폴더에 prouct 앱과 관련된 모든 테스트가 포함되며 init.py 파일을 생성해 둔다.     
파일명은 모듈 안에 있는 테스트를 식별하고 접근하기 쉽게 만드는게 좋다     

### 테스트 패턴
Arrange - Act - Assert

[테스트패턴 매우 자세한 설명](https://automationpanda.com/2020/07/07/arrange-act-assert-a-pattern-for-writing-good-tests/)

1. **_Arrange_** inputs and targets. _Arrange_ steps should set up the test case. Does the test require any objects or special settings? Does it need to prep a database? Does it need to log into a web app? Handle all of these operations at the start of the test.
2. **_Act_** on the target behavior. _Act_ steps should cover the main thing to be tested. This could be calling a function or method, calling a REST API, or interacting with a web page. Keep actions focused on the target behavior.
3. **_Assert_** expected outcomes. _Act_ steps should elicit some sort of response. _Assert_ steps verify the goodness or badness of that response. Sometimes, assertions are as simple as checking numeric or string values. Other times, they may require checking multiple facets of a system. Assertions will ultimately determine if the test passes or fails.

```python
test_models.py

import pytest
pytestmark = pytest.mark.django_db <- 데이터 베이스 접근

class TestCategoryModel:
    def test_str_method(self, category_factory):
        # Arrange
        # Act
        x = category_factory()
        # Assert
        assert x.__str__() == "test_category"

```


```python
factories.py

import factory
from product.models import Brand, Category, Product

class CategoryFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = Category
    name = "test_category"
```


```python
conftest.py

from pytest_factoryboy import register
from .factories import CategoryFactory

register(CategoryFactory)
-> 실제로는 category_factory로 접근된다.
```

![](https://i.imgur.com/8m3Q9qh.png)


![](https://i.imgur.com/ChOUElh.png)
이런식으로 모델을 테스트 코드를 짠다.

test_models.py
![](https://i.imgur.com/3nftEw5.png)

![](https://i.imgur.com/b2os4Nf.png)

## Test API End to End

test_endpoints.py
![](https://i.imgur.com/2VpVV9E.png)

```python
pytest -s
```
위 명령어를 찍으면 pass 나 fail 여부 뿐만 아니라 print에서 출력되는 부분을 모두 확인할 수 있다.
create_batch 는 Factory Boy 라이브러리에서 제공하는 기능 중 하나다.    
테스트 데이터를 생성하기 위한 도구로서 create_batch 는 지정된 팩토리를 사용해서 여러 개의 모델 인스턴스를 일괄적으로 생성하는 메서드다.    
category_factory.create_batch(4) 는 Category 객체를 데이터베이스 4개를 저장한다.     
이런 방식으로 테스트에서 실제 데이터에 대한 검증을 수행한다.
 

![](https://i.imgur.com/EBYMofd.png)

현재 name의 벨류 값이 하드코딩으로 test_category로 되어 있는데 이 부분을 바꿔주면 테스트를 진행하는데 있어서 용이하다.


```python
import factory
from product.models import Brand, Category, Product

class CategoryFactory(factory.django.DjangoModelFactory):

    class Meta:

        model = Category
        
  # name = 'test_category' 밑에 처럼 사용하면 아래와 같은 값을 터머널에서 확인할 수 있다.
    name = factory.Sequence(lambda n: "Category_%d" % n)
```


![](https://i.imgur.com/vaSvup4.png)

### % 연산자

`%s`는 문자열, `%d`는 정수, `%f`는 부동소수점 숫자를 나타탠다.

```python
name = "John"
age = 25

message = "My name is %s and I am %d years old." % (name, age)
print(message)
```
