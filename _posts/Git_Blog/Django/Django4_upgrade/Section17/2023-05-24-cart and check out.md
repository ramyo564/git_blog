---
layout: single
title: " [Django] Shopping Mall (12)"
categories: Django_ShoppingMall
tags:
  - Python
  - 장바구니
  - 체크아웃
  - Django
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# 체크아웃 페이지 만들기
`[Django] 장바구니 체크아웃`
{% raw %}

#### carts>views.py

```python
@login_required(login_url='login')
def checkout(request, total=0, quantity=0, cart_items=None):
    try:
        tax=0
        grand_total=0
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
    return render(request, 'store/checkout.html', context)
```
>- 기존에 만들었던 `cart`를 재활용해서 장바구니에 있던 상품 정보들을 가져왔다. 그리고 장바구니에서 결제를 눌렀을 때 로그인이 되어 있으면 체크아웃 페이지로 아닌 경우에는 자동으로 로그인 페이지로 가도록 데코레이터 `@login_required` 를 사용했다.
>- 현재 로그인하면 장바구니에 있던 세션정보가 넘어오지 않기 때문에 이를 연결해주기 위해서 모델을 수정해줘야 한다.
>	- 로그인하면 세션아이디가 바뀜


#### carts>models.py
```python
class CartItem(models.Model):
    user = models.ForeignKey(Account, on_delete=models.CASCADE, null=True)
    product = models.ForeignKey(Product, on_delete=models.CASCADE)
    variations = models.ManyToManyField(Variation, blank=True)
    cart    = models.ForeignKey(Cart, on_delete=models.CASCADE, null=True)
    quantity = models.IntegerField()
    is_active = models.BooleanField(default=True)

    def sub_total(self):
        return self.product.price * self.quantity
    def __unicode__(self):
        return self.product
```
> 로그인을 안 한 상태에서 로그인을 하면 세션이 초기화 된다.
> 	(장바구니도 날라간다는 소리)
> 로그인을 한 상태에서는 장바구니에 아이템을 담으면 로그아웃을 해도 계속 트래킹 할 수 있도록 `Account` 테이블을 참조해준다.

#### accounts>views.py
```python
def login(request):
    if request.method == 'POST':
        email = request.POST['email']
        password = request.POST['password']
        
        user = auth.authenticate(email=email, password=password)
        
        if user is not None:
            try:
                cart = Cart.objects.get(cart_id=_cart_id(request))
                is_cart_item_exists = CartItem.objects
                .filter(cart=cart).exists()
                
                if is_cart_item_exists:
                    cart_item = CartItem.objects.filter(cart=cart)
                    
                    for item in cart_item:
                        item.user = user
                        item.save()
                        
            except:
                pass
            auth.login(request, user)
            messages.success(request, "You are new logged in.")
            return redirect('dashboard')
        else:
            messages.error(request, 'Invalid login credentials')
            return redirect('login')
    return render(request, 'accounts/login.html')
```
>`cart` 를 먼저 선언하며서 `cart_id` 의 정보를 갖고 온다.
> 자식 테이블인 `CartItem` 은 부모테이블 `cart` 를 참조하고 있다
> 예를 들어 장바구니 1번은 셔츠 m사이즈 바지 L 사이즈 3개 이런식으로 정보들이 담겨있고 결론은 장바구니에 아이템이 담겨있었던건지 아닌지를 검사해서 그 결과를 `is_cart_item_exists` 에 담는다.
> 	`cart_id` 는 세션 값이다.
>`is_cart_item_exists` 가 참이면 아이템의 모든 정보를 `product_variation` 에 담아준다.
>그 후 장바구니에 여러 개의 물건이 있을 수 있으므로 반복문으로 다시 리스트에 담아준다.

#### carts>context_processors.py
```python
def counter(request):
    cart_count = 0
    if 'admin' in request.path:
        return {}
    else:
        try:
            cart = Cart.objects.filter(cart_id=_cart_id(request))
            if request.user.is_authenticated:
                cart_items = CartItem.objects.all()
                .filter(user=request.user)
            else:
                cart_items = CartItem.objects.all()
                .filter(cart=cart[:1])
            for cart_item in cart_items:
                cart_count += cart_item.quantity
        except Cart.DoesNotExist:
            cart_count = 0
    return dict(cart_count=cart_count)
```
>- 이전과 달리 `CartItem` 테이블은 `Account` 테이블을 참조 하므로 로그인을 하면 user 값을 갖고 오고 로그인이 되어있지 않은 상태는 기존의 방식대로 장바구니를 카운팅 한다.

