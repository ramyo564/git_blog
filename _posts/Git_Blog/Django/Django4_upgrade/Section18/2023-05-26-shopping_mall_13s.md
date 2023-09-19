---
layout: single
title: " [Django] Shopping Mall (13)"
categories: Django_ShoppingMall
tags:
  - Python
  - 장바구니
  - 결제하기
  - Django
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# 장바구니 결제하기

{% raw %}

테스트 도중에 로그인을 하지 않은 상태에서 체크아웃 버튼을 누르면
체크아웃은 로그인을 한 상태에서만 진행이 되므로 로그인 페이지로 연결되고
로그인이 되면 대시보드로 리다이렉팅 되기 때문에 다시 결제 창으로 가야 되는 번거로움이 있었다.
이 부분을 해결하기 위해서 `reuqests` 라이브러리를 사용했다.
`pip install reqeusts`

이 라이브러리는 python용 HTTP 라이브러리다.
[사용법](https://seungjuv.tistory.com/entry/requests-%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC-%EC%82%AC%EC%9A%A9%EB%B2%95)

#### views.py> accounts
```python
def login(request):
    if request.method == 'POST':
				....
				...
				..
				.
                
            auth.login(request, user)
            messages.success(request, "You are new logged in.")
            url = request.META.get('HTTP_REFERER')

            try:
                query = requests.utils.urlparse(url).query
                print('query -> ', query)
                print('---------')

                # next=/cart/checkout/
                params =  dict(x.split('=') for x in query.split('&'))
                print('params -> ', params)

                if 'next' in params:
                    nextPage = params['next']
                    return redirect(nextPage)

            except:
                return redirect('dashboard')

        else:
            messages.error(request, 'Invalid login credentials')
            return redirect('login')

    return render(request, 'accounts/login.html')
```

해당 라이브러리로 로직을 체크하면 로그인을 하지 않은 상태에서 결제창으로 넘어갈 때의 파라미터는 `query ->  next=/cart/checkout/` 이런 식의 결과를 얻는다.
그럼 위와 같은 경로가 찍힐 때 대시보드가 아닌 결제페이지로 보내기 위해서 
try 문으로 문제를 해결해주면 된다.

그리고 로그인 후 결제 창으로 넘어갔을 때 장바구니에 있는 정보를 갖고 오기 위해서
기존의 로직도 아래와 같이 변경해준다.

#### views.py>carts
```python
@login_required(login_url='login')
def checkout(request, total=0, quantity=0, cart_items=None):
    try:
        tax=0
        grand_total=0
        if request.user.is_authenticated:
            cart_items = CartItem.objects
            .filter(user=request.user, is_active=True)

        else:
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
이렇게 되면 로그인이 되었을 때 와 그렇지 않을 때의 예외 상황 모두 해결할 수 있다.
(사실 정상적인 경로라면 else문이 실행될 일은 없다.)




{% endraw %}
