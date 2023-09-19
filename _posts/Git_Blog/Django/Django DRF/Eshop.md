---

layout: single
title: " [Django DRF] eshop project (1) "
categories: Django
tag: [Python,"[Django DRF] Project cafe project RESTful API",]
toc: true
toc_sticky: true
author_profile: false
sidebar:

---

# Eshop - DRF
  
안 적어 놓으면 또 까먹고 공문 뒤져보고 하는 데 뒤져보는 걸 최소화 하기 위해서 그 동안 배운걸 응용해서 간단하게 기록용으로 적었다.    

## 필터 라입브러리 및 페이지 네이터 구현

```python
pip install django-filter
```

views.py
```python
from django.shortcuts import get_object_or_404

from rest_framework.decorators import api_view

from rest_framework.pagination import PageNumberPagination

from rest_framework.response import Response

  

from .filters import ProductsFilter

from .models import Product

from .serializers import ProductSerializer

  

# Create your views here.

  

@api_view(['GET'])

def get_products(request):

  

    filterset = ProductsFilter(request.GET, queryset=Product.objects.all().order_by('id'))

  

    count = filterset.qs.count()

  

    # Pagination

    resPerPage = 1

  

    paginator = PageNumberPagination()

    paginator.page_size = resPerPage

  

    queryset = paginator.paginate_queryset(filterset.qs, request)

  
  

    serializer = ProductSerializer(queryset, many=True)

  

    return Response({

        "count": count,

        "resPerPage": resPerPage,

        "products": serializer.data

         })

  
  

@api_view(['GET'])

def get_product(request, pk):

  

    product = get_object_or_404(Product, id=pk)

  

    serializer = ProductSerializer(product, many=False)

  

    return Response({ "product": serializer.data })
```


```python
from ast import keyword

  

from django_filters import rest_framework as filters

  

from .models import Product

  
  

class ProductsFilter(filters.FilterSet):

  

    keyword = filters.CharFilter(field_name="name", lookup_expr="icontains")

    min_price = filters.NumberFilter(field_name="price" or 0, lookup_expr="gte")

    max_price = filters.NumberFilter(field_name="price" or 1000000, lookup_expr="lte")

  

    class Meta:

        model = Product

        fields = ('keyword', 'category', 'brand', 'min_price', 'max_price')
```

django-filter 라이브러리를 통해 필터를 구현했다.     
우선 filters.py 파일을 만든 후 여기서 컨트롤할 사항을 만들어준다.     
사용법은 위와 같이 사용하는데 `lookup_expr` 는 필드에서 어떤 종류의 검색을 수행할지 지정하는 옵션이다.
`icontains` 는 대소문자를 구분하지 않고 문자열이 일부 포함되는지를 확인 , gte, lte 는 각각 greater than equal, less than equal 기능이다.

