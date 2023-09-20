---
layout: single
title: "프로그래머스 - [카카오] - 후보키 (알고리즘)"
categories: Algo
tag: [Java,Python,JavaScript,Lv_2,"[카카오] - 후보키"]
toc: true
toc_sticky: true
author_profile: false
sidebar:

---
[문제링크](https://school.programmers.co.kr/learn/courses/30/lessons/42890?language=python3)

```python
from itertools import combinations

def solution(relation):
    row = len(relation)
    col = len(relation[0])

    combo = []
    for i in range(1, col+1):
        combo.extend(combinations(range(col), i))
        
    unique = []
    for i in combo:
        tmp = [tuple([item[key] for key in i]) for item in relation]

        if len(set(tmp)) == row:  
            put = True
            
            for x in unique:
                if set(x).issubset(set(i)):
                    put = False
                    break
                    
            if put: unique.append(i)
    return len(unique)
```

https://velog.io/@sem/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EC%8A%A4-LEVEL2-%ED%9B%84%EB%B3%B4%ED%82%A4-Python