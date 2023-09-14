---
layout: single
title: " [Django DRF] 실시간 경매 live auction (2)"
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

개인적으로 장고를 개발할 때 초기에 모델 형성 후 어드민을 통해 관계형성에 있어서 문제가 없는지 확인을 하고 진행한다.      

```python
class Products(models.Model):
    seller_id = models.ForeignKey(User, on_delete=models.CASCADE)
    product_name = models.CharField(max_length=100)
    product_price = models.CharField(max_length=100)
    product_content = models.CharField(max_length=100)
    auction_start_at = models.DateTimeField()
    auction_end_at = models.DateTimeField()
    auction_active = models.BooleanField()

    class Meta:
        verbose_name = "Product"
        verbose_name_plural = "Products"

    def save(self, *args, **kwargs):
        if not self.id:
            # 모델이 생성될 때만 현재 시간을 설정
            self.auction_start_at = timezone.now()
            # 예시로 3일 후로 설정
            self.auction_end_at = timezone.now() + timezone.timedelta(days=3)
        super(Products, self).save(*args, **kwargs)

    def __str__(self):
        return self.product_name
```

경매를 위해 유저가 물건을 등록하면 등록한 시점부터 자동으로 3일동안 활성화 되게 만들었다.       
어드민을 통해 유저를 등록하고 물건을 등록할 때 db 에서 알 수 없는 오류가 발생했다.     

![](https://i.imgur.com/ywNSHfo.png)

djongo의 문제일까? 아니면 내 문제일까?

![](https://media4.giphy.com/media/wVcNP3TnXbl84/giphy.gif?cid=ecf05e47t2hke9dham0f2lhcovho3cgarjvfgmqi7l4phlav&ep=v1_gifs_search&rid=giphy.gif&ct=g)

혹시나 싶어서 db를 sqlite로 변경 후에 진행했고 동일 코드로 구동시 문제가 없었다.     
유저 등록이나 char 또는 int 필드는 문제가 없는 것 같아서 datetime 필드 부분에 문제가 있다고 생각했다.      
왜냐하면 djongo를 통해 매핑할 때 문제가 발생하는 게 아닐까 생각했다.      

특히 save 함수 부분에 문제가 있는 것 같아 지워서도 진행해 봤지만 동일한 오류가 발생했다.     

코드 리뷰에서 datetime 이 아닌 date 필드를 사용해 보라고 해서 고쳐 봤지만 동일한 오류가 발생했다.      

djongo 에서 orm 처리를 똑바로 못 해서 몽고 db에 데이터가 올바르게 들어가지 않는 상황이였다.     

![](https://media2.giphy.com/media/mP4240mOW8ngA/giphy.gif?cid=ecf05e47l5prnmyzyrzkrf7jnk9ff6xp5xebgy7ivwwxhid1&ep=v1_gifs_search&rid=giphy.gif&ct=g)


## django에서 mongoDB 를 사용하려면 어떻게 해야될까?

<iframe width="679" height="413" src="https://www.youtube.com/embed/oUIjHQMBdD4" title="Connect MongoDB with Django project using PyMongo | Complete Guide to MongoDB CRUD Operations" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
위 동영상과 같이 settings.py 에서 db를 제어하는게 아닌 파이몽고를 통해 직접적으로 제어해야되는데 ORM 기반인 장고에서는 이렇게 개발할 경우 초기부터 문제가 많아질 수 있다고 생각했다.      

팀원들과 상의 결과 문서형 데이터를 다시 관계형 데이터로 매핑하는 과정에서 성능에 대한 보장이 어렵고 패키지간 충돌 우려가 많아서 우선 sqlite로 초기 개발을 하고 rdbms로 넘어가는 걸로 결정했다.      

db에서 오류가 나면 이제 무조건 내가 코드를 잘못짜서 생기는 문제다.      

![](https://media3.giphy.com/media/Qvm2704d1Dqus/giphy.gif?cid=ecf05e475olrddw9zbtv0jukdwp73auij8kmh0ht5zh6v4y8&ep=v1_gifs_related&rid=giphy.gif&ct=g)

이번에 여러가지 오류를 만나면서 팀원들과 많은 것을 배웠다.      

개발하면서 문제가 생기면 추가로 또 기록!
