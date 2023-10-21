---
layout: single
title: "[백준 2738번 행렬 덧셈] (알고리즘)"
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

N*M크기의 두 행렬 A와 B가 주어졌을 때, 두 행렬을 더하는 프로그램을 작성하시오.

### 입력

첫째 줄에 행렬의 크기 N 과 M이 주어진다. 둘째 줄부터 N개의 줄에 행렬 A의 원소 M개가 차례대로 주어진다. 이어서 N개의 줄에 행렬 B의 원소 M개가 차례대로 주어진다. N과 M은 100보다 작거나 같고, 행렬의 원소는 절댓값이 100보다 작거나 같은 정수이다.

### 출력

첫째 줄부터 N개의 줄에 행렬 A와 B를 더한 행렬을 출력한다. 행렬의 각 원소는 공백으로 구분한다.

#### 예제 입력 1 복사

3 3
1 1 1
2 2 2
0 1 0
3 3 3
4 4 4
5 5 100

#### 예제 출력 1 복사

4 4 4
6 6 6
5 6 100

## 스스로 고민

행렬의 합을 구하는건데 numpy를 사용하면 매우 쉽게 해결할 수 있다고 생각했다!

## 구현 코드

```python
import sys
import numpy as np

n, m = map(int, sys.stdin.readline().split())

a = []
b = []

for _ in range(n):
    x = list(map(int, sys.stdin.readline().split()))
    a.append(x)
    
for _ in range(n):
    x = list(map(int, sys.stdin.readline().split()))
    b.append(x)
    
A = np.array(a)
B = np.array(b)

z = A+B
for i in z:
    print(*i)
```

백준은 numpy를 사용할 수 없다! 하하!     
그래서 반복문을 한 번 더 사용해서 문제를 해결했는데 다른 사람들은 어떻게 해결했는지가 매우 궁금했다.     

```python
import sys

n, m = map(int, sys.stdin.readline().split())

a = []
b = []
c = []
for _ in range(n):
    x = list(map(int, sys.stdin.readline().split()))
    a.append(x)
    
for _ in range(n):
    x = list(map(int, sys.stdin.readline().split()))
    b.append(x)
    
for i in range(n):
    for q in range(m):
        c.append(a[i][q] + b[i][q])
    print(*c)
    c=[]
```
## 시간 복잡도와 공간 복잡도

시간 복잡도:

1. `n`과 `m`을 입력 받는 부분의 시간 복잡도는 O(1)다.
2. `a`와 `b` 리스트를 초기화하기 위해 `n`번 반복한다. 각 반복에서 `m`개의 정수를 입력 받으므로, 이 부분의 시간 복잡도는 O(n * m)다.
3. `c` 리스트에 값을 추가하기 위해 중첩된 반복문을 사용한다. 외부 반복문은 `n`번 반복하고 내부 반복문은 `m`번 반복한다. 따라서 `c` 리스트에 값을 추가하는 부분의 시간 복잡도는 O(n * m)다.
4. `print(*c)` 부분은 `c` 리스트의 내용을 출력하며, `c` 리스트의 길이는 항상 `m`다. 따라서 이 부분의 시간 복잡도는 O(m)다.

따라서 전체 코드의 시간 복잡도는 O(1) + O(n * m) + O(n * m) + O(m) = O(n * m)다.        

공간 복잡도:

1. `a`, `b`, `c` 리스트는 입력값과 결과를 저장하기 위한 리스트다. 각각의 길이는 `n * m`다. 따라서 이 부분의 공간 복잡도는 O(n * m)다.
2. 그 외에 사용되는 메모리는 상수이므로 추가적인 공간 복잡도는 O(1)다.

따라서 전체 코드의 공간 복잡도는 O(n * m)다.        

## 회고 과정

- 아쉽게도 다른 사람들이 제출한 답안을 봐도 딱히 다른 점이 없어서 아쉬웠다.
- 다른 점은 컴프리헨션을 사용해서 메모리 낭비를 줄이는 정도?