---
layout: single
title: "[백준 2745번 진법 변환] (알고리즘)"
categories: Algo
tags:
  - Python
  - 백준
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
## 문제

B진법 수 N이 주어진다. 이 수를 10진법으로 바꿔 출력하는 프로그램을 작성하시오.

10진법을 넘어가는 진법은 숫자로 표시할 수 없는 자리가 있다. 이런 경우에는 다음과 같이 알파벳 대문자를 사용한다.

A: 10, B: 11, ..., F: 15, ..., Y: 34, Z: 35

### 입력

첫째 줄에 N과 B가 주어진다. (2 ≤ B ≤ 36)

B진법 수 N을 10진법으로 바꾸면, 항상 10억보다 작거나 같다.

### 출력

첫째 줄에 B진법 수 N을 10진법으로 출력한다.

### 예제 입력 1 복사

ZZZZZ 36

### 예제 출력 1 복사

60466175


## 접근법

- 해시맵으로 하면 되지 않을까 생각했다.
- 처음에 10진법으로 변환 한다는 게 무슨 소리인지 몰라서 찾아봤는데
- ![](https://i.imgur.com/iHz8v6h.png)
- 위와 같다 
- 그럼 문제를 풀어보자!
## 구현 코드

```python

import sys

dic = {chr(65 + i): 10 + i for i in range(26)}

a, n = map(str,sys.stdin.readline().split())

length = len(a)-1

answer = 0

for i in a:
    value = dic[i]
    answer += value * (int(n) ** length)
    length -=1

print(answer)
        
    
```

- 정답은 나오는데 런타임 오류가 난다. 뭐지?
- 귀찮아서 딕셔너리를 만든건데....
- 아무리 생각해도 접근 방식에는 문제가 없는 것 같았는데 힌트를 얻기 위해서 다른 사람이 풀은 방식을 봤다

```python
import sys

c = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ"

n, b = sys.stdin.readline().split()
ans = 0

s = 0

for i in reversed(n):
    ans += (c.index(i))*(int(b)**s)
    s+=1

print(ans)

```

- 문제를 잘못 이해 했다.. 10을 넘어가면 알파벳으로 변환 되는 거지 10 이하는 숫자로 표기된다.
- 시간 복잡도 같은 경우는 큰 문제가 없으니 숫자 부분만 처리해주면 될거라고 생각!

```python
import sys

dic = {chr(65 + i): 10 + i for i in range(26)}

a, n = map(str,sys.stdin.readline().split())

length = len(a)-1

answer = 0

for i in a:
    if i in dic:
        value = dic[i]
        answer += value * (int(n) ** length)
        length -=1
    else:
        answer += int(i) * (int(n) ** length)
        length -=1
print(answer)
```
## 시간 복잡도와 공간 복잡도

시간 복잡도:

1. 입력값 `a`의 길이를 `L`이라고 하면, `for` 루프에서 `a`의 모든 문자를 한 번씩 확인한다. 따라서 `for` 루프의 시간 복잡도는 O(L) 다.
2. `if i in dic:` 조건문에서 딕셔너리 `dic`를 확인하며, 이 연산은 상수 시간이 소요된다.
3. 그 외의 연산은 상수 시간이 소요된다.

따라서 전체 코드의 시간 복잡도는 O(L) 다.     

공간 복잡도:

1. 딕셔너리 `dic`는 26개의 알파벳에 대한 키-값 쌍을 가진다. 따라서 공간 복잡도는 O(26) = O(1) 다.
2. 나머지 변수들은 상수 개수의 변수이므로 추가적인 공간을 차지하지 않으므로 공간 복잡도는 O(1) 다.

## 회고 과정

- 구현 문제들은 특히나 문제 자체를 처음부터 이해를 제대로 하는 게 정말 중요하다고 생각했다.
- 한국말인데 뭔가 한 번에 이해 못 할 때가 진짜 많은 것 같다..

### 다른 사람 코드를 보고 그 기준으로 어떤 부분을 개선 할 수 있는지 최종 회고

```python
n, b = input().split() 

print(int(n,int(b)))
```

다른 사람들의 풀이 방법을 구경하는데 와우 정말 짧게 문제를 풀 수 있는 방법이 있었다.     
파이썬의 경우 int 함수를 활용하면 n진법을 10진법으로 변환할 수 있다.     
int(변환할 string, n진법) 형식으로 사용된다.     
n 진법은 int 형으로 입력해야 한다.      
이렇게 오늘 또 하나를 배웠다 :D