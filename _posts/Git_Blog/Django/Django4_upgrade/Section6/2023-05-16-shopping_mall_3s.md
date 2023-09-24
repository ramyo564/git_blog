---
layout: single
title: " [Django] Shopping Mall (3)"
categories: Django_ShoppingMall
tags:
  - Python
  - 상품등록
  - Django
  - Project_ShoppingMall
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# 상품에 맞는 모델을 어떻게 만들까?

{% raw %}
## Products 모델 만들기
### models.py
```python
from django.db import models
from category.models import Category
from django.utils.text import slugify

# Create your models here.

class Product(models.Model):
    product_name    = models.CharField(max_length=200, unique=True)
    slug            = models.SlugField(max_length=200, unique=True, allow_unicode=True)
    description     = models.TextField(max_length=500, blank=True)
    price           = models.IntegerField()
    images          = models.ImageField(upload_to='photos/products')
    stock           = models.IntegerField()
    is_available    = models.BooleanField(default=True)
    category        = models.ForeignKey(Category, on_delete=models.CASCADE)
    created_date    = models.DateTimeField(auto_now_add=True)
    modified_date   = models.DateTimeField(auto_now=True)

    def __str__(self):
        return self.product_name

    def save(self, force_insert=False, force_update=False, using=None, update_fields=None):
        self.slug = slugify(self.product_name, allow_unicode=True)
        super().save(force_insert, force_update, using, update_fields)
```
>- slug 자동완성 기능을 쓰려면 파라미터에 allow_unicode=True 로 해둬도 가능하지만 그럴 경우 urls 에서 다시 한 번 작업해줘야 한다.
>	- model 의 save함수를 오버라이딩하면 모델에서 저장될 때마다 slug fields 값을 추가하도록 할 수 있다.
>- `images` ->카테고리 모델에서 이미지필드의 upload 장소를 베이스를 photos 로 해주었으니 동일하게 맞춰준다.

### admin.py
```python
from django.contrib import admin
from .models import Product

# Register your models here.

class ProductAdmin(admin.ModelAdmin):
    list_display = ('product_name', 'price', 'stock', 'category', 'modified_date', 'is_available')
    prepopulated_fields = {'slug': ('product_name',)}

admin.site.register(Product, ProductAdmin)
```

![](https://i.imgur.com/Yujqgwg.png)


## 렌더링해주기

- 이제 커스텀한 모델을 html에 반복문으로 보내주면 끝이다.

```python
from django.shortcuts import render
from store.models import Product

def home(request):
    products = Product.objects.all().filter(is_available = True)
    context = {
        'products': products,
    }
    return render(request, 'home.html',context)
```

```html
<div class="row">
    {% for product in products %}
    <div class="col-md-3">
        <div class="card card-product-grid">
            <a href="./product-detail.html" class="img-wrap"> <img src="{{product.images.url}}"> </a>
            <figcaption class="info-wrap">
                <a href="./product-detail.html" class="title">{{ product.product_name }}</a>
                <div class="price mt-1">{{ product.price }}</div> <!-- price-wrap.// -->
            </figcaption>
        </div>
    </div>
    {% endfor %}
</div> <!-- row.// -->
```

![](https://i.imgur.com/50Q6CBl.png)

## 숫자 포맷 적용시키기

- 장고에는 숫자나 날씨 포맷이 이미 내장되어있다.
### settings.py
```python
INSTALLED_APPS = [
    'django.contrib.humanize', # 숫자 포멧
]
```
>- 따로 라이브러리 설치 필요 없이 그냥 `humanize` 를 앱에 등록해준다.



```python
{% load humanize %}

{{ product.price|intcomma}}
```


>- 그 후 static 폴더처럼 호출한 뒤 태그를 씌워주면 적용된다.
>- [참고]([django.contrib.humanize | Django documentation | Django (djangoproject.com)](https://docs.djangoproject.com/en/4.2/ref/contrib/humanize/))



## 페이지 연결시키기
- 메인화면이 아닌 store 페이지를 따로 만들고 거기서 카테고리 별로 나눈 페이지를 만들려고 했는데 그 이유는 페북이나 구글 스크립트를 심어 놨을 때 세분화 할 수록 추적할 때도 좋고 인사이트를 얻기도 좋다
- 두 번째로는 사용자가 원하는 상품을 찾기 쉽게 하기 위해서다.
### urls.py
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.store, name='store'),
]
```

### views.py
```python
from .models import Product

def store(request):
    products = Product.objects.all().filter(is_available = True)
    product_count = products.count()
    context = {
        'products': products,
        'product_count' : product_count,
    }
    return render(request, 'store/store.html',context)
```

>- 모델 Product에 있는 모든 객체들을 갖고 온 후 객체 products에 담아둔다.
>- .count() 함수를 써서 총 몇 개인지 알 수 있는 객체를 만들고 context로 보내준다.
>- html 은 이전과 동일하게 반복문으로 돌려준다.




## 카테고리 별로 페이지 출력하게 만들기 (slug)

- 왼쪽에 카테고리들은 현재 데모로 구현되어있다. 이 카테고리들 중 하나를 클릭하면 해당 카테고리에 맞는 아이템들만 반복문으로 출력하게 만들고 싶었다.
	- 이를 구현하기 위해서 slug를 사용하면 편하다.
### urls.py
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.store, name='store'),
    path('<slug:category_slug>/', views.store, name='products_by_category'),
]
```
>- `/store/slug`의 형태로 주소를 받는다.
>- slug 주소를 views에서 받은 후 유효성 검사를 진행한다.

### views.py
```python
from django.shortcuts import render, get_object_or_404
from .models import Product, Category

# Create your views here.
def store(request, category_slug=None):
    categories = None
    products = None

    if category_slug != None:
        categories = get_object_or_404(Category, slug=category_slug)
        products = Product.objects.filter(category=categories, is_available=True)
        product_count = products.count()

    else:
        products = Product.objects.all().filter(is_available=True)
        product_count = products.count()
        
    context = {
        'products': products,
        'product_count': product_count,
    }
    return render(request, 'store/store.html', context)
```
>- 우선 처음에 파라미터 category_slug 값이 없을 수도 있으니 argument 값으로 None을 주는게 좋다.
>- 이렇게 하면 슬러그 주소가 없을 경우 기존에 랜더링 했던 것과 동일하게 출력을 한다.
>- 만약 매개변수 값이 있다면 `get_object_or_404` 를 통해 Category 에서 슬러그 주소와 일치하는 값이 있다면 객체 categories로 보내고 없으면 404 오류를 만든다.
>- categories 값이 만들어 졌다면 필터를 거쳐 products 객체를 만든후 context로 보내주면 된다.


{% endraw %}