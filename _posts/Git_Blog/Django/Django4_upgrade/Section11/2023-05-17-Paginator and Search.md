---
layout: single
title: " [Django] Shopping Mall (6)"
categories: Django_ShoppingMall
tags:
  - Python
  - Paginator
  - Search
  - Django
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# 검색 및 페이지 수 구현

`페이지 네이션 구현`

{% raw %}

## 페이지 수 구현
- 장고는 정말 모든 게 준비되어있다. 그냥 본인이 찾아서 쏙쏙 뽑아먹으면 된다.
- [Django Paginator](https://docs.djangoproject.com/en/4.2/ref/paginator/) 공식문서에 자세히 나와있는데 참고하면 좋다.

#### views.py

```python
from django.shortcuts import render, get_object_or_404
from .models import Product, Category
from carts.models import CartItem
from carts.views import _cart_id
from django.http import HttpResponse
from django.core.paginator import EmptyPage, PageNotAnInteger, Paginator

# Create your views here.
def store(request, category_slug=None):
    categories = None
    products = None

    if category_slug != None:
        categories = get_object_or_404(Category, slug=category_slug)
        products = Product.objects.filter(category=categories, is_available=True)
        paginator = Paginator(products,9)
        page = request.GET.get('page')
        paged_products = paginator.get_page(page)
        product_count = products.count()

    else:
        products = Product.objects.all().filter(is_available=True)
        paginator = Paginator(products,9)
        page = request.GET.get('page')
        paged_products = paginator.get_page(page)
        product_count = products.count()

    context = {
        'products': paged_products,
        'product_count': product_count,
    }

    return render(request, 'store/store.html', context)
```

>- 기존의 products 객체를 Paginator 클래스에 상속해주고 파라미터로 몇 개의 상품을 보여줄 건지 정해준다.
>- 그다음 page를 받아서 객체 paginator에 상속받은 get_page()에 인수 page를 넣어준 객체를 기존 context의 `products` 의 벨류 값으로 새로 교체해준다.
>	- `page = request.GET.get('page')` 는 `http://127.0.0.1:8000/store/?page=1` 여기서 ?page=1 부분을 캡쳐한 객체다.
>- 또한 카테고리를 클릭해서 들어오거나 슬러그가 있는 상태에서도 같은 로직에 돌아갈 수 있도록 처리해주면 끝

#### store.html
```python
 <nav class="mt-4" aria-label="Page navigation sample">
	{% if products.has_other_pages %}
		<ul class="pagination">
		{% if products.has_previous %}
			<li class="page-item"><a class="page-link" href="?page={{products.previous_page_number}}">Previous</a></li>
		{% else %}
			<li class="page-item disabled"><a class="page-link" href="#">Previous</a></li>
		{% endif %}
			{% for i in products.paginator.page_range %}
			{% if products.number == i %}
				<li class="page-item active"><a class="page-link" href="#">{{i}}</a></li>
			{% else %}
				<li class="page-item"><a class="page-link" href="?page={{i}}">{{i}}</a></li>
			{% endif %}
			{% endfor %}
			{% if products.has_next %}
			<li class="page-item"><a class="page-link" href="?page={{products.next_page_number}}">Next</a></li>
			{% else %}
			<li class="page-item disabled"><a class="page-link" href="#">Next</a></li>
			{% endif %}
		</ul>
	{% endif %}
	  </nav>
```
>- 조건문은 공식문서를 참고
>- [참고](https://wikidocs.net/71240)
>-  [Django Paginator](https://docs.djangoproject.com/en/4.2/ref/paginator/) 
>- ![](https://i.imgur.com/L7s9if2.png)
>- ![](https://i.imgur.com/RnlnLug.png)
>- `page_range` 같은 경우는 `Paginator`클래스를 호출해서 쓰면 된다.
>- ![](https://i.imgur.com/ek13M2M.png)

#### 추천 상품 컨트롤

```python 
products = Product.objects.all().filter(is_available=True).order_by('id')
```
>- 해당 부분의 정렬을 컨트롤 해주면 된다.
>- 추천상품이나 광고 상품 같은걸 만들고 싶은경우 모델에서 새로운 객체를 만들고 개체별 점수를 매기고 상위 몇 개는 광고상품 나머지는 난수로 정렬하는 등 본인자유

### 버그수정

#### cart/views.py
```python
def cart(request, total=0, quantity=0, cart_items=None):
    try:
        tax=0 <--- !!!
        grand_total=0 <--- !!!
        cart = Cart.objects.get(cart_id=_cart_id(request))
        cart_items = CartItem.objects.filter(cart=cart, is_active=True)

        for cart_item in cart_items:
            total +=(cart_item.product.price * cart_item.quantity)
            quantity += (cart_item.quantity)

        tax = int(total * 0.1) # 부가가치세
        grand_total = tax + total
    except ObjectDoesNotExist:
        pass

    context = {
        'total': total,
        'quantity': quantity,
        'cart_items': cart_items,
        'tax' : tax,
        'grand_total': grand_total,
    }
    return render(request, 'store/cart.html', context)
```
>- 장바구니 같은 경우 `tax` 와 `grand_total` 부분이 로컬변수라서 오류가 났다.
>- 해당 부분만 다시 함수내에서 재정의 해주면 끝

## 검색 구현

### 기존 경로 문제 
- 검색 페이지를 만드려고 하는데 문제가 생겼다.
- 기존에 슬러그 때문에 url 주소 부분에서 오류가 남

```python
def search(request):
    return HttpResponse('Search page')
```

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.store, name='store'),
    path('<str:category_slug>/', views.store, name='products_by_category'),
    path('<str:category_slug>/<str:product_slug>/', views.product_detail, name='product_detail'),
    path('search/', views.search, name='search'),

]
```

-![](https://i.imgur.com/H1Be5DD.png)

- 위와 같은 오류가 났는데 search를 슬러그로 인식했고 해당 슬러그가 존재 하지 않기 때문에 오류가 난 것이다.
- `path('category/<str:category_slug>/', views.store, name='products_by_category'),` 이렇게 경로만 바꿔주면 간단하게 해결 가능
- ![](https://i.imgur.com/0DcpBsZ.png)


### html 페이지 연결시키기

#### navbar.html
```python
<div class="col-lg  col-md-6 col-sm-12 col">
      <form action="{% url 'search' %}" class="search" method="GET">
        <div class="input-group w-100">
            <input type="text" class="form-control" style="width:60%;" placeholder="Search" name ="keyword">
            <div class="input-group-append">
              <button class="btn btn-primary" type="submit">
                <i class="fa fa-search"></i>
              </button>
            </div>
          </div>
      </form> <!-- search-wrap .end// -->
    </div> <!-- col.// -->
```
>- input 태그의 name은 검색어를 받는다.
>- 검색창에 jeans 라고 입력하면 아래와 같은 주소창이 형성된다.
>	- `http://127.0.0.1:8000/store/search/?keyword=jeans` 
>	- 이 방식은 어떤 웹사이트를 가도 마찬가지고 이 원리를 이용해서 할 수 있는게 많다
>	- 대표적인게 API 같은 것들이 있다.

#### views.py
```python
def search(request):
    if 'keyword' in request.GET:
        keyword = request.GET['keyword']
        if keyword:
            products = Product.objects.order_by('-created_date')
            .filter(
	            Q(description__icontains=keyword) | Q(product_name__icontains=keyword))
            product_count = products.count()

    context = {
        'products': products,
        'product_count': product_count,
    }
    return render(request, 'store/store.html', context)

```
>- 정렬 키워드 ('-created_date') 앞에 있는 - 는 최근순으로 나열된다 
>	- 오름차순으로 하고 싶다면 -를 뺴고 ('created_date') 만 입력하면 된다.
>- Q 는 장고 model orm 으로 where 절에 or 문을 추가하고 싶을 때 사용한다.
>- __icontains 는 포함하는 문자열을 찾는다 (대소문자 안가림)
>- [장고쿼리셋](https://gaussian37.github.io/python-django-django-query-set/)
>-[자세한 설명](https://velog.io/@may_soouu/%EC%9E%A5%EA%B3%A0-Q%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0)
>[더 자세한 설명 Q in Django](https://docs.djangoproject.com/en/4.2/topics/db/queries/)



{% endraw %}
