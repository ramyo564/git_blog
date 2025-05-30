---
layout: single
title: " [Django] request.get과 request.GET.get의 차이점 "
categories: Django_Basic
tags:
  - Python
  - 차이점
  - Django
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# **request.get과 request.GET.get의 차이점**


 request관련한 다양한 메소드들이 있다. 이번 포스팅에서 비교할 메소드는 request.get과 request.GET.get이다. 

## **request.get과 request.GET.get의 차이점**

---

 한 마디로 request.get은 python의 문법이며, request.GET은 djang의 문법이라는 이미지라고 하면 알기 쉬울 거라고 생각한다. 또한 request.GET을 사용하면 get 리퀘스트를 보냈을 때의 파라미터도 얻을 수 있다는 것이 특징이다. 순서대로 조금 더 자세히 알아보자. 

## **request.get에 대해**

---

 먼저 request.get이다. 이것은 request라는 이름의 객체에 대해 get 메소드를 실행하는 것이다. 이때의 request객체는 사전형의 데이터인 것을 인식하자. 구체적인 코드로 살펴보자.

```python
request = {'key':10, 'animal':'cat'}

request.get('key')

# 아웃풋 10
```

  request라는 객체에 대해 get메소드를 사용하여 데이터를 꺼냈다. 그러나 한 가지 주의 사항이 있다. 그것은 **_get 메소드를 사용할 수 있는 것은 사전형 객체_**뿐이다. django에 function based view를 정의할 때의 인수로써 설정하는 'request'는 사전형 데이터가 아닌 것을 주의하자. 시험삼아 request.get를 취득해보자.

```python
def fbv(request):

     print(request.get(''))

    return HttpResponse('')
    
 # 커맨드 라인
 # AttributeError: 'WSGIRequest' object has no attribute 'get'
```

 WSGIRequest객체는 get속성이 없다는 에러가 발생한다. 즉 request는 사전형의 객체가 아닌 것이다.

## **request.GET에 대해**

---

 다음은 request.GET에 대해 살펴보자. request.GET은 django에서 사용할 수 있다. 아까 방금 확인했지만 request는 Http 리퀘스트가 보내졌을 때에 Django가 만든 객체이다. 그리고 request.GET을 실행하는 것으로, request의 정보를 사전형의 데이터로 얻을 수 있게 된다. 즉, request.GET하는 것으로 get메소드를 사용하여 데이터를 취득하는 것이 가능하게 된다는 것이다.

 실제 코드로 확인해보자. 기본적인 코드의 설정은 [초기 설정 코드](https://codor.co.jp/django/basecode)라는 포스팅에서 설명하고 있으므로 참고하길 바란다. 먼저 아래의 url을 적은 경우를 전제로 코드를 살펴보자. 

http://localhost:8000/fbv/?q=100

 url에 q=100이라는 파라미터를 추가하고 있다. 이 파라미터를 request.GET으로 취득해보자.

```python
def fbv(request):

     print(request.GET['q'])

    return HttpResponse('')
    
# 아웃풋 100
```

 확실히 url의 파라미터를 취득하고 있다는 것을 확인할 수 있다. 

 더욱이 request.GET에 대해 이해를 깊이해보자. 예를 들어, request.GET형의 사전형이 아닌 파라미터를 참고한 경우에 어떻게 될까? 실제 코드로 살펴보자.

```python
def fbv(request):

     print(request.GET['somekey'])

    return HttpResponse('')
    
# 커맨드 라인
# django.utils.datastructures.MultiValueDictKeyError: 'somekey'
```

 위와 같은 에러가 발생한다.

 그리고 django에는 request의 내용을 얻어낼 때는 request.GET을 사용하는 것이 일반적이다. 그 이유는 get 메소드는 대상이 되는 데이터가 없는 경우에 None이 리턴되기 때문이다. 이것도 실제 코드로 확인해보자.

```python
def fbv(request):

     print(request.GET.get('somekey'))

    return HttpResponse('')

# 아웃풋 None
```

 바로 전에 언급했듯 None이 리턴되는 것을 알 수 있다. 

## **결론**

---

django에서는 request내용을 뽑아낼 때는 request.GET.get을 사용하는 것이 일반적이다. 그 이유는 get 메소드는 대상으로 하는 데이터가 없는 경우에 None을 리턴하기 때문이다. 

(1) get()은 python 메소드. 대상은 사전형 데이터

(2) django의 request는 사전형이 아니다.

(3) request를 사전형으로 바꿀 수 있는 것이 request.GET

(4) 에러가 발생하지 않기 때문에 request.GET.get을 사용하는 것이 일반적이다.

---

참고자료

[intellectual-curiosity.tokyo/2019/02/27/django%E3%81%A7get%EF%BC%8Fpost%E3%81%8B%E3%82%89%E5%80%A4%E3%82%92%E5%8F%96%E5%BE%97%E3%81%99%E3%82%8B%E6%96%B9%E6%B3%95/](https://intellectual-curiosity.tokyo/2019/02/27/django%E3%81%A7get%EF%BC%8Fpost%E3%81%8B%E3%82%89%E5%80%A4%E3%82%92%E5%8F%96%E5%BE%97%E3%81%99%E3%82%8B%E6%96%B9%E6%B3%95/)

[codor.co.jp/django/difference-request-get](https://codor.co.jp/django/difference-request-get)
[원글](https://engineer-mole.tistory.com/125)
