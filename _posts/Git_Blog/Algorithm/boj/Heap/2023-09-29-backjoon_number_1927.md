---
layout: single
title: "[백준 1927번 최소힙] (알고리즘)"
categories: Algo
tags:
  - Python
  - 백준
  - Heap
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
`Heap`
## 문제

[문제 링크](https://www.acmicpc.net/problem/1927)

널리 잘 알려진 자료구조 중 최소 힙이 있다. 최소 힙을 이용하여 다음과 같은 연산을 지원하는 프로그램을 작성하시오.

1. 배열에 자연수 x를 넣는다.
2. 배열에서 가장 작은 값을 출력하고, 그 값을 배열에서 제거한다.

프로그램은 처음에 비어있는 배열에서 시작하게 된다.

### 입력

첫째 줄에 연산의 개수 N(1 ≤ N ≤ 100,000)이 주어진다. 다음 N개의 줄에는 연산에 대한 정보를 나타내는 정수 x가 주어진다. 만약 x가 자연수라면 배열에 x라는 값을 넣는(추가하는) 연산이고, x가 0이라면 배열에서 가장 작은 값을 출력하고 그 값을 배열에서 제거하는 경우이다. x는 231보다 작은 자연수 또는 0이고, 음의 정수는 입력으로 주어지지 않는다.

### 출력

입력에서 0이 주어진 횟수만큼 답을 출력한다. 만약 배열이 비어 있는 경우인데 가장 작은 값을 출력하라고 한 경우에는 0을 출력하면 된다.

#### 예제 입력 1 복사

9
0
12345678
1
2
0
0
0
0
32

#### 예제 출력 1 복사

0
1
2
12345678
0

## 스스로 고민

![](https://media2.giphy.com/media/aLTFHi2aowjJe/giphy.gif?cid=ecf05e473ztmtbpy1wuwd8c86qy3o7rv3czepcvc0ccpppt5&ep=v1_gifs_search&rid=giphy.gif&ct=g)

우선순위 큐 마스터하겠어!

- 기존의 heapq를 사용하면 쉽게 풀 수 있을 것 같다.
- 하지만 실제로 구현하는 걸 물어보는 거 같은데 일단 라이브러리 자체도 내가 원하는 데로 사용을 딱딱 잘 못하니까 라이브러리 사용법부터 제대로 익히자!

### 접근법

- 0이라는 입력이 들어올 때 마다 최소힙을 방출하면된다.

## 구현 코드

```python
import heapq

n = int(input())

heap = []
for _ in range(n):
    num = int(input())
    if num == 0:
        if len(heap) == 0:
            print(0)
        else:
            print(heapq.heappop(heap))
    else:
        heapq.heappush(heap, num)

```

- 답은 맞게 나오지만 시간초과가 나온다.. 한 번에 입력하지 않아서 그런건가?

```python
import heapq

n, *nums = list(map(int, input().split()))

heap = []
for num in nums:
    if num == 0:
        if len(heap) == 0:
            print(0)
        else:
            print(heapq.heappop(heap))
    else:
        heapq.heappush(heap, num)
```

- 이번에는 틀렸다고 나오는데 문제가 한 줄씩 이루어지므로 입력 값은 따로 받아야 되는 것 같다.

```python
import heapq
import sys
n = int(sys.stdin.readline())

heap = []
for _ in range(n):
    num = int(sys.stdin.readline())
    if num == 0:
        if len(heap) == 0:
            print(0)
        else:
            print(heapq.heappop(heap))
    else:
        heapq.heappush(heap, num)
```

- 시간초과가 계속 걸려서 시간초과를 해결하는 방법을 찾아보니까 input 이 아니라 readline을 써야 된다고 한다.
- 해당 방법대로 입력 받는 방식을 바꾸니 통과가 되었다.
- 근데 readline은 뭐길래 input 보다 빠른 속도를 내는걸까?

### input vs readline

- `input()`: 사용자로부터 한 줄의 텍스트 입력을 받는다. 입력을 기다리고 사용자가 엔터 키를 누를 때까지 대기한다.
- `readline()`: 표준 입력 스트림(stdin)에서 한 줄을 읽어온다. 파일과 같은 스트림에서 사용할 수 있으며, `input()`과 달리 사용자 입력을 기다리지 않는다.

`readline()`이 일반적으로 더 빠른 이유는 `input()`은 사용자 입력을 받을 때마다 표준 입력 버퍼를 읽어오고 파싱하는 작업이 추가되기 때문이다. 반면에 `readline()`은 이미 준비된 줄을 읽어오는 동작만 수행하므로 더 빠르다.
## 시간 복잡도와 공간 복잡도

- 시간 복잡도:
    - 힙에 요소를 추가하는 데 시간복잡도는 O(log N)이며, N번 반복
    - 0이 입력으로 주어지는 경우, 최솟값을 꺼내는데 시간복잡도는 O(log N)
    - 따라서 전체 시간 복잡도는 O(N * log N)

- 공간 복잡도:
    - 입력으로 주어진 데이터를 저장하는데 사용되는 메모리의 양은 O(N)
    - 힙(heap)의 크기도 최대 N까지 커질 수 있다.
    - 그 외에 `n`, `num`와 같은 상수 개수의 변수가 있으므로 추가적인 공간 복잡도는 O(1)
    - 따라서 전체 공간 복잡도는 O(N)

## 회고 과정

- readline 에 대한 개념을 알게 되었고 heapq를 어떻게 사용하면 되는지도 조금 더 익숙해졌다.
- 역시 문제를 많이 풀어봐야겠다.


### 다른 사람 코드를 보고 그 기준으로 어떤 부분을 개선 할 수 있는지 최종 회고

- 대부분 라이브러리를 통해 풀어서 그런지 구현 방식이 비슷비슷했다.
- 하지만 직접 구현 하는 방법도 궁금해서 좀 더 찾아봤다.

```python
import sys

class heap:
    db = [0]
    size = 0


    def __init__(self):
        pass
    def push(self, n):
        self.size+=1
        if(len(self.db)>self.size):
            self.db[self.size] = n
        else:
            self.db.append(n)

        tmp = self.size
        while(tmp>1):
            if(self.db[tmp>>1] >= self.db[tmp]):
                temp = self.db[tmp]
                self.db[tmp] = self.db[tmp>>1]
                self.db[tmp>>1] = temp
                tmp = tmp>>1

            else:
                break


    def pop(self):
        if self.size == 0:
            return('0')
        ans = self.db[1]

        self.db[1]= self.db[self.size]
        self.size-=1

        tmp = 1
        while(1):
            if(tmp*2+1 <= self.size): #left, right
                if(self.db[tmp*2] <= self.db[tmp*2+1] and self.db[tmp*2] <= self.db[tmp]):
                    temp = self.db[tmp*2]
                    self.db[tmp*2] = self.db[tmp]
                    self.db[tmp] = temp
                    tmp = tmp*2
                elif(self.db[tmp*2] > self.db[tmp*2+1] and self.db[tmp*2+1] <= self.db[tmp]):
                    temp = self.db[tmp*2+1]
                    self.db[tmp*2+1] = self.db[tmp]
                    self.db[tmp] = temp
                    tmp = tmp*2+1
                else:
                    return(str(ans))
            elif(tmp*2 == self.size): #Left
                if(self.db[tmp*2] <= self.db[tmp]):
                    temp = self.db[tmp*2]
                    self.db[tmp*2] = self.db[tmp]
                    self.db[tmp] = temp
                    tmp = tmp*2
                else:
                    return(str(ans))
            else:
                return(str(ans))



    def print(self):
        print(self.db)

a = heap()
N = int(input())
ss =""
for _ in range(N):
    n = int(sys.stdin.readline())
    if n == 0:
        ss +=a.pop()+'\n'
    else:
        a.push(n)

print(ss)

```

- 링크드 리스트보다 확실히 뭔가 손이 많이 간다.
- 이런 이유 때문에 라이브러리를 이용하나보다,