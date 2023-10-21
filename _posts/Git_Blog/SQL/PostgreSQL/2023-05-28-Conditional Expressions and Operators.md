---

layout: single
title: "[PostgreSQL] 조건문과 연산자"
categories: SQL
tag: [Python,"[PostgreSQL] 조건문과 연산자"]
toc: true
toc_sticky: true
author_profile: false
sidebar:

---
# Conditional Expressions and Operators

- CASE
- COALESCE
- NULLIF
- CAST
- VIEWS
- Import and Export Functionality

## CASE

- We can use the CASE statement to only excute SQL code when certain conditions are met
- This is very similar to IF/ELSE statements in other programming languages
- There are two main ways to use a CASE statement, either a general CASE or a CASE expression
- Both methods can lead to the same results
- Let's first show the syntax for a "general" CASE


```postgresql
# General Syntax
CASE
	WHEN condition1 THEN result1
	WHEN condition2 THEN result2
	ELSE some_other_result
END

# Simple Example 
SELECT a,
CASE
WHEN a = 1 THEN 'one'
WHEN a = 2 THEN 'two'
ELSE 'other' AS label
END
FROM test;
```


| a   | label |
| --- | ----- |
| 1   | one   |
| 2   | two      |


```postgresql
Example
SELECT customer_id,
CASE
	WHEN (customer_id <= 100) THEN 'Premium'
	WHEN (customer_id BETWEEN 100 and 200) THEN 'Plus'
	ELSE 'Normal'
END
FROM customer

# Example 위에 보다 더 빠름
SELECT customer_id,
CASE customer_id
	WHEN 2 THEN 'Winner'
	WHEN 5 THEN 'Second Place'
END AS raffle_results
FROM customer

# 집계 연산자와 함께 사용하기
SELECT
SUM(CASE rental_rate
	WHEN 0.99 THEN 1
	ELSE 0
END) AS number_of_bargains
FROM film

# 집계 연산자와 함께 사용하기2
SELECT
SUM(CASE rental_rate
	WHEN 0.99 THEN 1
	ELSE 0
END) AS bargains,

SUM(CASE rental_rate
   WHEN 2.99 THEN 1
   ELSE 0
END) AS regular,

SUM(CASE rental_rate
   WHEN 4.99 THEN 1
   ELSE 0
END) AS premium
FROM film

# Practice
SELECT
SUM(CASE rating
   WHEN 'R' THEN '1'
   ELSE 0
END) as r,

SUM(CASE rating
   WHEN 'PG' THEN '1'
   ELSE 0
END) as pg,

SUM(CASE rating
   WHEN 'PG-13' THEN '1'
   ELSE 0
END) as pg13
FROM film

```

## COALESCE

- The COALESCE function accepts an unlimited number of arguments
- It returns the first argument that is not null.
- If all arguments are null, the COALESCE function will return null
	- COALESCE (arg_1, arg_2, ...., arg_n)
- NULL 값 처리 유용함

```postgresql
# Example 
SELECT COALESCE(1, 2)
-> 1
SELECT COALESCE(NULL, 2, 3)
-> 2
```

- Table of Products
	- Price and Discount in Dollars

| Item | Price | Discount |
| ---- | ----- | -------- |
| A    | 100   | 20       |
| B    | 300   | null     |
| C    | 200   | 10         |

```postgresql
SELECT item,(price - discount) AS final
FROM table
```

| item | final |
| ---- | ----- |
| A    | 80    |
| B    | null  |
| C    | 190   |

```postgresql
SELECT item,(price - COALESCE(discount,0))
AS final FROM table
```

| item | final |
| ---- | ----- |
| A    | 80    |
| B    | 300   |
| C    | 190   |
 

## CAST

- The CAST operator let's you convert from one data type into another
- Keep in mind not every instance of a data type can be CAST to another data type, it must be reasonable to convert the data, for example '5' to an integer will work, 'five' to an integer will not

```postgresql
# Syntax for CAST function
SELECT CAST ('5' AS INTEGER)

# PostgreSQL CAST operator 다른 SQL에서는 안됨
SELECT '5'::INTEGER

SELECT CAST(date AS TIMESTAMP)
FROM table

# Practice
SELECT CHAR_LENGTH(CAST(inventory_id AS VARCHAR) FROM rental

```     

## NULLIF

- The NULLIF function takes in 2 inputs and returns NULL if both are equal, otherwise it returns the first argument passed
	- NULLIF(arg1, arg2)
- Example
	- NULLIF(10,10)
		- Returns NULL
	- NULLIF(10, 12)
		- Returns 10
- This becomes very useful in cases where a NULL value would cause an error or unwanted result

```postgresql
CREATE TABLE depts(
first_name VARCHAR(50),
department VARCHAR(50)
)

INSERT INTO depts(
first_name,
department
)
VALUES
('Vinton','A'),
('Lauren','A'),
('Claire','B');

```

| first_name | department |
| ---------- | ---------- |
| Vinton     | A          |
| Lauren     | A          |
| Claire     | B          |

```postgresql
SELECT (
SUM(CASE WHEN department = 'A' THEN 1 ELSE 0 END)/
SUM(CASE WHEN department = 'B' THEN 1 ELSE 0 END)
) AS department_ratio
FROM depts

-> 2/1 = 2

# Claire 퇴사
DELETE FROM depts
WHERE department = 'B'

SELECT (
SUM(CASE WHEN department = 'A' THEN 1 ELSE 0 END)/
	NULLIF(
	SUM(CASE WHEN department = 'B' THEN 1 ELSE 0 END), 0)
) AS department_ratio
FROM depts

-> 에러가 나지 않고 Null 로 값이 나옴
```

## VIEWS

- Often there are specific combinations of tables and conditions that you find yourself using quite often for a project
- Instead of having to perform the same query over and over again as a starting point, you can create a VIEW to quickly see this query with a simple call
- A view is a database object that is of a stored query
- A view can be accessed as a virtual table in PostgreSQL
- Notice that a view does not store data physically, it simply stores the query.

```postgreSql
# practice
CREATE VIEW customer_info AS
SELECT first_name, last_name, address FROM customer
INNER JOIN address
ON customer.address_id = address.address_id

# 
SELECT * FROM customer_info

# FIX info
CREATE OR REPLACE VIEW customer_info AS
SELECT first_name, last_name, address, district FROM customer
INNER JOIN address
ON customer.address_id = address.address_id

# DELETE view
DROP VIEW IF EXISTS customer_info

# CHANGE VIEW name
ALTER VIEW customer_info RENAME TO c_info
```


## Importing and Exporting Data
- In this lecture we will explore the Import/Export functionality of PgAdmin, which allows us to import data from a.csv file to an already existing table
- There are some important notes to keep in mind when using Import/Export

>Important Note
>	Not every outside data file work, variations in formatting, macros, data types, etc. may prevent the Import command from reading the file, at which point, you must edit your file to be compatible with SQL
>	You MUST provide the 100% correct file path to your outside file, otherwise the Import command will fail to find the file.
>	The most common mistake if failing to provide the correct file path, confirm the file's location under its properties

[Doc](https://www.postgresql.org/docs/current/sql-copy.html)

>VERT Important Note
>	The import command DOES NOT create a table for you
>	It assumes a table is already created
>	Currently there is no automated way within pdAdmin to create a table directly from a.csv file