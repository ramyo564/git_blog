---
layout: single
title: " [Django DRF] 실시간 경매 live auction (4)"
categories: Django
tags:
  - Python
  - MongoDB
  - Project_Live_Auction
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# Djongo의 버그 발견

product model 생성 시
`set_auction_active` 함수가 `instance.save()` 를 호출 하면서 `post_save` 로 다시 트리거 되어 무한루프 발생

![image](https://github.com/wodnrP/realtime_auction/assets/101565486/38715874-a8cb-4227-8fda-5fcf6685c563)

---

![](https://i.imgur.com/iaf2296.png)

API 로 테스트를 했을 당시에는 문제가 없었다. 근데 팀원으로 부터 받은 이슈 에서는 admin에서 따로 데이터를 생성할 시에 문제가 생기는 걸로 짐작했다.
근데 API로는 데이터를 생성할 때는 문제가 없고 왜 어드민 패널에서 데이터를 생성하면 무한루프에 빠지는걸까?     


![](https://media0.giphy.com/media/OADnCQDNf0WHu/giphy.gif?cid=ecf05e47vvy7px89bfn5kayx01g09ylvu0jfphh13axfz6xp&ep=v1_gifs_search&rid=giphy.gif&ct=g)


같은 팀원이 오류를 발견해준 것 뿐만 아니라 코드까지 제안해줬다

변경 전 코드

```python
    @receiver(post_save, sender="product.Products")
    def set_auction_active(sender, instance, **kwargs):
        if not instance.auction_end_at or instance.auction_end_at < timezone.now():
            instance.auction_active = False
            instance.save()
```

변경 후 

```python
    @receiver(post_save, sender="product.Products")
    def set_auction_active(sender, instance, created, **kwargs):
        if created:
            if not instance.auction_end_at or instance.auction_end_at < timezone.now():
                instance.auction_active = False
                instance.save(update_fields=["auction_active"])

```


그렇다면 receiver를 통해서 set_auction_active를 호출할 때 if created의 조건을 만들어야지만 재귀호출에 빠지지 않는다는건데       

솔직히 왜 if created 조건이 있을 때만 재귀 호출을 막을 수 있는지 100% 이해가 가지 않았다 (뭐가 다른 거지? 뭘 내가 이해 못한거지...?)

![](https://media0.giphy.com/media/5Zesu5VPNGJlm/giphy.gif?cid=ecf05e47h6scpw9hngh89vhgwb46kjtxac5b3pfy0um92xh1&ep=v1_gifs_search&rid=giphy.gif&ct=g)

그렇다면 직접 알아보는거 말고는 방법이 없다.. 😑

## @receiver 재귀호출이 일어난 이유!

`@receiver(post_save, sender="product.Products")` 신호 핸들러는 `product.Products` 모델에서 `post_save` 신호를 받아들인다. 이 신호는 해당 모델의 객체가 저장될 때마다 트리거된다.      

API를 통해 데이터를 생성할 때, 이 신호 핸들러는 객체가 생성되어 `created` 인수가 `True`인 경우만 실행된다. 그리고 `auction_active`가 변경되면 `update_fields`를 사용하여 해당 필드만 업데이트한다. 이렇게 하면 무한 루프에 빠지지 않는다.       

그러나 어드민 패널을 통해 객체를 생성할 때, 이미 생성된 객체를 저장하므로 `created` 인수가 `False`가 되고, 이 때도 `auction_active`를 변경하면 다시 `post_save` 신호가 트리거되어 핸들러가 실행된다. 이 때, 신호 핸들러 내에서 `instance.save()`를 호출하면 객체가 다시 저장되어 무한 루프가 발생한다.      

하지만 `if created` 조건은 객체가 생성될 때만 코드 블록 내의 작업을 실행하도록 만든다. 즉, 신호가 트리거될 때 `created` 인수가 `True`이면 객체가 새로 생성된 것을 의미하고, `False`이면 이미 존재하는 객체의 저장을 나타낸다.      

![](https://media1.giphy.com/media/l41lVsYDBC0UVQJCE/giphy.gif?cid=ecf05e47h6scpw9hngh89vhgwb46kjtxac5b3pfy0um92xh1&ep=v1_gifs_search&rid=giphy.gif&ct=g)
(무슨 말이지)

다시 말하면 API를 통해 데이터를 생성할 때, 객체가 새로 생성될 때만 `created`는 `True`이므로 코드 블록 내의 작업이 실행된다. `auction_active`를 변경하더라도 `created`가 `False`가 되지 않으므로 무한 루프가 발생하지 않는다.     

그러나 어드민 패널을 통해 객체를 생성할 때, 이미 생성된 객체를 저장하므로 `created`는 `False`가 된다. 이때, `auction_active`를 변경하면 다시 `post_save` 신호가 트리거되어 핸들러가 실행된다. `if created` 조건이 없다면 이때도 작업이 실행되어 객체가 다시 저장되며, 이는 무한 루프를 발생시킨다.      

### 결론 

결론적으로, `if created` 조건은 어드민 패널을 통해 새로운 객체를 생성하면, 그 객체는 실제로 이미 생성된 것이 아니며, 새로운 객체로 간주된다. 따라서 `created` 인수는 `True`로 설정된다. 
그렇기 때문에 `if created` 조건이 True로 평가되고, 해당 코드 블록이 실행된다.     


![](https://media0.giphy.com/media/Rlwz4m0aHgXH13jyrE/giphy.gif?cid=ecf05e474h3jmji2x8e3ec7zgmq9r01lkqd7c6cxvt4q0imj&ep=v1_gifs_search&rid=giphy.gif&ct=g)
