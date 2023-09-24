---
layout: single
title: " [Django] Shopping Mall (5)"
categories: Django_ShoppingMall
tags:
  - Python
  - Django
  - Project_ShoppingMall
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# 장바구니 만들기

{% raw %}

- 장바구니를 만들기 위해서는 세션을 만들어줘야한다.
- 장바구니 수량 +, - 구현 (ajax이 아닌 이번에는 파이썬으로 구현!)

## 세션만들기
- Cart 앱에서 장바구니 구현을 위해 모델을 만들어준다.

### models.py
```python
from django.db import models
from store.models import Product

# Create your models here.

class Cart(models.Model):
    cart_id = models.CharField(max_length=250, blank=True)
    date_added = models.DateField(auto_now_add=True)

    def __str__(self):
        return self.cart_id

class CartItem(models.Model):
    product = models.ForeignKey(Product, on_delete=models.CASCADE)
    cart    = models.ForeignKey(Cart, on_delete=models.CASCADE)
    quantity = models.IntegerField()
    is_active = models.BooleanField(default=True)

    def sub_total(self):
        return self.product.price * self.quantity

    def __str__(self):
        return self.product
```
>- 클래스 Cart
>	- 세션키를 cart_id로 쓸거다.
>	- 세션이 생성된 날짜를 카운트다운 및 자동소멸을 할 예정이므로 날짜도 기록해준다.


>- 클래스 CartItem
>	- 장바구니에서 총 합계 구현을 위해 `sub_total` 함수를 만들어준다
>	-  Product와 Cart의 테이블을 연결시키키는 객체 `product` 와 `cart` 를 만들어준다.
>	- `Cart` 혹은 `Product` 가 삭제되면 장바구니에 있을 수 없으므로 CASCADE를 구현해준다.

## 장바구니 +, - 구현하기

### views.py
```python
from django.shortcuts import render
from .models import Cart, CartItem

def _cart_id(request):
    cart = request.session.session_key
    if not cart:
        cart = request.session.create()
    return cart

def add_cart(request, product_id):
    product = Product.objects.get(id=product_id) #get the product
    try:
        cart = Cart.objects.get(cart_id=_cart_id(request)) # get the cart using the cart_id present in the session
    except Cart.DoesNotExist:
        cart = Cart.objects.create(
            cart_id = _cart_id(request)
        )
    cart.save()
    try:
        cart_item = CartItem.objects.get(product=product, cart=cart)
        cart_item.quantity += 1 # cart_item.quantity = cart_item.quantity + 1
        cart_item.save()
    except CartItem.DoesNotExist:
        cart_item = CartItem.objects.create(
            product = product,
            quantity = 1,
            cart = cart,
        )
        cart_item.save()
    # return HttpResponse(cart_item.product)
    # exit()
    return redirect('cart')

def cart(request, total=0, quantity=0, cart_items=None):
    try:
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
>- `_cart_id` 를 통해 세션아이디를 만들어준다.
>	- 세션아이디가 없다면 세션을 만들어주도록 조건문도 만들어준다.
>- `add_cart` 가 호출되었을 때 우선 Product에 일치하는 상품을 찾아 객체 product로 만든다. 그 후 세션키가 기존에 있는 게 있으면 그대로 사용하고 없으면 새로 만들어서 저장한다.
>  해당 함수가 호출 되었을 때는 수량이 1개씩 증가함으로 +=1을 해주고 저장한다.
>  넘어간 product_id 값은 `add_cart`의 파라미터 `product_id`의 인수로 들어가고 안에있는 로직이 다 돌아간후 `cart` 를 리다이렉팅한다.

### cart.html
```python
<a href="{% url 'add_cart' cart_item.product.id %}" class="btn btn-light" type="button" id="button-minus"><i class="fa fa-plus"></i> </a>
```
>- `single_product` 의 데이터는 아래의 store>views.py 참고
>- url 에서 등록한 add_cart 가 눌리면 `cart_item.product.id` 가 int로 넘어간다.
>- 수량감소 혹은  삭제 모두 같은 로직으로 돌아간다.

### urls.py
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.cart, name='cart'),
    path('add_cart/<int:product_id>/', views.add_cart, name='add_cart'),
    path('remove_cart/<int:product_id>/', views.remove_cart, name='remove_cart'),
    path('remove_cart_item/<int:product_id>/', views.remove_cart_item, name='remove_cart_item'),

]
```

>- 템플릿에서 a 태그안에 있는  `href="{% url 'add_cart' single_product.id %}"` 는 urls.py 의 name이 add_cart로 연결되고 path에 있는대로 값을 전달한다.
### store>views.py
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


![](https://i.imgur.com/8PoQjPe.png)



srtore . views.py
```python

def product_detail(request, category_slug, product_slug):
    try:
        single_product = Product.objects.get(category__slug=category_slug, slug=product_slug)
        in_cart = CartItem.objects.filter(cart__cart_id=_cart_id(request), product=single_product).exists()
    except Exception as e:
        raise e
    context = {
        'single_product' : single_product,
    }
    return render(request, "store/product_detail.html", context)
```

## 다이나믹 장바구니 카운트

![](https://i.imgur.com/q4oO1BD.png)


- 장바구니에 담긴 아이템들을 카운트해서 표시한다.
- ajax의 벨류 값으로 받아서도 구현 가능하지만 장고에서 구현해보자

### 네비게이션바 -> 모든페이지
- 네비게이션바나 항상 어디서든 사용 가능하게 하려면 셋팅 TEMPLATES 에 등록해줘야 사용 가능하다.

```python
from .models import Cart, CartItem
from .views import _cart_id

def counter(request):
    cart_count = 0
    if 'admin' in request.path:
        return {}
    else:
        try:
            cart = Cart.objects.filter(cart_id=_cart_id(request))
            cart_items = CartItem.objects.all().filter(cart=cart[:1])
            for cart_item in cart_items:
                cart_count += cart_item.quantity
                
        except Cart.DoesNotExist:
            cart_count = 0
    return dict(cart_count=cart_count)
    
```
>- 장바구니에 아무것도 담겨있는게 없으면 0을 
>- 장바구니에 뭔가 담겨 있으면 반복문을 돌려서 중첩한 뒤 반환하게 만든다.

[if문 admin](https://stackoverflow.com/questions/71957631/what-is-if-admin-in-request-path)

![](https://i.imgur.com/J9yX8nC.png)

[그냥 이것 저것 찾아보다가 출처](https://yonghyunlee.gitlab.io/python/django-master-6/)

```html
<span class="badge badge-pill badge-danger notify">{{cart_count}}</span>
```
>- 구현할 곳에 값을 넣어주면 딕셔너리 키 값을 넣어주면 된다.



{% endraw %}
