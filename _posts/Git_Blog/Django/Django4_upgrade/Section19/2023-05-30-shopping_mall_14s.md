---
layout: single
title: " [Django] Shopping Mall (14)"
categories: Django_ShoppingMall
tags:
  - Python
  - 결제
  - Django
  - Project_ShoppingMall
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# 결제 연동시키기

{% raw %}

[페이팔 사이트](https://developer.paypal.com/docs/checkout/standard/upgrade-integration/)
[네이버 페이](https://developer.pay.naver.com/docs/v2/api)
[토스](https://docs.tosspayments.com/common/testing)
[카카오페이](https://developers.kakao.com/docs/latest/ko/kakaopay/single-payment#prepare-sample)

페이팔과 카카오페이로 결제 페이지를 만든 이유는 우선 네이버 페이, 토스등은 페이팔처럼 JavaScriptSDK로 진행되지만, 카카오톡은 REST API로 진행된다.
그래서 나머지 결제방식과 다른 카카오페이와 페이팔 이렇게 두 가지로 결제 창을 구현했다.
- 페이팔 계정은 캐나다에서 만들었던 계정을 사용했다.
- 한국에서도 비즈니스 어카운트를 열 수 있는 거 같은데 개발자 센터에는 각국 통화 코드는 다 있는데 한화로 따로 변경하는 코드가 없다;;  

## Django 보안 문제

장고 4.0 부터 보안 때문에 페이팔 팝업 결제창이 막혔다.

![](https://i.imgur.com/UfFtphb.png)

해당 문제는 settings.py 에 `SECURE_CROSS_ORIGIN_OPENER_POLICY='same-origin-allow-popups'`를 넣으면 된다. [참고](https://stackoverflow.com/questions/71104248/paypal-javascript-sdk-button-opens-aboutblankblocked-window-in-django-template)

## 페이팔 결제창 구현

```python
<script src="https://www.paypal.com/sdk/js?client-id={{ PAY_PAL }}" data-sdk-integration-source="integrationbuilder"></script>

<script>

  {% comment %} # 수동으로 토큰 만들기 {% endcomment %}

  function getCookie(name) {
	let cookieValue = null;
	if (document.cookie && document.cookie !== '') {
		const cookies = document.cookie.split(';');
		for (let i = 0; i < cookies.length; i++) {
			const cookie = cookies[i].trim();
			// Does this cookie string begin with the name we want?
			if (cookie.substring(0, name.length + 1) === (name + '=')) {
				cookieValue = decodeURIComponent(
				cookie.substring(name.length + 1));
				break;
			}
		}
	}
	return cookieValue;
}
  var amount = "{{ dollar }}"
  var url = "{% url 'payments' %}"
  var csrftoken = getCookie('csrftoken');
  var orderID = "{{order.order_number}}"
  var payment_method = 'PayPal'
  paypal.Buttons({
	style: {
	  color: 'blue',
	  shape: 'rect',
	  label: 'pay',
	},
	// Set up the transaction
	createOrder: function(data, actions) {
	  return actions.order.create({
		purchase_units: [{
		  amount: {
			value: amount,
		  }
		}]
	  });
	},
// Finalize the transaction
onApprove: function(data, actions) {
  return actions.order.capture().then(function(details) {
	// Show a success message to the buyer
	console.log(details);
	sendData();
	function sendData(){
	  fetch(url, {
		method : "POST",
		headers: {
		  "Content-type": "application/json",
		  "X-CSRFToken": csrftoken,
		},
		body: JSON.stringify({
		  orderID: orderID,
		  transID: details.id,
		  payment_method: payment_method,
		  status: details.status,
		}),
	  })
	}
  });
}
  }).render('#paypal-button-container');
</script>
```
`onApprove` 를 통해서 결제 성공여부 정보를 받는다.
여기서 response 내용을 보기 위해서 `console.log(details)`를 찍었다.
`details`에는 transaction의 모든 디테일이 포함 되어있다.

![](https://i.imgur.com/Cx4bzOt.png)

이제 해당 정보를 View에서 처리하면 된다.
해당 정보를 백엔드로 보내줄 함수 `sendData()` 를 만들어준다.
[Fetch function](https://developer.mozilla.org/ko/docs/Web/API/Fetch_API)
fetch 함수를 사용해서 데이터를 업데이트 했다.
- 자바스크립트 안에서는 `{% csrf_token %}`가 안된다. 그래서 수동으로 만들어줘야한다.
	- [CSRF 수동으로 토큰 만들기](https://docs.djangoproject.com/en/4.2/howto/csrf/)
- AJAX 에서는 csrf 가 아닌 X-CSRF-Token 을 사용한다.
	- [X-CSRF-Token?](https://stackoverflow.com/questions/34782493/difference-between-csrf-and-x-csrf-token)
- 해당 정보를 view에서 제대로 받는걸 확인하면 끝

```python
def payments(request):
    body = json.loads(request.body)
    print(body)
    return render(request, 'orders/payments.html')
```

![](https://i.imgur.com/fVZizQY.png)

```python
def payments(request):
    body = json.loads(request.body)
    order = Order.objects.get(
    user=request.user, is_ordered=False, order_number=body['orderID'])

    # Store transaction details inside Payment model
    payment = Payment(
        user = request.user,
        payment_id = body['transID'],
        payment_method = body['payment_method'],
        amount_paid = order.order_total,
        status = body['status'],
    )
    payment.save()
    order.payment = payment
    order.is_ordered = True
    order.save()
    return render(request, 'orders/payments.html')
```
json 화된 정보를 다시 받아서 이전에 만들어둔 Payment 모델에 입력한다.
그리고 Order 모델에서 order_number가 일치하는 객체를 꺼내와서 payment 도 저장해준다.
![](https://i.imgur.com/x2hsSrk.png)

정보가 잘 넘어온다.

## 카카오페이 결제창 구현

카카오페이는 AJAX으로 구현이 안 된다.
그래서 맨 처음에 form 태그로 구현했었는데 네비게이션 바에 구현되어 있는 장바구니 갯수 때문에 오류가 계속 났다.
왜냐하면 카카오페이 버튼을 누르면 카카오페이의 페이지로 리다이렉팅 되는데 이때 네비게이션에 구현되어있는 정보들이 넘어가거나 넘어가도 다시 넘어오지를 못해서 오류가 났다.

그래서 어차피 로직상 체크아웃 페이지로 가는 순간 모델의 모든 정보가 저장되어 있기 때문에 굳이 폼에서 정보를 받지 않고 모델에서 꺼내오는 방법으로 해결했다.

```python
<form method="post" action="{% url 'kakao_pay' %}">
{% csrf_token %}
  <input class="btn btn-warning btn-block" type="submit" value="KakaoPay">
</form>
```
폼 태그로는 그냥 카카오페이 버튼을 눌린 정보만 넘어가고 상품 정보에 대해서는 따로 없다.

```python
last_order_number = 0

def kakao_pay(request):
    BASE_URL = "http://127.0.0.1:8000"
    if request.method == 'POST':
        current_user = request.user
        # If the cart count is less than or equal to 0, then redirect back to shop
        cart_items = CartItem.objects.filter(user=current_user)
        cart_count = cart_items.count()
        if cart_count <= 0:
            return redirect('store')

        # total payment
        grand_total = 0
        total =0
        tax =0
        quantity =0

        for cart_item in cart_items:
            total += (cart_item.product.price * cart_item.quantity)
            quantity += (cart_item.quantity)

        tax = int(total * 0.1) # 부가가치세
        grand_total = tax + total

        # Order number
        global last_order_number
        KAKAO_PAY = settings.KAKAO_PAY
        URL = 'https://kapi.kakao.com/v1/payment/ready'
        headers = {
            "Authorization": "KakaoAK " + KAKAO_PAY,
            "Content-type": "application/x-www-form-urlencoded;charset=utf-8",    
        }
        data = {
            'cid': 'TC0ONETIME',  # test code
            'partner_order_id': last_order_number,  
            'partner_user_id': request.user,  
            'item_name': f'{cart_item.product.product_name} 상품등 전체 총 {cart_count}건',
            'quantity': quantity,  
            'total_amount': grand_total,  
            'tax_free_amount': 0,  
            'approval_url': BASE_URL + '/orders/kakao_pay_approval/',  
            'cancel_url': BASE_URL + '/orders/kakao_pay_cancel/',  
            'fail_url': BASE_URL + '/orders/kakao_pay_cancel/',  
        }
        res = requests.post(URL, headers=headers, params=data)
        request.session['tid'] = res.json()['tid']
        next_url = res.json()['next_redirect_pc_url']
        return redirect(next_url)


```

API 를 통해 정보를 넘겨주고 승인이 되면 `kakao_pay_approval` 그 외에는 `kakao_pay_cancel` 으로 보내준다.
![](https://i.imgur.com/UVCPVNJ.png)


```python
def kakao_pay_approval(request):
    KAKAO_PAY = settings.KAKAO_PAY
    URL = 'https://kapi.kakao.com/v1/payment/approve'
    headers = {
        "Authorization": "KakaoAK " + KAKAO_PAY,
        "Content-type": "application/x-www-form-urlencoded;charset=utf-8",    
    }
    data = {
        'cid': 'TC0ONETIME',  # test code
        "tid": request.session['tid'],
        'partner_order_id': last_order_number,  
        'partner_user_id': request.user,
        "pg_token": request.GET.get("pg_token"),
    }

    res = requests.post(URL, headers=headers, params=data).json()
    order = Order.objects.get(user=request.user, is_ordered=False, order_number=last_order_number)
    payment = Payment(
        user = request.user,
        payment_id = res['tid'],
        payment_method = 'KaKao Pay',
        amount_paid = res['amount']['total'],
        status = 'COMPLETED',
    )
    payment.save()
    order.payment = payment
    order.is_ordered = True
    order.save()
    return render(request, 'orders/payment-success.html')

def kakao_pay_cancel(request):
    return render(request, 'orders/payment-failed.html')
```

![](https://i.imgur.com/yOsuz9C.png)


{% endraw %}

