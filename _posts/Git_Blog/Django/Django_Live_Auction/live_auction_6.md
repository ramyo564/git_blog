---
layout: single
title: " [Django DRF] 실시간 경매 live auction (6)"
categories: Django
tags:
  - Python
  - Project_Live_Auction
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# Product API

| HTTP method | 기능                     | end-point                    | auth required |
| ----------- | ------------------------ | ---------------------------- | ------------- |
| GET         | 낙찰상품조회             | /payments/winning-bid-list   | O             |
| POST        | 카카오페이 결제준비      | /payments/kakao-pay-ready    | O             |
| GET         | 카카오페이 결제준비 조회 | /payments/kakao-pay-ready    | O             |
| POST        | 카카오페이 결제승인      | /payments/kakao-pay-approval | O             |
| GET         | 카카오페이 결제승인 조회 | /payments/kakao-pay-approval | O             |
| GET         | 카카오페이 결제취소 조회 | /payments/kakao-pay-cancel   | O             |
| GET         | 카카오페이 결제실패 조회 | /payments/kakao-pay-fail     | O             |
|             |                          |                              |               |

## 반영 브런치

Yohan -> develop

## 기능 추가 사항
---- 
**1. Payments 낙찰상품조회** - GET
- 유저가 대시보드에서 낙찰된 상품을 조회 했을 때 결제가 안된지 2일이 지났을 경우 해당 payment 삭제
	- 결제되지 않은 payment 같은 경우 참조 및 활용성이 없다고 생각해서 삭제로 구현했습니다. 
	- 혹시 다른 의견 있으시면 코멘트 남겨주세요 :)
- 대시보드 조회시 유저가 낙찰된 상품이 있지만 아직 payment db가 만들어 지지 않았을 경우 새로 생성

**2. Payments 카카오페이 결제준비 ** - POST
- 카카오페이 같은 경우 보안상 프론트에서 직접 ajax로 불가능합니다. 프론트에서 프록시를 설정하고 axios로는 가능한거 같지만 현재 프론트가 없는 관계로 쿠키,세션 및 캐싱을 쓰기 어려워 db에 저장하거나 글로벌 변수를 사용해서 해결했습니다.
- 사용자가 결제하려는 payment의 PK 값을 갖고 오기 위해서 글로벌 변수로 딕셔너리를 사용했습니다.
	- 딕셔너리를 사용한 이유는 다음과 같습니다.
		- 현재 개발환경상 토큰을 활용해 유저정보만 갖고 올 수 있는데 유저 값이 핸드폰 번호이므로 중복될 확률이 매우 적다고 생각했습니다.
		  
		  그렇게 생각한 이유는 한 사용자가 결제를 진행할 때 동시에 2개 이상 결제가 불가능한 점 
		  (한 사람이 네이버 페이, 카카오페이의 서로 다른 플랫폼을 동시에 결제하거나 카카오페이로 동시에 2개의 각각 다른 결제건을 결제하는건 불가능
		  
		- 핸드폰 번호를 바꾸지 않는 한 웬만하면 키 값이 겹칠 일 또한 없다고 생각했습니다.
		- 마지막 이유는 딕셔너리를 사용하면 많은 글로벌 변수를 설정하지 않아도 되고 시간복잡도도 O(n) 인점 등 현재 상황에서 제일 적합한 방법이라고 생각해서 딕셔너리로 결정했습니다.
		- 따라서 키 값으로 유저 값(핸드폰 번호) 벨류 값으로 payment의 pk 값을 저장했습니다.


**3. Payments 카카오페이 결제준비  조회** - GET
- 현재 프론트가 없기 때문에 POST와 GET을 따로 구현해야되고 POST를 한 후 GET을 조회해야합니다.
- 사용자가 결제하기 버튼을 눌렀을 경우 카카오 서버에서 결제 링크를 받아오는데 결제링크를 받은 후 API를 조회를 하면 통해 해당 링크를 확인할 수 있습니다.


**4. Payments 카카오페이 결제승인 ** - POST
- 사용자가 정상적으로 결제를 진행했을 경우 해당 API가 호출되는데 프론트가 없어서 쿼리매개변수로 받아오는 pg_token 와 글로벌 변수에 저장했던 유저의 키값을 이용해 payment pk 값을 조회해 결제 승인 부분을 해결했습니다.


**5. Payments 카카오페이 결제승인 조회**
- 이전과 같은 이유로 POST, GET을 따로 구현해야하고 POST가 진행된 뒤 GET 으로 상태조회 가능합니다.



## 결제과정
---- 
### 초기 환경 설정
- 초기설정
	- 모든 앱의 migrations 폴더 안에 `__init__.py`파일 밑에 숫자로 시작하는 파일들 삭제
		- ex) 0001.py
	- db.sqlite3 삭제
	- `python manage.py makemigrations`
	- `python manage.py migrate`
	- `python manage.py createsuperuser`
	- 위 순서로 진행하면 됩니다.
- 그 이후 레디스나 셀러리 서버를 켜주는건 이전과 동일하게 진행하면 됩니다.


![초기환경설정](https://github.com/wodnrP/realtime_auction/assets/103474568/edafc188-d48f-4430-981a-a51c5c622eea)


### 카카오페이 결제 API 사용 과정

#### 상품등록과정 + 채팅과정 (테스트 확인)

![상품등록및채팅과정](https://github.com/wodnrP/realtime_auction/assets/103474568/fcae8868-0122-45f9-b62f-2ea832ab6d33)

#### 결제과정 (테스트 확인)

![결제 과정](https://github.com/wodnrP/realtime_auction/assets/103474568/ad1e62af-8ec9-469d-a349-d4cbd69ae422)

##### 낙찰목록 불러오기

- 사용자가 프론트단에서 낙찰된 상태에서 payment_list (마이페이지에서 결제목록) 을 클릭을 트리거로 실시간으로 결제목록이 업데이트 됩니다. 테스트는 아래와 같습니다.
	- 결제 안하고 시간 초과
	- 결제 안했지만 아직 결제할 시간 남은 경우 (영상에서는 일괄적으로 불러와져서 전부 삭제되지만 실제로는 정상작동합니다)
	- 결제 완료
	- 낙찰자 없이 끝난 경우
- 상품이 삭제될 경우 제품도 삭제되기 때문에 다음과 같은 상황을 생각해봐야 될 것 같습니다.
	- 상대방이 결제를 했을 경우 PROTECT로 payment 데이터를 보호 하던가 최종 결제된 상품은 판매자가 해당 상품을 삭제 할 수 없도록 비공개로 해놔야함

## 프론트 테스트 파일

