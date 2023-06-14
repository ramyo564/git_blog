---

layout: single
title: "[PostgreSQL] Advanced command"
categories: SQL
tag: [Python,"[PostgreSQL] 고급명령어"]
toc: true
toc_sticky: true
author_profile: false
sidebar:

---
## 고급명령어
- Timesetamps and EXTRACT
- Math Fucntions
- String Functions
- Sub-query
- Self-Join

## Timesetamps and EXTRACT
- TIME - Contains only time
- DATE - Contains only date
- TIMESTAMP - Contains date and time
- TIMESTAMPTZ - Contains date,time, and timezone

```postgresql
SHOW ALL
SHOW TIMEZONE
SELECT NOW()
SELECT TIMEOFDAY()
SELECT CURRENT_TIME
SELECT CURRENT_DATE
```

- EXTRACT()
	- YEAR,MONTH,DAY,WEEK,QUARTER
	- EXTRACT(YEAR FROM date_col)
	- 년도,월,일 등을 추출함
- AGE()
	- 해당 타임스탬프 내에서 현재까지의 시기를 계산해서 알려줌
	- AGE(date_col) -> returns = 13 years 1 mon 5 days 01:32.13.003432 이런식
- TO_CHAR()
	- 글자로 바꿔주는 함수
	- TO_CHAR(date_col, 'mm-dd-yyyy')
- [데이터 포맷 참고](https://www.postgresql.org/docs/current/functions-formatting.html)
- [데이터 포맷 참고2](https://www.postgresql.org/docs/current/functions-datetime.html)
```postgresql
SELECT EXTRACT (YEAR FROM payment_date) AS year
FROM payment;

SELECT AGE(payment_date)
FROM payment;

SELECT TO_CHAR(payment_date,'MONTH-YYYY')
FROM payment;
```

> 특정 요일에 발생한 건수를 뽑는데 이럴 때는 TO_CHAR 가 아닌 EXTRACT를 사용해야한다.
> 	월요일에 월급 발생 횟수를 구할 때

```postgresql
SELECT COUNT(payment) FROM payment
WHERE EXTRACT(DOW FROM payment_date) = 1
```

> 밑에 처럼 구하면 값이 없다 TO_CHAR 는 단순히 변환만 하는거고 변환한 값을 따로 조건을 붙이는게 불가능하다.

```postgresql
SELECT COUNT(payment) FROM payment
WHERE TO_CHAR(payment_date, 'DAY') = 'MONDAY'
# 이런식으로는 사용이 안됨
```

## 수학 연산자
- [수학연산자](https://www.postgresql.org/docs/current/functions-math.html)
- 로그, 삼각함수등 정말 다양하게 사용가능

```postgresql
SELECT ROUND(col_1/col_2, 2) * 100 AS new_name FROM table
```

## 문자열 함수와 연산자
- [문자열 함수 & 연산자](https://www.postgresql.org/docs/current/functions-string.html)
- 형변환, 문자 추출, 문자 길이,문자 합치기등 다양하게 사용가능

```postgresql
# 문자 길이 추출
SELECT LENGTH(col_1) FROM table 

# 이름 합치기
SELECT upper(first_name) || '' || upper(last_name) AS full_name
FROM customer

# 응용
SELECT LOWER(LEFT(first_name,1)) || LOWER(last_name) || '@gmail.com'
AS custom_email
FROM customer
```


## 서브 쿼리
- 서브 쿼리를 쓰면 더 복잡한 쿼리를 만들 수 있다.
- 다른 쿼리의 결과에 대한 쿼리를 실행하거나 다른 쿼리의 결과를 사용할 수 있다.
- 구문은 상대적으로 간단하다.

### 예시
```postgresql
# 학생들의 이름과 성적
SELECT student,grade FROM test_socres 

# 반 평균 점수
SELECT AVG(grade) FROM test_socres

# 반 평균보다 높은 학생
-- 괄호 안에 서브 쿼리가 먼저 작동한다--

SELECT student,grade FROM test_scores
WHERE grade > (SELECT AVG(grade)
FROM test_scores)


SELECT student,grade FROM test_scores
WHERE student IN
(SELECT student FROM honor_roll_table) 

```
----

- EXISTS 오퍼레이터는 서브 쿼리에서 행의 존재를 테스트하는데 사용한다.
- 보통 서브쿼리가 EXISTS 함수 뒤 괄호에 입력되어, 어떤 행이 서브 쿼리로 도출 되었는지 확인한다.
- EXISTS는 IN 보다 일반적으로는 빠르다 (EXISTS는 ROW의 존재 유무만 체크, IN은 값을 비교하면서 체크)
### 예시
```postgresql
SELECT column_name
FROM table_name
WHERE EXISTS
(SELECT column_name FROM
table_name WHERE condition)


# 서브쿼리
SELECT title, rental_rate
FROM film
WHERE rental_rate >
(SELECT AVG(rental_rate) FROM film)

# 서브 쿼리의 결과가 다양하면 IN 오퍼레이터를 사용해야한다.
SELECT film_id, title
FROM film
WHERE film_id IN
(SELECT inventory.film_id)
FROM rental
INNER JOIN inventory, ON inventory.inventory_id = rental.inventory_id
WHERE return_date BETWEEN '2005-05-29' AND '2005-05-30')
ORDER BY film_id
-- 위와 같은 경우 영화 제목을 갖고 오고 싶은데 rental 테이블에는 영화 제목이 없는 상태다 따라서 film 테이블과 합친 다음 5월 29일부터 5월 30일 사이에 대여된 필름 아이디로 제목을 찾는 과정인데 이런 상황에서는 IN이 적합하다.

# EXISTS 
SELECT first_name,last_name
FROM customer AS c
WHERE NOT EXISTS
(SELECT * FROM payment as p
WHERE p.customer_id = c.customer_id
AND amount > 11)
--11 이하의 금액을 지급한 고객을 찾는 상황
```

## 셀프조인
- 표 자체에 합쳐져 있는 쿼리를 셀프 조인이라고 한다.
- 셀프 조인은 같은 표에 여러 열의 여러 값을 비교할 때 유용하다.
- 셀프 조인은 같은 표의 두 복사본 합처럼 보이지만 사실 복사된 게 아니다.
- 셀프 조인은 특별한 키워드가 없다.
- AS 를 사용해야 애매모호하지 않음

```postgresql
SELECT tableA.col, tableB.col
FROM table AS tableA
JOIN table AS tableB 
ON tableA.some_col = tableB.other_col


# 상영시간 길이가 같은 영화들을 찾을 때
SELECT fi.title, f2.title, f1.length
FROM film AS f1
INNER JOIN film AS f2 ON
f1.film_id != f2.film_id
AND f1.length = f2.length
```

