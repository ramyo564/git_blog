---
layout: single
title: " [Django] Shopping Mall (4)"
categories: Django_ShoppingMall
tags:
  - Python
  - context_processors
  - Django
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# 상품페이지 만들기

{% raw %}

## 경로설정할 때 자주 쓰는 함수를 어떻게 하면 모든 페이지에서 편하게 사용할까?

- 예를 들어서 본인은 네비게이션 바는 웬만하면 상단에 계속적으로 고정되어 있기 때문에 카테고리 목록을 불러오는 함수를 만들어서 모든 페이지에서 사용 가능하겠금 만들려고 했다.

### context_processors.py
```python
from .models import Category

def menu_links(request):
    links = Category.objects.all()

    return dict(links=links)
```
>- context_processors.py 파일을 만든 후 여기 안에 카테고리 목록을 다 불러온 후 딕셔너리로 리턴하는 기능을 만든다.


### settings.py
```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': ['templates'],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',

                'category.context_processors.menu_links',

            ],
        },
    },
]
```
>- 그후 settings.py 에서 해당 기능을 등록해주면 끝

### navbar.html
- 카테고리 목록을 클릭하면 등록한 카테고리 이름만 필요한 상황이니 모델로 만든 Category 클래스에서 이름 값으로 저장된 변수 category_name를 오버라이딩한다.
- 그리고 클릭했을 때 해당 카테고리 페이지 (아까 슬러그 연결했던) 곳으로 갈 수 있게 get_url 이라는 함수도 만들어서 오버라이딩 할 수 있게 만들어준다.

```html
<div class="dropdown-menu">
	{% for category in links %}
	<a class="dropdown-item" href="{{category.get_url}}">{{ category.category_name }}</a>
	{% endfor %}
</div>
```

### urls.py
```python
from django.urls import path
from . import views

urlpatterns = [
path('<slug:category_slug>/', views.store, name='products_by_category'),
]
```
>- 웹페이지 주소가 도메인/카테고리 슬러그 로 만들기 위해서 path 부분을 slug로 한다.
>- 근데 한글로 카테고리를 만들경우 오류가 나니 이런경우에는 slug 대신에 str을 사용하면 문제 없이 사용 가능하다.

### models.py
```python
class Category(models.Model):

    category_name = models.CharField(max_length=50, unique=True)
    slug = models.SlugField(max_length=100, unique=True, allow_unicode=True)
.
.
.
.
    def get_url(self):
            return reverse('products_by_category', args=[self.slug])
```
>- 네비게이션 바에서 카테고리를 클릭하면 해당 슬러그 주소로 들어갈 수 있도록 `get_url` 함수를 작성해준다. 
>- reverse를 통해 args 값으로 슬러그를 갖고 가면 된다.
>- [reverse](https://ugaemi.com/django/Django-reverse-and-resolve/)


>- 클릭하면 해당 페이지로 들어가는데 사실은 해당 슬러그 주소를 갖고 있는 아이템들만 보여지는 로직이다.


## 상품페이지 연결하기

- 카테고리와 같은 논리로 상품의 슬러그를 이용해 URL 고유값을 만들어 연결시키기

### views.py
```python
def product_detail(request, category_slug, product_slug):
    try:
        single_product = Product.objects.get(category__slug=category_slug, slug=product_slug)
    except Exception as e:
        raise e
    context = {
        'single_product' : single_product,
    }
    return render(request, "store/product_detail.html", context)
```
>- 상품을 클릭하면 상세페이지로 가는 템플릿에 매개변수로 슬러그 값을 갖고 간다.
>- `category__slug` 여기서 `__`는 모델의 slug에 바로 접근하는 문법이다.

### urls.py
```python
from django.urls import path
from . import views

urlpatterns = [
    path('<slug:category_slug>/<slug:product_slug>/', views.product_detail, name='product_detail'),
]
```
>- 상품 이름을 한글로 할꺼면 slug 대신 str을 써주면 된다.
>- product_detail.html 에서 `single_product` 값으로 템플릿에 맞게 불러오면 된다.



## 상품 품절 로직 만들기

```html
{% if single_product.stock <= 0 %}
<h5>Out of Stock</h5>
{% else %}
<a href="./product-detail.html" class="btn  btn-primary"> <span class="text">Add to cart</span> <i class="fas fa-shopping-cart"></i>  </a>
{% endif %}
```
>- 로그인, 로그아웃을 구현할 때와 동일하게 조건문으로 구현 가능하다.


![](https://i.imgur.com/uSRj7sW.png)






{% endraw %}
