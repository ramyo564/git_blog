---
layout: single
title: "[백준 11279번 최대힙] (알고리즘)"
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

널리 잘 알려진 자료구조 중 최대 힙이 있다. 최대 힙을 이용하여 다음과 같은 연산을 지원하는 프로그램을 작성하시오.

1. 배열에 자연수 x를 넣는다.
2. 배열에서 가장 큰 값을 출력하고, 그 값을 배열에서 제거한다.

프로그램은 처음에 비어있는 배열에서 시작하게 된다.

### 입력

첫째 줄에 연산의 개수 N(1 ≤ N ≤ 100,000)이 주어진다. 다음 N개의 줄에는 연산에 대한 정보를 나타내는 정수 x가 주어진다. 만약 x가 자연수라면 배열에 x라는 값을 넣는(추가하는) 연산이고, x가 0이라면 배열에서 가장 큰 값을 출력하고 그 값을 배열에서 제거하는 경우이다. 입력되는 자연수는 231보다 작다.

### 출력

입력에서 0이 주어진 횟수만큼 답을 출력한다. 만약 배열이 비어 있는 경우인데 가장 큰 값을 출력하라고 한 경우에는 0을 출력하면 된다.

#### 예제 입력 1 복사

13
0
1
2
0
0
3
2
1
0
0
0
0
0

#### 예제 출력 1 복사

0
2
1
3
2
1
0
0


## 스스로 고민

- 최대 힙을 구하는 문제다!
- 기본적으로 heapq는 최소힙으로 구현되어 있기 때문에 -를 사용해야한다.
- 이제 사용하는데 조금 익숙해진거 같다 ㅎㅎ

## 구현 코드

```python
import heapq
import sys

n = int(sys.stdin.readline())

heap = []
heapq.heapify(heap)

for _ in range (n):
    k = int(sys.stdin.readline())
    if k == 0:
        if len(heap) == 0:
            print(0)
        else:
            print(-(heapq.heappop(heap)))
    else:
        heapq.heappush(heap, -k)
```

- 이전에 배웠던 readline 이 잘 생각나지 않았다.
	- 정말 많이 해봐야 한 번에 딱 기억날 것 같다
- 이전에 구해봤던 최소힙과 별 차이는 없지만 확실히 복습차원에서 좋았다.
## 시간 복잡도와 공간 복잡도

1. 시간 복잡도:
    - 입력된 정수의 개수를 n이라고 할 때, 루프가 n번 반복
    - 각 루프에서, 정수 k를 입력 받아 힙에 삽입하거나 힙에서 추출하는 연산을 수행
    - heapq 모듈을 사용하면 이러한 연산이 O(log n) 시간 수행
    - 따라서 전체 시간 복잡도는 O(n * log n)

2. 공간 복잡도:
    - 입력된 정수를 저장하기 위한 힙(heap) 데이터 구조를 사용하며 입력된 정수의 개수에 따라 힙의 크기가 달라진다.
    - 힙(heap)에 저장되는 정수의 개수는 최대 n개일 수 있으므로, 공간 복잡도는 O(n)

## 회고 과정

- 이제 어느정도 익숙해졌으니 다음에는 난이도를 한 단계 높여서 도전해보자!

![](https://media3.giphy.com/media/65lBM9eTRnWCuANHNQ/giphy.gif?cid=ecf05e47n46nqmjzp8ilvat9v35yvq7vyadk7ru5mbrhyimu&ep=v1_gifs_gifId&rid=giphy.gif&ct=g)


