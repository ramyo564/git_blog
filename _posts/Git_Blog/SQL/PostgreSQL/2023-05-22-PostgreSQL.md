---

layout: single
title: "[PostgreSQL] Basic SQL"
categories: SQL
tag: [Python,"[PostgreSQL] 기초 구문"]
toc: true
toc_sticky: true
author_profile: false
sidebar:

---
# 기초 SQL

하도 관계형 데이터베이스 나와서 한 번 쭉 봤는데
매우 초 간단 버전 판다스 느낌

## SELECT 문

```SQL
SELECT column_name FROM table_name;
```
1. 모든 열을 보고 싶다면 `*` 를 사용하면 된다.
2. 컬럼 여러 개를 선택할 때 순서는 상관 없다.  -> `SELECT column_name_3, column_name_1, column_name_4  FROM table_name;`
3. 가독성 측면에서 쿼리가 끝날 때 ; 를 붙여주고 대문자를 사용하는 게 좋다.


## SELECT DISTINCT 중복 값 제거
```sql
SELECT DISTINCT(column_name) FROM table_name;
```
- 괄호는 없어도 상관 없음
``

## COUNT
```sql
SELECT COUNT(*) FROM table;
SELECT COUNT(name) FROM table;
SELECT COUNT(DISTINCT name) FROM table;
```
- 중첩할 때는 괄호를 사용하자

## WHERE
- 조건문을 사용할 때 WHERE를 사용한다.
- 크거나 작을 때 혹은 특정 값등에 이용가능하다.

| Name   | Choice |
| ------ | ------ |
| Zeah   | Green  |
| Davicd | Green  |
| Claire | Yellow |
| David  | Red    |


```sql
SELECT name,choice FROM table WHERE name = 'David;
```

쿼리문을 실행하면 아래의 결과가 나온다.
여기서는 대문자 표기가 중요하다.

| Name  | Choice |
| ----- | ------ |
| David | Green  |
| David | Red    |


```postgresql
SELECT name,choice FROM table WHERE name = 'David' AND choice ='Red;
```

쿼리문을 실행하면 아래의 결과가 나온다.

| Name  | Choice |
| ----- | ------ |
| David | Red    |

## ORDER BY
```postgresql
SELECT column_1, column_2 FROM table ORDER BY column_1 ASC/DESC;
```
- ORDER BY 뒤에 컴럼 순서대로 정렬됨 
	- ORDER BY 숫자 열, 알파벳 열 -> 숫자로 먼저 정렬되고 그 다음 알파벳 순으로 정렬됨

## LIMIT
- 쿼리에 대해 반환되는 행의 개수를 제한할 수 있는 명령어
- 이는 테이블의 모든 행을 반환하는 게 아니라 상위 몇 개의 행만 표시하여 테이블 레이아웃을 파악하려고 할 때 쓴다.
- LIMIT 은 쿼리 요청의 가장 아래로 내려가며 가장 마지막에 실행되는 명령이다.

```postgresql
SELECT * FROM payment ORDER BY payment_date DESC LIMIT 5;
```

## BETWEEN
- 값을 값 범위와 비교할 때 사용 (a 와 b 사이의 값을 조회)
- 날짜 정보는 0:00 부터 시작
```postgresql
SELECT * FROM payment WHERE payment_date BETWEEN '2007-02-01' AND '2007-02-15';
```

## IN
```postgresql
SELECT color FROM table WHERE color IN ('option_A','option_B');
SELECT color FROM table WHERE color NOT IN ('option_A','option_B');
```

## LIKE , ILIKE, NOT LIKE
- LIKE 연산자를 사용하면 문자열에 대한 패턴 매칭을 수행할 수 있다.
- ILIKE는 대 소문자를 가리지 않는다.
- % 또는 _ 로서 단 하나의 문자하고만 매칭된다.
	- All names that begin with an 'A'
		- WHERE name LIKE 'A%'
	- All names that end with an 'a'
		- WHERE name LIKE '%a'
	- WHERE value LIKE 'Version#__'
		- format is 'Version#A4', 'Version#B7'
- `WHERE name LIKE '_her%'`
	- C**her**yl
	- T**her**esa
	- S**her**ri
		- her 앞에는 한 문자만 오지만 her 뒤에는 문자 제한이 없다.
		- 또한 공백이 와도 상관 없다.
- [Pattern 더 자세히 보기](https://www.postgresql.org/docs/15/functions-matching.html)





