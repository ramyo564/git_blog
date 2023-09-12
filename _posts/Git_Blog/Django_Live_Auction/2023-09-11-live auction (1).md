---
layout: single
title: " [Django DRF] 실시간 경매 live auction (1)"
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
# 팀 프로젝트 시작

팀 내부 회의 결과 주제는 `실시간 경매`로 정해졌고 다들 지금 당장의 결과물을 만들어 내는 것 보다는 현재 잘 모르거나 배우고 싶은 것에 좀 더 초점을 두고 있다.     

채팅 기능, 정확히 말하면 비동기에 대해 관심이 많았고 나도 비동기를 이용해 예전에 채팅 앱을 만들어본 적은 있지만 솔직히 제대로 이해했다고 말하기 어렵다.     

또한 db로 MongoDB를 선택하게 되었는데 엄청나게 큰 이유가 있는 것은 아니고 다들 NoSQL 경험이 따로 없어서 새로운 지식을 습득하기 위해 채택했다.    

짜잘한 합의점으로는 APIView를 사용해야 된다는 것과 스웨거를 사용하지 않는 다는 점이다.     

개인적으로 개발하면서 post맨을 따로 사용하기 귀찮기도 하고 APIView를 사용하면 url을 기능마다 연결해야되는 등 특별한 경우가 아니면 보통 ViewSets 과 스웨거를 사용해서 편하게 만들었는데 이런 부분을 포기해야 되서 좀 아쉬웠다.     

## 실시간 경매의 핵심 기능

- 비동기 시스템을 이용해 유저가 상품을 등록하면 경매가 시작된다.
- 경매가 시작되면 다양한 사람들이 실시간으로 경매에 참여가 가능해진다.
- 최고 높은 가격으로 낙찰되면 구매자와 판매자간의 1:1 대화창이 새롭게 생긴다.
- 주의사항 및 확인이 끝나면 결제로 이루어진다.
- 결제가 이루어지지 않거나 허위 입찰 등 거래가 원활하게 되지 않는 것을 방지하기 위해 패널티를 부과하는 방식이 있다.


## MongoDB 설치

- 개인적으로 설치 같은 글은 계속 바뀌는 부분이 많아서 그 때 그 때 찾는 편인데 `윈도우` 환경에서는 설치가 너무 복잡했다.
	- 래서 간단하게 설치 방법에 대해 적어보려고 한다.

### Window 환경

- 현재 몽고DB 사이트에 가면 계속 로컬 설치가 아닌 클라우드로 연결시켜주려고 한다.
- [다운로드 페이지](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-windows/) 여기에 맞는 환경에 따라 설치해주면 된다. (현재 7.0 버전이다.)
- bash_profile 등 vim 에디터를 사용해야되는데 아래의 사이트에 매우 잘 정리가 되어 있다.
	- https://medium.com/@LondonAppBrewery/how-to-download-install-mongodb-on-windows-4ee4b3493514
- 다만 명령어 같은 경우 해당 포스터가 좀 오래되서 현재는 mongo 가 아닌 mongos 로 변경 해줘야 되는 점 그리고 각 환경에 맞게 알아서 수정을 해줘야한다.

```
alias mongod="/c/Program\ files/MongoDB/Server/7.0/bin/mongod.exe"
alias mongos="/c/Program\ Files/MongoDB/Server/7.0/bin/mongos.exe"
```


### 로컬 테스트

