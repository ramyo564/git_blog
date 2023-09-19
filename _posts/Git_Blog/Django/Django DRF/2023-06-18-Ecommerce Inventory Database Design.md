---
layout: single
title: " [Django DRF] Ecommerce inventory Database Design"
categories: Django_DRF_Practice
tags:
  - Python
  - Project
  - eCommerce
  - RESTful
  - API
  - Ecommerce
  - Inventory
  - Database
  - Design
  - Django
toc: true
toc_sticky: true
author_profile: false
sidebar:
---

# Database Design?

## Preliminary Table List

![](https://i.imgur.com/kHfhTJW.png)


- Primary field list
	- How or what tables are going to be needed within our database
		- ex) name, age, address
- List of subjects
- Mission objectives
	- What's the objective off our application?
	- What does our application need to do?
	- What data does it need?
- System requirements
	- What exactly does our application do?
	- What data does it generate?
	- What data does it require in order to work correctly?

## Multivalued Fields

![](https://i.imgur.com/ezKOFZW.png)

한 개의 제품에 여러가지 옵션이 달릴 경우 테이블을 나눠준다.

| ProdPrice | ProdImage      | ProdSKU   | ProdQty | ProdColor       | ProdSize |
| --------- | -------------- | --------- | ------- | --------------- | -------- |
| $100      | img1,img2,img3 | PR9102120 | 100     | Blue,Red,Yellow | 10,11,12         |

하지만 이렇게 할 경우 몇몇 필드는 위와 같이 다중 값을 가진다.

| ProdPrice | ProdImage      | ProdSKU   | ProdQty | ProdColor       | ProdSize | 
| --------- | -------------- | --------- | ------- | --------------- | -------- | 
| $100      | img1 | PR9102120 | 100     | Blue,Red,Yellow | 10,11,12 |     
| $100      | img2 | PR9102120 | 100     | Blue,Red,Yellow |          |    
|  $100      | img3 | PR9102120 | 100     | Blue,Red,Yellow          |                                |          |     |     |     |     |

이렇게 데이터 베이스를 만들 경우 많은 중복 데이터들이 생기며 나중에 문제가 생기기 쉽다.
예를 들어 이 제품의 수량을 업데이트 한다면 다른 라인도 영향을 받지만 동일한 제품이며 이미지만 다르게 갖고 있을 뿐이다.
데이터 베이스에서는 중복 데이터를 일반적으로 피하는게 좋다.

데이터베이스를 설계하는 목적은 데이터를 한 번만 입력하도록 하는 것이다.

| ProdPrice | ProdImage A | ProdImage B | ProdImage C | ProdSKU  | ... |
| --------- | ----------- | ----------- | ----------- | -------- | --- |
| $100      | img1        | img2        | img3        | PR801212 |     |
| $200      | img1        |         |         | PR212212         |     |

> 테이블을 평탄화 -> 더 많은 필드를 추가한다.

이렇게 할 경우 새로운 이미지가 더 필요한 제품 라인을 추가한다면 문제가 발생할 수 있다 또한 null 값이 문제로 생길 수 있다.

![mfrVjPx.png](https://i.imgur.com/mfrVjPx.png)

| ProdPrice | ProdImage | ProdSKU   | ProdQty | ProdColor       | ProdSize |
| --------- | --------- | --------- | ------- | --------------- | -------- |
| $100      | img1      | PR9102120 | 100     | Blue,Red,Yellow | 10,11,12 |
| $100      | img2      | PR9102120 | 100     | Blue,Red,Yellow | 10,11,12 |
| $100      | img3      | PR9102120 | 100     | Blue,Red,Yellow | 10,11,12 |

> 새로운 테이블 만들기

ProdColor 와 ProdSize를 새로운 테이블로 만든다

여기서 다시 분리해서 새로운 테이블을 만들어준다.

| AttName | AttValue        |
| ------- | --------------- |
| Color   | red,blue,yellow |
| Size    | 10,11,12                |

다중 값이 있는 부분을 다시 세분화 한다.

| AttValue |     |
| -------- | --- |
| red      |     |
| blue     |     |
| yellow   |     |

![](https://i.imgur.com/25jYuRO.png)


이렇게 새 테이블을 만드는게 더 합리적인 방법이다.
물론 데이터에 따라 다르다.

### Field Checklist

- Represents a distinct characteristic of the subject of the table
- Contaion only a single value
- Cannot be deconstructed into smaller components
- Should not contaion calculated values
- Is a unique characteristic

## Table and Field Naming Conventions

![](https://i.imgur.com/68uArE7.png)


일반적으로 테이블은 고유의 이름을 갖고 있어야 한다.
다른 작업자들이 봐도 테이블 이름만 갖고도 내부의 데이터를 쉽게 파악할 수 있게 네이밍 해야한다.
장고 쿼리셋에서는 알아서 해주기 때문에 SQL에서 사용하는
prefix 작업이 필요 없다 (ex: prod_name)

### Table Naming Guidelines

- Unique(identifies only one subject)
- Meaningful / Descriptive
- Identifies the subject / entity of the table
- Minimum number of words possible
- Avoid acronyms and abbreviations
- Avoid restristing data entry
- Avoid plural names

## Keys

![](https://i.imgur.com/I3GrVTF.png)

| name      | description   | type | 
| --------- | ------------- | ---- | 
| product_1 | This is a ... | dl   |   
| product_1 | This is a ... | dl   |   
| product_1 | This is a ... | dl   |     

장고에서는 migrate 를 진행할 때 자동으로 Primary key를 할당해준다.
이 pk 를 통해 이전에 분리해둔 테이블도 기존 테이블에 접근이 가능하다.

### Primary Key Guidelines

- Unique
- Must not be multipart
- Is not an optional value
- Excusively identfy the record


## Field Specifications

| id  | name      | description   | type |
| --- | --------- | ------------- | ---- |
| 1   | product_1 | This is a ... | dl   |
| 2   | product_1 | This is a ... | dl   |
| 3   | product_1 | This is a ... | dl   |

[Django field types](https://docs.djangoproject.com/en/4.2/ref/models/fields/)

![](https://i.imgur.com/GNW8jed.png)

### Field Specification (General)
- Name
- Description

### Field Specification (Physical Attributes)
- Data Type
- Length
- Decimal Places
- Character types (Letter, Number, Special)

### Field Specification (Logical Attributes)
- Key Type (Primary, Foreign, Non)
- Uniqueness (Unique / Non-Unique)
- Null Support
- Imput by (User, System)
- Required
- Default
- Value Range
- Editing Rules

## Table Realationships

### one to one
일대일 관계에서는 한 테이블의 레코드가 다른 테이블의 정확히 하나의 레코드와 연관된다.    이 관계는 두 테이블 간에 공통 키를 사용하여 설정한다. 일대일 관계의 특징은 다음과 같다:   

- A 테이블의 각 레코드는 최대 하나의 B 테이블 레코드와 연관된다.
- B 테이블의 각 레코드는 최대 하나의 A 테이블 레코드와 연관된다.
- 관계를 설정하는 공통 키는 일반적으로 두 테이블 모두에서 주 키 또는 고유 키로 사용된다.

예시: "Person"(사람)과 "Passport"(여권)라는 두 개의 테이블이 있다고 가정해보자   
각 사람은 하나의 여권만 가지며, 각 여권은 단 하나의 사람과 연관된다.    
"Person" 테이블에는 "Passport" 테이블을 참조하는 외래 키 열이 있으며, 그 반대로 "Passport" 테이블에도 외래 키 열이 있다.   

### one to many
일대다 관계에서는 한 테이블의 레코드가 다른 테이블의 하나 이상의 레코드와 연관된다. 그러나 반대로는 성립되지 않는다.    
이 관계는 "다(many)"측 테이블에 주 키를 참조하는 외래 키를 사용하여 설정된다. 일대다 관계의 특징은 다음과 같다:   

- A 테이블의 각 레코드는 B 테이블의 하나 이상의 레코드와 연관된다. 
- B 테이블의 각 레코드는 최대 하나의 A 테이블 레코드와 연관된다.
- 관계를 설정하기 위해 외래 키는 "다(many)"측 테이블에 배치된다.

예시: "Department"(부서) 테이블과 "Employee"(직원) 테이블을 생각해보자.   
한 부서에는 여러 직원이 속할 수 있지만, 한 직원은 하나의 부서에만 속한다.   
"Employee" 테이블에는 "Department" 테이블의 주 키를 참조하는 외래 키 열이 있다.   

### many to many
다대다 관계에서는 한 테이블의 여러 레코드가 다른 테이블의 여러 레코드와 연관된다.    
이 관계는 보통 중간 테이블인 연결 테이블 또는 조인 테이블을 사용하여 설정된다. 다대다 관계의 특징은 다음과 같다:   

- A 테이블의 각 레코드는 B 테이블의 하나 이상의 레코드와 연관된다.   
- B 테이블의 각 레코드는 A 테이블의 하나 이상의 레코드와 연관된다.   
- 이 관계는 두 테이블의 주 키를 참조하는 외래 키를 포함하는 연결 테이블을 생성하여 구현된다.

예시: "Student"(학생)과 "Course"(과목)라는 두 개의 테이블을 생각해보자.    
한 학생은 여러 과목에 등록할 수 있고, 한 과목에는 여러 학생이 있을 수 있다.    
이 관계를 표현하기 위해 "Enrollment"(수강)이라는 연결 테이블을 생성하고, 이 테이블에는 "Student"와 "Course" 테이블의 주 키를 참조하는 외래 키 열이 포함된다.   

### 정리

![](https://i.imgur.com/rlBT4kh.png)

예를 들어서 카테고리 하나당 상품이 한 개만 있을 수도 있고 여러개가 있을 수도 있다.
반대로 말하면 여러개의 상품이 하나의 카테고리랑 관련이 있을 수 있다.

- 상황에 따라 다르지만 예를 들어서 키보드라는 상품은 전자제품 카테고리에 들어갈 수도 있고 컴퓨터 부품 카테고리등 중복 카테고리를 허용한다고 치자.
- 이럴 경우 키보드는는 2개 이상의 카테고리를  갖을 수 있으며 전자제품 또는 컴퓨터 부품 카테고리 또한 여러개의 상품에 포함 될 수있다.
- 이럴 경우 many to many다.

표로 정리하면 아래와 같다.    

| -     | Product | Category |
| ----- | ------- | -------- |
| -     | 1       | M        |
| -     | M       | 1        |
| Final | M       | M        |

물론 카테고리 중복을 허용하지 않으면 아래와 같이 될 것이다.

| -     | Product | Category |
| ----- | ------- | -------- |
| -     | 1       | 1        |
| -     | M       | 1        |
| Final | M       | 1        |


![](https://i.imgur.com/pyI6alk.png)

좀 더 정확한 예를 들어서 Product -> 운동화A 모델이 있다고 가정한다고 치자.   
또한 이 모델은 Product LIne -> 빨간색 파란색이라고 한다면 빨간색 파란색마다 모델명이 다를 것이고 수량도 다를 것이다.    
이럴 경우 one to many 관게가 된다.
테이블 관계를 정리하면 아래와 같다.

### 테이블 관계 정리

![](https://i.imgur.com/xR1VnpV.png)