[장고필터 공문](https://django-filter.readthedocs.io/en/stable/)

페이지 네이터는 rest_framework에 있는 기능을 사용했다.

## Custom Exception Handling

[DRF EXCEPTIONS](https://www.django-rest-framework.org/api-guide/exceptions/)

error_views.py
```python
from django.http import JsonResponse

  
  

def handler404(request, exception):

    message = ('Route not found')

    response = JsonResponse(data={'error': message})

    response.status_code = 404

    return response

  
  

def handler500(request):

    message = ('Internal server error.')

    response = JsonResponse(data={'error': message})

    response.status_code = 500

    return response
```

custom_exception_handler.py
```python
  

from rest_framework.response import Response

from rest_framework import status

from rest_framework.views import exception_handler

from http import HTTPStatus

  

def custom_exception_handler(exc, context):

  

    response = exception_handler(exc, context)

  

    if response is not None:

  

        http_code_to_message = {v.value: v.description for v in HTTPStatus}

  

        error_payload = {

            "error": {

                "status_code": 0,

                "message": "",

                "details": []

            }

        }

  

        error = error_payload["error"]

        status_code = response.status_code

  

        error["status_code"] = status_code

        error["message"] = http_code_to_message[status_code]

        error['details'] = response.data

  

        response.data = error_payload

  

        return response

  

    else:

        error = {

            "error": "Something went wrong."

        }

  

        return Response(error, status=status.HTTP_500_INTERNAL_SERVER_ERROR)
```

settings.py
```python
REST_FRAMEWORK = {

    'EXCEPTION_HANDLER': 'utils.custom_exception_handler.custom_exception_handler'

}
```

## 이미지 관리

```python
pip install pillow
```

urls.py
```python
from django.urls import path

from . import views

  
  

urlpatterns = [

    path('products/', views.get_products, name="products"),

    path('products/upload_images/', views.upload_product_images, name="upload_product_images"),

    path('products/<str:pk>/', views.get_product, name="get_product_details"),

  

]
```

views.py
```python
@api_view(['POST'])

def upload_product_images(request):

  

    data=request.data

    files = request.FILES.getlist('images')

  

    images= []

    for f in files:

        image = ProductImages.objects.create(product=Product(data['product']), image=f)

        images.append(image)

  

    serializer = ProductImagesSerializer(images, many=True)

  

    return Response(serializer.data)
```


```python
class ProductImages(models.Model):

  

    product=models.ForeignKey(Product, on_delete=models.CASCADE, null=True, related_name="images")

    image=models.ImageField(upload_to="products")
```

AWS 방식으로 이미지 소스 url을 불러오는 방식이기 때문에  lsit로 주소들을 처리한다.

## Add Product Validations

views.py
```python
@api_view(['POST'])

def new_product(request):

  

    data= request.data

  

    serializer = ProductSerializer(data=data)

  

    if serializer.is_valid():

  

        product = Product.objects.create(**data)

  

        res = ProductSerializer(product, many=False)

  

        return Response({ "product": res.data })

  

    else:

        return Response(serializer.errors)
```

urls.py
```python
path('products/new/', views.new_product, name="new_product"),
```

serializers.py
```python
class ProductSerializer(serializers.ModelSerializer):

  

    images = ProductImagesSerializer(many=True, read_only=True)

  

    class Meta:

        model = Product

        fields = ('id', 'name', 'description', 'price', 'brand', 'ratings', 'category', 'stock', 'user', 'images')

  

        extra_kwargs = {

            "name": { "required": True, 'allow_blank':False },

            "description": { "required": True, 'allow_blank':False },

            "brand": { "required": True, 'allow_blank':False },

            "category": { "required": True, 'allow_blank':False },

        }
```

post 를 통해 data를 받을 때 위와 같이 에러처리를 할 수 있다.

## Update , Delete Product Details

views.py
```python
@api_view(['PUT'])

def update_product(request, pk):

    product = get_object_or_404(Product, id=pk)

  

    # Check if the user is same - todo

  

    product.name = request.data['name']

    product.description = request.data['description']

    product.price = request.data['price']

    product.category = request.data['category']

    product.brand = request.data['brand']

    product.ratings = request.data['ratings']

    product.stock = request.data['stock']

  

    product.save()

  

    serializer = ProductSerializer(product, many=False)

  

    return Response({ "product": serializer.data })

  
  

@api_view(['DELETE'])

def delete_product(request, pk):

    product = get_object_or_404(Product, id=pk)

  

    # Check if the user is same - todo

  

    args = { "product": pk }

    images = ProductImages.objects.filter(**args)

    for i in images:

        i.delete()

  

    product.delete()

  

    return Response({ 'details': 'Product is deleted' }, status=status.HTTP_200_OK)
```

urls.py
```python
    path('products/<str:pk>/update/', views.update_product, name="update_product"),
    path('products/<str:pk>/delete/', views.delete_product, name="delete_product")
```

models.py
```python
@receiver(post_delete, sender = ProductImages)

def auto_delete_file_on_delete(sender, instance, **kwargs):

    if instance.image:

        instance.image.delete(save=False)
```

`args = { "product": pk }` 는 product 키 값에 벨류가 pk 인 값에 해당되는 값을 모델에서 필터링한다.     
`@receiver(post_delete, sender = ProductImages)` 는 `ProductImages` 에서 객체가 삭제 될 때 post_delete 신호가 호출되는 함수다.     
`sender` 는 신호를 보내는 `ProductImages` 모델이다.     
`instance` 는 삭제되는 productImages 객체가 인자로 전달된다.    
그리고 if 문에서 instance에 image 파일이 있는지 검사한 후 삭제한다.