#### carts>views.py
```python
def cart(request, total=0, quantity=0, cart_items=None):
    try:
        tax=0
        grand_total=0
        # 추가 
        if request.user.is_authenticated:
            cart_items = CartItem.objects
            .filter(user=request.user, is_active=True)
		# 
		else:
	        cart = Cart.objects.get(cart_id=_cart_id(request))
	        cart_items = CartItem.objects
	        .filter(cart=cart, is_active=True)
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
>- 위의 로직과 똑같이 로그인을 했을 때와 로그인 하지 않았을 때로 나눠서 로직을 짜준다.

## 문제점

![](https://i.imgur.com/oZLBhfy.png)


원래 장바구니에 아이템이 있는 상태에서 로그아웃한 상태로 장바구니에 아이템을 넣고 다시 로그인 했을 때 중복된 아이템이 분리되서 나왔다.
로그인 부분에서 인식을 제대로 못 해서 발생되는 부분이였다.

#### accounts>views.py
```python
def login(request):
    if request.method == 'POST':
        email = request.POST['email']
        password = request.POST['password']
        user = auth.authenticate(email=email, password=password)
        if user is not None:
            try:
                cart = Cart.objects.get(cart_id=_cart_id(request))
                is_cart_item_exists = CartItem.objects
                .filter(cart=cart).exists()

                if is_cart_item_exists:
                    cart_item = CartItem.objects.filter(cart=cart)
  
                    # Getting the product variations by cart id
                    product_variation = []
                    for item in cart_item:
                        variation = item.variations.all()
                        product_variation.append(list(variation))
                    # Get the cart items from the user to access his product variations
                    cart_item = CartItem.objects.filter(user=user)
                    ex_var_list = []
                    id = []

                    for item in cart_item:
                        existing_variation = item.variations.all()
                        ex_var_list.append(list(existing_variation))
                        id.append(item.id)                    

                    for pr in product_variation:
                        if pr in ex_var_list:
                            index = ex_var_list.index(pr)
                            item_id = id[index]
                            item = CartItem.objects.get(id=item_id)
                            item.quantity += 1
                            item.user = user
                            item.save()
                        else:
                            cart_item = CartItem.objects
                            .filter(cart=cart)

                            for item in cart_item:
                                item.user = user
                                item.save()
            except:
                pass
            auth.login(request, user)
            messages.success(request, "You are new logged in.")
            return redirect('dashboard')
        else:
            messages.error(request, 'Invalid login credentials')
            return redirect('login')
    return render(request, 'accounts/login.html')
```
>- 이전에 장바구니와 문제 해결이 비슷하다. 우선 장바구니에 물건이 있다면 해당 정보를 `product_variation` 라는 빈 리스트를 만들어서 넣어주고
>- 그 다음 필터로 user (로그인 했을 때) 정보에 담겨 있던 장바구니 정보를 불러와서 `ex_var_list` 에 넣어주고 추가로 아이템 분류를 위해 `id` 의 빈 리스트를 만들어서 각 id 값도 따로 저장해준다.
>- 반복문으로 이전에 로그인 했을 때의 아이템이 존재한다면 +1 로 업데이트 해주면 완성

## 문제점 2
- 기능 검사중 이전에는 잘 되던  remove 또는 - 버튼을 누르면 오류가 났다

```python
def remove_cart(request, product_id, cart_item_id):
    product = get_object_or_404(Product, id=product_id)
    try:
        if request.user.is_autehnticated:
            cart_item = CartItem.objects
            .get(product=product,
             user=request.user, id=cart_item_id)

        else:
            cart = Cart.objects.get(cart_id=_cart_id(request))
            cart_item = CartItem.objects
            .get(product=product, cart=cart, id=cart_item_id)

        if cart_item.quantity >1:
            cart_item.quantity -=1
            cart_item.save()

        else:
            cart_item.delete()

    except:
        pass

    return redirect('cart')
```
>- 유저가 로그인 했을 때와 안 했을 때를 구분해줘서 짜준다.
>- 로그인 했을 때는 user에 이미 `cart` 정보가 있어서 상관 없지만 로그아웃되어있는 상태에서는 아무런 정보가 없기 때문에 else 문에서 `cart`를 선언해줘야한다.
>- remove 도 같은 논리로 짜주면 된다.




{% endraw %}
