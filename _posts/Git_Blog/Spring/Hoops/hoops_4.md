---
layout: single
title: "[Hoops PJ] 트러블 슈팅 & TIL (1)"
categories: Spring_Project_Hoops
toc: true
toc_sticky: true
author_profile: false
sidebar: 
tags:
  - TIL
  - 개발스터디
  - 99클럽
  - 99일지
  - 항해
---

# GET -> 동적 쿼리 어떻게 쓸까?
## 정규화!

데이터를 효율적으로 사용하기 위해 데이터에 대한 평탄화 작업이 필요하며 이것이 정규화다.   

### 이상현상 (anormaly)

- 정규화를 거치지 않은 데이터베이스에서 발생할 수 있는 현상
- 갱신,삽입,삭제 이상이 있다.

### 정규화 목적

- 이상 발생 방지
- 무결성 유지
- 효율적인 검색
- 결론은 휴먼에로 최소화

## 첫 번째 평탄화

- 1NF : 도메인이 원자 값이어야 한다. (list 이런거 쓰지 말란 얘기)
- 데이터베이스에서 도메인은 문자, 숫자 등의 자료형을 말한다, 도메인 주도설계에서 말하는 도메인은 데이터베이스의 스키마급 개념이다.

| 데이터베이스   | 객체지향프로그래밍 | 비고                                |
| -------- | :-------- | --------------------------------- |
| 스키마      | 도메인       | 스키마는 데이터를 위한 데이터. 메타데이터로 표현       |
| 속성(컬럼)   | 필드        | 데이터베이스에는 '행위' 메서드라는 개념이 없다.       |
| 도메인      | (원시) 자료형  | 문자, 숫자, 불린등                       |
| 엔티티(테이블) | 데이터객체     | 필드로 구성된 클래스를 말한다<br>(JPA의 entity) |
#### 정규화 전 

![](https://i.imgur.com/ACzrIaQ.png)

#### 정규화 후 

![](https://i.imgur.com/XgemiUr.png)

## 두 번째 평탄화

- 2NF : 부분적 함수 종속성 제거
- 복함키에서 후보키에 종속되어있는 경우 별도 테이블로 분리해준다.
- 정규화 후 두 테이블은 자연스럽게 식별 관계가 된다.

#### 정규화 전

![](https://i.imgur.com/f5xsbDI.png)


#### 1차 정규화 

![](https://i.imgur.com/rNEIcwx.png)

- 담당교수는 수강과목에 종속적이고 수강과목은 후보키다

#### 2차 정규화

![](https://i.imgur.com/AzwaRBt.png)

자연스럽게 식별관계로 연결되었고, 수강과목과 학생과의 다대다 관계가 해소되었다.
- M : M -> 1:M , M:1

## 세 번쨰 평탄화

- 3NF : 종속적 (이행적) 함수 종속성 제거
- a -> b , b -> c 인경우 a -> c가 성립하는 경우
- 정규화 후 두 테이블은 비식별 관계가 된다.

### 정규화 전

![](https://i.imgur.com/8PMk0if.png)

학생은 학과에 종속되고 학과는 단과대에 종속됨

![](https://i.imgur.com/x45WRd4.png)


### 분리

![](https://i.imgur.com/rxhnMBu.png)

- 학과를 기준으로 단과대를 넣을 수 있지만 단과대를 기준으로 넣어야한다면?
- 공과대는 검색 결과가 2개임 그래서 단과대 컬럼과 학과 두개 컬럼이 다 필요함 이런 복합키는 조인할 때 귀찮다고 함 그래서 학과 코드라는 대체키 하나로 관리

![](https://i.imgur.com/edncGXp.png)

- 2차 정규화는 식별 관계
- 3차 정규화는 비식별관계
- 학과코드는 학생현황에 키로 참여하고 있지 않음 -> 비식별 관계
## 조금 힘든 평탄화

- BC(Boyce-Codd)NF : 결정자이면서 후보키가 아닌 것 제거
- 마스터 코드 관리 테이블 정도로 표현하게 된다.
- ERD를 그릴 때 마스터 코드는 릴레이션을 굳이 표시하지 않는다.


![](https://i.imgur.com/H8OaNs4.png)

## 평탄화를 하지 말아야 한다.


<iframe width="560" height="315" src="https://www.youtube.com/embed/68o6mQOewF8?si=rUnixrZhtCFCd2zy" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

https://multifrontgarden.tistory.com/181
