---
layout: single
title: " [Django DRF] 실시간 경매 live auction (3)"
categories: Django_Live_Auction
tags:
  - Python
  - Project_Live_Auction
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# Product API

| HTTP method | 기능               | end-point                          | auth required |
| ----------- | ------------------ | ---------------------------------- | ------------- |
| GET         | 경매상품조회           | /products/all-products             | X             |
| POST        | 경매등록           | /products/new-product              | O             |
| DELETE      | 경매삭제           | /products/<str:pk>delete           | O             |
| POST        | 이미지등록         | /products/upload-images            | O             |
| GET         | 경매상품이미지조회 | /products/images/<str:products_id> | O             |
|             |                    |                                    |               |

## 반영 브런치

Yohan -> develop

## 기능 추가 사항
---- 
### requirements.txt 업데이트

- 라이브러리 업데이트 필요


**1. Products 경매상품조회** - GET

- /products/all-products 
- 검색기능 추가 (트라이 자료 구조)
- 카테고리 검색 필터링 (트라이 자료 구조)
- 페이지 네이션 적용
- 경매 종료가 얼마 남지 않은 순서대로 정렬
- 경매가 종료되지 않은 상품만 조회
- 경매 낙찰 후 내용 변경을 방지하기 위해 상품수정이 없음
- 이미지 URL 시리얼라이저에 추가 (여러장일 경우 모두 출력)

**2. Products 경매등록** - POST

- /products/new-product 
- 사용자 jwt로 인증 및 권한부여
- 인증된 사용자는 상품 등록 가능
	- 경매 낙찰된 후 혹은 경매진행 당시 내용 변경 방지를 위해 PUT,PATCH 기능 제외
	- 내용 수정이 필요한 경우 진행 중인 경매를 삭제후 재등록 해야함

**3. Products 경매삭제** - DELETE

- /products/<str:pk>delete    
- 본인 경매상품만 삭제 가능
- 경매가 종료되지 않았을 때만 삭제 가능

**4. Products 이미지 등록** - POST

- /products/upload-images
- 인증된 사용자 + 등록된 상품에 이미지 등록
- 로컬환경으로 구현


**5. Products 모델 리팩토링**

- 자동 경매 시작 시점 함수 구현에서 디폴트로 변경
	- 경매상품 등록하면 등록 시점부터 active 
		- 등록 후 3일 뒤 자동으로 active false
- product_content 필드 CharField -> TextField() 변경
 

**6. Categories 모델 리팩토링**

- 유지보수 용이성을 위해 카테고리를 트리 자료 구조로 변경
- 트리구조로 변경시 CategoryItem 모델은 필요 없으므로 삭제


**7. ProductImages 모델 어드민 패널 수정**

- 어드민 패널에서 썸네일로 이미지 확인 기능 추가


## 검색 기능 및 카테고리 필터링 결과 값
---- 
### /products/all-products  

#### 디폴트 결과 값

![디폴트 결과 값](https://github.com/wodnrP/realtime_auction/assets/103474568/3cfbc7da-367b-4696-9d3b-ba8872a53dae)


#### 키워드 검색

![키워드 적용](https://github.com/wodnrP/realtime_auction/assets/103474568/6e7cfc6e-cedb-4ddc-b5bd-26dfab8f390d)


#### 카테고리 적용

![카테고리 적용](https://github.com/wodnrP/realtime_auction/assets/103474568/99cf5c09-2441-41a8-9bde-1dfc82e39434)



## 어드민 패널 썸네일 적용

![어드민 패널](https://github.com/wodnrP/realtime_auction/assets/103474568/4d338d12-b95a-4e9f-9b6c-35008543ba35)



## 팀원들에게 받은 코드 리뷰 부분




