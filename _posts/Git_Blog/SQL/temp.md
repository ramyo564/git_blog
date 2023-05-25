---

layout: single
title: "[PostgreSQL] "
categories: SQL
tag: [Python,"[PostgreSQL] "]
toc: true
toc_sticky: true
author_profile: false
sidebar:

---
## **파이썬의 클래스**



```postgresql
# 1번
SELECT * FROM cd.facilities

# 2번 
SELECT name,membercost FROM cd.facilities

# 3번
SELECT name,membercost FROM cd.facilities limit 5

# 4번
문제가 뭔소린지 모르겠음 

# 5번
SELECT * FROM cd.facilities
WHERE name like '%Tennis%'

# 6번
SELECT * FROM cd.facilities
WHERE name like '%2'

# 7번
SELECT memid,surname,firstname,joindate FROM cd.members
WHERE EXTRACT(month FROM joindate) = 9
-- TO_CHAR 는 데이터 조작 불가한 듯

# 8번
SELECT DISTINCT(surname) FROM cd.members 
ORDER BY surname LIMIT 10 

# 9번
SELECT joindate as Result FROM cd.members 
ORDER BY joindate desc LIMIT 1 
-- 정확히 모르겠음 뭘 원하고 어떤 방법을 원하는지 모르겠음
-- 

# 10번
SELECT COUNT(*) FROM cd.facilities
where guestcost > 10

# 11번
SELECT distinct(facid) FROM cd.bookings
WHERE EXTRACT(month from starttime) = 9

```