For those who want to work on latest version have to download **mongosh** which is suppressor of mongo
1. You can download it through this link [MongoDB Shell](https://www.mongodb.com/try/download/shell?jmp=docs)
2. After downloading it extract the zip and copy the mongosh folder
3. Now go inside program-files available in c drive(`C:\Program Files`)  and paste the copied folder
4. Now open the mongosh folder and go inside bin folder then copy the exact path of that bin directory
It will be similar to my path i.e. `C:\Program Files\mongosh-1.5.4-win32-x64\bin`
*paste the in a notepad
5. Now go back to program files and open MongoDb folder (At this point I hope so you have downloaded the mongodb by following the above video)
6. Now open the bin folder by following the path `C:\Program Files\MongoDB\Server\6.0\bin`
copy your bin directory path and save it in notepad

  
**_This is the time to setup enviroment variable_**

1. So to setup enviroment variable click **window** button type **env** press enter
2. System Property Dialog Box will apppear clink on **Enviroment Variable**
3. Enviroment Variable Dialog Box will Appear. There you can see many variable presents inside use Variable from Home
4. Select the **Path** variable and click on **Edit** button
5. Edit Enviroment Variable Dialog Box will appear (*If not then make sure you have clicked on 1st edit button)
6. Paste the path you have copied in notepad one by one by clicking on **New** button
7. Once you have paste both the path click on **Ok** button of all 3 dialog box and it will close all the dialog box

**_Its  time to start connecting our database_**

1. Open hyper terminal and type "**mongod**" or "**Mongod**" and it will start the server
*Don't take stress if you see something like this :--

```
{"t":{"$date":"2022-08-20T10:12:09.003+05:30"},"s":"I",  "c":"NETWORK"................
```

This is not any error you can read its doc for more information [Log Messages](https://www.mongodb.com/docs/manual/reference/log-messages/)

2. Now wait untill you see something like this

```
{"t":{"$date":"2022-08-19T22:29:25.840-07:00"},"s":"I",  "c":"NETWORK",  "id":46887,   "ctx":"listener","msg":"Waiting for connections","attr":{"port":27017,"ssl":"off"}} 
```

3. Open new hyper terminal and type "**mongosh**"

After sometime you can see something like  `test>`  or a normal greater bracket `>`

4. Once you reached at this step without any error and warning you can start to learn mongoDB 

HAPPY LEARNING !!  :-)


![](https://i.imgur.com/7BOBga8.png)


### Mongo DB CRUD Operations

[CRUD 공식 문서](https://www.mongodb.com/docs/manual/crud/)

```
use shopDB
-> switched to db shopDB
show dbs
db
-> shopDB
db.products.insertOne({
	id: 1, name: "Pen", price: 1.20
})
show collections
db.products.find()
```

[연산자 공식문서](https://www.mongodb.com/docs/manual/reference/operator/query/)

```
db.products.find({name: "Pencil"})

db.products.find({price: {$gt: 1}})

db.products.find({id: 1}, {name: 1, id: 0})
-> {"name": "Pen"}

db.products.updateOne({id: 1}, {$set: {stock: 32}})
```

### Relationships in MongoDB

```
db.products.insert(
	{
		id:3,
		name: "Rubber",
		price: 1.3,
		stock: 43,
		reviews: [
			{
				authorName: "Sally",
				rating: 5,
				review: "Best rubber ever!"
			},
			{
				authorName: "Jhon",
				rating: 4,
				review: "Good Good!"
			},
		]
	}
)
```

## Django with Mongo DB 

[파이몽고](https://www.mongodb.com/docs/drivers/python/)

현재 모델만 만들어 놓고 몽고 db를 공부하고 있는데 우선 장고에서는 mongoDB 지원을 안한다.     
몽고 DB를 장고에서 간편하기 위해서는 `djongo` 라이브러리를 사용하는데 관계형 데이터를 매핑하는 ORM 형식을 NoSQL 의 맵핑 ODM으로 변환해서 사용한다.     
 
djongo 의 그나마 최적화된 버전이 장고 3.0.5 버전이고 실제로 PYPI 에 등록된 djongo 업데이트도 21년 6월에 끝나있다.    

[Djongo 깃헙](https://github.com/doableware/djongo/commits/master)

깃에 이슈 보고도 그렇고 최근 커밋이 3주전에 있긴 하지만 커밋 내용을 보니 페이지 부분이고 실제로 문제가 되는 쿼리 변환에 대한 최근 업데이트는 23년 2월이다.      

버그 없이 온전히 쓰려면 파이몽고를 써야하는데 이럴 경우 ORM을 포기해야 하는거 같다.     

뷰도 그렇고 모델, 마이그레이션이 없으며 어드민 패널도 따로 지원되지 않는다.  또한 settings.py 가 아닌 따로 db에 다이렉트로 연결 시키고 직접 관리해야 한다. 특히 ORM 을 쓰지 않기 때문에 유저 인증권한등 여러부분에서 어려움이 생길 수 있다.     

<iframe width="1086" height="611" src="https://www.youtube.com/embed/ORog5kwPd_E" title="Watch This Before Using Django With MongoDB" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
[djongo에 대한 블로그 소개](https://medium.com/@dennisivy/read-this-before-using-mongodb-with-django-879927ce1ef)

개인적으로 파이몽고가 아닌 djongo를 이용해서 orm으로만 제어한다면 왜 NoSQL을 굳이 써야하는지 이해가 잘 가지 않았다.     

djongo를 사용하는 부분에 있어서 꺼려했던 나의 솔직한 이유는 현재 모두 NoSQL에 대해서 잘 모르는 상태인데 나중에 문제가 생겼을 경우 온전히 나의 문제인지 혹은 내가 잘 몰라서 구현을 못 하는건지 아니면 이게 버그인지를 구별할 수 없기 때문이였다.    

팀원들 말로는 결과 값이나 쿼리 성능이 더 좋을 거라고 하는데 솔직히 나도 해보지 않아서 아니라고 말 할 수 없었다.      

제일 중요한건 팀원들 모두 몽고DB를 쓰고 싶어했다.    
만약 파이몽고를 통해 로우쿼리를 쓰는 팀원이 없다면 나중에 문제가 생길 경우 DB만 다시 교체하면 될 거 같아 그냥 진행하기로 했다.

### 요약하자면

- djongo를 쓸 경우 기존 orm 으로 작업이 동일하므로 나중에 오류가 났을 경우 db만 다른걸로 바꾸면 될 것 같다.
- 대부분 djongo가 아닌 pymongo를 추천하는데 기존 몽고 DB에서도 파이썬 드라이버로 파이몽고를 추천한다.
	- 실제로도 플라스크나 fastAPI에서 사용하는데 문제가 없다
	- 다만 장고가 ORM 기반이라 파이몽고를 사용할 경우 정말 많은 부분을 손봐야 되서 이렇게 될 경우 장고의 간편함의 메리트가 없어지는 단점이 있다.


[몽고B에서 장고 사용](https://www.mongodb.com/compatibility/mongodb-and-django)

어차피 몽고 DB를 사용하기로 결정했으니 큰 오류 없이 잘 넘어갔으면 좋겠다.     
오류가 생기면 issue 부분에 기록하고 그 때 대처하기로 했다.
몽고DB를 사용하면서 생기는 오류들은 지속적으로 기록 👍
