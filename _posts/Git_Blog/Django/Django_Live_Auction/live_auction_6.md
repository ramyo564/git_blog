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


#### 디폴트 결과 값

![디폴트 결과 값](https://github.com/wodnrP/realtime_auction/assets/103474568/3cfbc7da-367b-4696-9d3b-ba8872a53dae)


#### 키워드 검색

![키워드 적용](https://github.com/wodnrP/realtime_auction/assets/103474568/6e7cfc6e-cedb-4ddc-b5bd-26dfab8f390d)


#### 카테고리 적용

![카테고리 적용](https://github.com/wodnrP/realtime_auction/assets/103474568/99cf5c09-2441-41a8-9bde-1dfc82e39434)



## 어드민 패널 썸네일 적용

![어드민 패널](https://github.com/wodnrP/realtime_auction/assets/103474568/4d338d12-b95a-4e9f-9b6c-35008543ba35)






