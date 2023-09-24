---
layout: single
title: " [Django] Shopping Mall (8)"
categories: Django_ShoppingMall
tags:
  - Python
  - Variation
  - Django
  - Project_ShoppingMall
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# Adding Variation in Cart Item

{% raw %}
#### cart/views.py
```python
def add_cart(request, product_id):
# 추가 수정 부분 ------------
    product = Product.objects.get(id=product_id) #get the product
    product_variation = []
    if request.method == 'POST':
        for item in request.POST:
            key = item
            value = request.POST[key]
            try:
                variation = Variation.objects.get(product=product, variation_category__iexact=key, variation_value__iexact=value)
                product_variation.append(variation)
            except:
                pass

####### 나머지는 동일
```
>- iexact 대소문자 정확히 일치하는지 확인
>	- value의 값이 대소문자가 정확히 일치 하지 않는다면 에러가 난다.
>	- [exact 예제](https://www.w3schools.com/django/ref_lookups_iexact.php)
>- admin에서 확인하기 위해서  `product_variation` 라는 빈 리스트를 만들어준다.
>	- 해당 리스트에 데이터가 존재하면 색이 칠해진다.

#### carts/models.py
```python
from store.models import Product, Variation

class CartItem(models.Model):

    product = models.ForeignKey(Product, on_delete=models.CASCADE)
    variations = models.ManyToManyField(Variation, blank=True) <- 추가
    cart    = models.ForeignKey(Cart, on_delete=models.CASCADE)
    quantity = models.IntegerField()
    is_active = models.BooleanField(default=True)

    def sub_total(self):
        return self.product.price * self.quantity
        
    def __unicode__(self):
        return self.product
```
>- `ManyToManyField` 다대다 관계
>	- 한 테이블의 여러 레코드가 다른 테이블의 여러 레코드와 연결되어 있는 관계
>	- 자동으로 두 테이블 사이의 관계를 관리해주는 중간 테이블을 생성한다고 되어 있다. 이 테이블은 우리가 작성한 모델에는 없지만, 데이터베이스를 확인하면 다대다 관계의 두 테이블 이름을 `_` 로 이어준 별도의 중간 테이블이 생성된 것을 볼 수 있다.
>	- 이 중간 테이블은 두 테이블의 id를 각각 필드로 가지고 있다.
>	- 그렇기 때문에 각 테이블에 데이터가 존재하지 않으면 중간 테이블에도 primary key가 존재하지 않는다.
>- [1. ManyToManyField](https://docs.djangoproject.com/en/4.2/ref/models/fields/#django.db.models.ManyToManyField)
>- [2. ManyToMany](https://himanmengit.github.io/django/2018/02/05/DjangoModels-04-ManyToMany.html)
>- [3. ManyToManyField](https://velog.io/@jiffydev/Django-9.-ManyToManyField-1)

## 어드민 페이지 커스텀

- 현재 어드민에서는 object 형태로 보이는데 이렇게 보면 뭔지 알 수가 없다.
- 해당 object를 보여주면서 장바구니 아이템이 어떤 형태로 담겼는지 보여주도록 커스텀을 해보자

![](https://i.imgur.com/jbhY3Bw.png)


```python
def add_cart(request, product_id):

    product = Product.objects.get(id=product_id) #get the product
    product_variation = []
    
    if request.method == 'POST':
        for item in request.POST:
            key = item
            value = request.POST[key]

            try:
                variation = Variation.objects.get(product=product, variation_category__iexact=key, variation_value__iexact=value)
                product_variation.append(variation)
            except:
                pass
          
    try:
        cart = Cart.objects.get(cart_id=_cart_id(request)) # get the cart using the cart_id present in the session
    except Cart.DoesNotExist:
        cart = Cart.objects.create(
            cart_id = _cart_id(request)
        )
    cart.save()
    try:
        cart_item = CartItem.objects.get(product=product, cart=cart)
        if len(product_variation) >0:
            for item in product_variation:
                cart_item.variations.add(item)
        cart_item.quantity += 1 # cart_item.quantity = cart_item.quantity + 1
        cart_item.save()
    except CartItem.DoesNotExist:
        cart_item = CartItem.objects.create(
            product = product,
            quantity = 1,
            cart = cart,
        )

        if len(product_variation) >0:
            for item in product_variation:
                cart_item.variations.add(item)
        cart_item.save()

    return redirect('cart')
```
>- len 으로 `product_variation` (장바구니에) 아이템이 존재한다면 `cart_item` 의 `variations` 에 해당 상품들을 넣어준다.

#### store/models.py
```python
class Variation(models.Model):
    product = models.ForeignKey(Product, on_delete=models.CASCADE)
    variation_category = models.CharField(max_length=100, choices=variation_category_choices)
    variation_value     = models.CharField(max_length=100)
    is_active           = models.BooleanField(default=True)
    created_date        = models.DateTimeField(auto_now=True)
    objects = VariationManager()

    def __str__(self):

        return self.variation_value
```
>- `unicode` 였던 부분을 `str` 로 바꿔주고 리턴을 기존의 self.product가 아닌 self.variation_value로 해주면 해결된다.
>- [unicode & str](https://stackoverflow.com/questions/18189874/why-unicode-doesnt-work-but-str-does)
>- 아래 사진과 같이 장바구니에 있으면서 해당되는 옵션들이 선택되어 보여진다.

![](https://i.imgur.com/J2yGl7K.png)


## 문제점 버그 발견

- 해당 코드로는 같은 상품이지만 옵션을 다르게 할 경우 카운트는 제대로 되지만 사이즈가 중첩되어 버린다 
- 어드민 페이지도 마찬가지다.

![](https://i.imgur.com/LAw8ijF.png)


![](https://i.imgur.com/iMgObNH.png)


- 장바구니에 담긴 후 만약 담겨있고 동일한 옵션을 갖고 있다면 수량을 올리고 옵션이 다르다면 다른 옵션으로 새롭게 하나가 생기도록 코드를 만들었다.
- 우선 옵션이 다를 경우 중첩이 안되게 만들어야하니 중첩을 없에도록 만들었다.

#### cart/views.py
```python
    try:
        cart_item = CartItem.objects.get(product=product, cart=cart)
        if len(product_variation) >0:
            cart_item.variations.clear() <--- 추가
            for item in product_variation:
                cart_item.variations.add(item)
        cart_item.quantity += 1 
        cart_item.save()
```
>- 만약 장바구니에 옵션이 존재하는 상품이 존재한다면 `variations` 부분을 초기화 해준다.

```python
    try: 
        cart_item = CartItem.objects.create(product=product, quantity=1, cart=cart)
        get -> create 로 변경, quantity 변경
        if len(product_variation) >0:
            cart_item.variations.clear()
            for item in product_variation:
                cart_item.variations.add(item)
        cart_item.save()
```
>- `add_cart` 가 호출될 때마다 옵션과 상관없이 누적되기 때문에 그냥 수량이 1개씩 늘어나게 만들었다.
>- 여기서 같은 옵션이 존재하는지를 먼저 감지하고 같은 옵션이 존재한다면 수량만 증가, 그렇지 않으면 새롭게 추가되도록 아래와 같이 if 문으로 수정했다.

```python
def add_cart(request, product_id):

    product = Product.objects.get(id=product_id) #get the product
    product_variation = []
    
    if request.method == 'POST':
        for item in request.POST:
            key = item
            value = request.POST[key]

            try:
                variation = Variation.objects.get(product=product, variation_category__iexact=key, variation_value__iexact=value)
                product_variation.append(variation)
            except:
                pass
          
    try:
        cart = Cart.objects.get(cart_id=_cart_id(request)) # get the cart using the cart_id present in the session
    except Cart.DoesNotExist:
        cart = Cart.objects.create(
            cart_id = _cart_id(request)
        )
    cart.save()




#########수정#########

	is_cart_item_exists = CartItem.objects.filter(product=product, cart=cart).exists()
    if is_cart_item_exists:
        cart_item = CartItem.objects.filter(product=product, cart=cart)

		ex_var_list = []
		id = []
		for item in cart_item:
			existing_variations = item.variations.all()
			ex_var_list.append(list(existing_variations))
			id.append(item.id)

		if product_variation in ex_var_list:
			# increase cart item quantity
			index = ex_var_list.index(product_variation)
			item_id = id[index]
			item = Cartitem.onjects.get(product=product, id=item_id)
			item.quantity += 1
			item.save()
			
		else: 
			#create a new cart item
			item = Cartitem.objects.create(product=product, quantity=1, cart=cart)
	        if len(product_variation) >0:
		        item.variations.clear()
	            for item in product_variation:
	                item.variations.add(*product_variation)

	        item.save()
	        
#########수정 끝 ########

    else:
        cart_item = CartItem.objects.create(
            product = product,
            quantity = 1,
            cart = cart,
        )

        if len(product_variation) >0:
			item.variations.clear()
            item.variations.add(*product_variation)
        cart_item.save()

    return redirect('cart')
```
>- existing_variations 은 database 에서
>- current variation 은 product_variation 에서
>- item_id 는 다시 database 에서 값을 갖고 온다.
>	- 만약 current variation 이 existing_variations 에 있다면 수량을 증가 시켜준다.

>- `is_cart_item_exists` 변수를 만들어서 우선 장바구니에 아이템이 존재하는지 검사한다.
>- 아이템이 존재한다면 존재하는 아이템을 `cart_item`에 담아준다.
>- `ex_var_list` 라는 빈 리스트를 만들어준다.
>- `cart_item` 에는 아이템이 몇 개가 있을지 모르니 반복문을 사용해준다.
>- 반복문을 통해 하나의 아이템 마다 `existing_variation`에 모든 옵션을 넣어서 아까 만들어 둔 `ex_var_list` 에 넣어준다 
>	- 이때 `existing_variation` 은 쿼리셋이니 리스트로 변환해줘야한다.
>	- `id` 도 마찬가지.

>- `ex_var_list` 에 아까 만들어 놓은 `product_variation` 가 있는지 검사를 하고 만약 존재한다면 `index` 변수를 만들고 해당 인덱스를 추출해서 변수에 넣어준다.
>	- 여기서 `ex_var_list` 는 `[[]]` 이런식으로 싸여있어서 맞는 인덱스를 찾을 수 있다.
>		- ![](https://i.imgur.com/tILHgay.png)
>	- 해당 index를 id에 주입해서 새로운 변수 item_id 를 만들어준다.
>	- 이제 장바구니에 있는 상품 아이디를 알아냈으니 수량만 변경해준다.
>- 만들어 놓은 `ex_var_list` 에 걸리는게 없으면 처음에 만들어 놓았던 로직을 활용해서 새로 등록해주면 끝


이제 똑같은 바지는 따로 잘 나오고 중복되는 상품은 알아서 쌓인다.

![](https://i.imgur.com/Dsy4feR.png)


## 버그 수정 두 번째
- 마이너스 버튼을 누르면 알아서 잘 삭제되지만 플러스 버튼을 누르면 옵션 정보를 갖고 오지 못해서 그냥 무옵션의 새로운 상품이 등록 되버린다.
- 해당 문제는 버튼을 클릭했을 때 넘어오는 값이 없기 때문인데 아를 위해서 html을 고쳐준다.

![](https://i.imgur.com/r6W31wM.png)


```python
<td>
	<!-- col.// -->
	<div class="col">
	<div class="input-group input-spinner">
		<div class="input-group-prepend">
			<a href="{% url 'remove_cart' cart_item.product.id cart_item.id %}" <-cart_item.id 추가
			class="btn btn-light" type="button" id="button-plus"> 
			<i class="fa fa-minus"></i> </a>
		</div>
			<input type="text" class="form-control"  value="{{ cart_item.quantity }}">
			<div class="input-group-append">
			
			
			<- form 형식으로 바꿔줌
			
			<form action="{% url 'add_cart' cart_item.product.id %}" method="POST">
			
			{% csrf_token %}
			{% for item in cart_item.variations.all  %}
			<input type="hidden" name="{{ item.variation_category | lower }}" 
				value="{{ item.variation_value | capfirst }}">
			{% endfor %}
	
			<button class="btn btn-light" type="submit" id="button-minus"> 
				<i class="fa fa-plus"></i> </button>

			</form>
			
			#<a href="{% url 'add_cart' cart_item.product.id %}" 
			#class="btn btn-light" type="button" id="button-minus"> 
			#<i class="fa fa-plus"></i> </a>
		</div>
	</div> <!-- input-group.// -->
	</div>
	<!-- col.// -->
</td>
```

### 마이너스 버튼
```python
def remove_cart(request, product_id, cart_item_id):
    cart = Cart.objects.get(cart_id=_cart_id(request))
    product = get_object_or_404(Product, id=product_id)
    
    try:
	    cart_item = CartItem.objects.get(product=product, cart=cart, id=cart_item_id)
	    if cart_item.quantity >1:
	        cart_item.quantity -=1
	        cart_item.save()
	
	    else:
	        cart_item.delete()
	except:
		pass

    return redirect('cart')
```
>- `cart_item_id` 를 추가해준다.
>- 마이너스 버튼을 누르면 수량을 1개씩 삭제해서 업데이트 시켜준다.


```python
from django.urls import path
from . import views


urlpatterns = [

    path('', views.cart, name='cart'),

    path('add_cart/<int:product_id>/', views.add_cart, name='add_cart'),
	
    #path('remove_cart/<int:product_id>/', views.remove_cart, name='remove_cart'),
    #path('remove_cart_item/<int:product_id>/', views.remove_cart_item, name='remove_cart_item'),
    
    path('remove_cart/<int:product_id>/<int:cart_item_id>/', views.remove_cart, name='remove_cart'),
    path('remove_cart_item/<int:product_id>/<int:cart_item_id>/', views.remove_cart_item, name='remove_cart_item'),


    # path('checkout/', views.checkout, name='checkout'),

]
```

### 장바구니 삭제 버튼

```python
<td class="text-right">

<a href="{% url 'remove_cart_item' cart_item.product.id cart_item.id %}" onclick="return confirm('Are you sere you want to delete this item?')" class="btn btn-danger"> Remove</a>

</td>
```
>- 같은 원리로 `cart_item.id` 값을 넘겨준다.
>- 위에 미리 수정해 놓았는데 url 도 값을 받을 수 있도록 수정해준다.
>- 자바스크립트로 삭제 할껀지 물어봐주는 것도 처리해준다.


{% endraw %}
