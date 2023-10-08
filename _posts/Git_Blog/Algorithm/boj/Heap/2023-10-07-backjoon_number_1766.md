---
layout: single
title: "[백준 1766번 문제집] (알고리즘)"
categories: Algo
tags:
  - Python
  - 백준
  - Heap
  - TopologicalSorting
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
`Heap`, `Topological Sorting`
## 문제

[문제 링크](https://www.acmicpc.net/problem/1766)

민오는 1번부터 N번까지 총 N개의 문제로 되어 있는 문제집을 풀려고 한다. 문제는 난이도 순서로 출제되어 있다. 즉 1번 문제가 가장 쉬운 문제이고 N번 문제가 가장 어려운 문제가 된다.

어떤 문제부터 풀까 고민하면서 문제를 훑어보던 민오는, 몇몇 문제들 사이에는 '먼저 푸는 것이 좋은 문제'가 있다는 것을 알게 되었다. 예를 들어 1번 문제를 풀고 나면 4번 문제가 쉽게 풀린다거나 하는 식이다. 민오는 다음의 세 가지 조건에 따라 문제를 풀 순서를 정하기로 하였다.

1. N개의 문제는 모두 풀어야 한다.
2. 먼저 푸는 것이 좋은 문제가 있는 문제는, 먼저 푸는 것이 좋은 문제를 반드시 먼저 풀어야 한다.
3. 가능하면 쉬운 문제부터 풀어야 한다.

예를 들어서 네 개의 문제가 있다고 하자. 4번 문제는 2번 문제보다 먼저 푸는 것이 좋고, 3번 문제는 1번 문제보다 먼저 푸는 것이 좋다고 하자. 만일 4-3-2-1의 순서로 문제를 풀게 되면 조건 1과 조건 2를 만족한다. 하지만 조건 3을 만족하지 않는다. 4보다 3을 충분히 먼저 풀 수 있기 때문이다. 따라서 조건 3을 만족하는 문제를 풀 순서는 3-1-4-2가 된다.

문제의 개수와 먼저 푸는 것이 좋은 문제에 대한 정보가 주어졌을 때, 주어진 조건을 만족하면서 민오가 풀 문제의 순서를 결정해 주는 프로그램을 작성하시오.

### 입력

첫째 줄에 문제의 수 N(1 ≤ N ≤ 32,000)과 먼저 푸는 것이 좋은 문제에 대한 정보의 개수 M(1 ≤ M ≤ 100,000)이 주어진다. 둘째 줄부터 M개의 줄에 걸쳐 두 정수의 순서쌍 A,B가 빈칸을 사이에 두고 주어진다. 이는 A번 문제는 B번 문제보다 먼저 푸는 것이 좋다는 의미이다.

항상 문제를 모두 풀 수 있는 경우만 입력으로 주어진다.

### 출력

첫째 줄에 문제 번호를 나타내는 1 이상 N 이하의 정수들을 민오가 풀어야 하는 순서대로 빈칸을 사이에 두고 출력한다.

#### 예제 입력 1 복사

4 2
4 2
3 1

#### 예제 출력 1 복사

3 1 4 2


## 스스로 고민

최소 힙으로 풀면 될 거 같았다.     
다만 조건에 만족하기 위해서는 조건문의 순서를 잘 정해야 된다고 생각했는데 우선  쉬운 문제 -> 그리고 나머지는 링크드 리스트로 연결하면 되지 않을까?     
우선 힙과 조건문으로만 구현해 보고 좀 안된다 싶으면 링크드 리스트로 연결해서 풀어보기로 했다!

## 구현 코드

```python
import sys
import heapq

n, m = map(int, sys.stdin.readline().split())
heap = []
heapq.heapify(heap)
for _ in range(m):
    a, b = map(int, sys.stdin.readline().split())
    heapq.heappush(heap, (a,b))
    

print(*[item for sublist in heap for item in sublist])
```

틀렸다. 1, 2, 3, 4 이렇게 출력 되서 그런건가 싶어서 출력방식을 바꿨다.

```python
import sys
import heapq

n, m = map(int, sys.stdin.readline().split())
heap = []
heapq.heapify(heap)
for _ in range(m):
    a, b = map(int, sys.stdin.readline().split())
    heapq.heappush(heap, (a,b))
    

flat_list  = [item for sublist in heap for item in sublist]
result = ' '.join(map(str, flat_list))
print(result)

    
```

출력 초과가 뜬다.

```python
import sys
import heapq

n, m = map(int, sys.stdin.readline().split())

heap = []
num_list = []
heapq.heapify(heap)
for _ in range(m):
    a, b = map(int, sys.stdin.readline().split())
    heapq.heappush(heap, (a,b))
    num_list.append(a)
    num_list.append(b)
    
for num in range(1, n+1):
    if num not in num_list:
        heapq.heappush(heap, (num, ))
        
flat_list  = [item for sublist in heap for item in sublist]
result = ' '.join(map(str, flat_list))
print(result)
```

먼저 풀어야 할 문제가 없을 경우를 추가해 봤는데 이번에는 시간 초과가 뜬다
스택으로 풀어야 하나..      

문제를 다시 읽어보니        

```
 문제의 수 N(1 ≤ N ≤ 32,000)과 먼저 푸는 것이 좋은 문제에 대한 정보의 개수 M(1 ≤ M ≤ 100,000)이 주어진다.
```

그렇다는 건 1번보다 2번 2번 보다 3번 이렇게 주어질 수도 있다는 뜻이라고 생각헀다.     
그러면  3, 2, 2, 1 이렇게 나올 테니 초과 출력으로 되는 게 맞다. 😑

힙 하나 만으로는 문제가 안 풀릴 거 같아서 데크를 섞어 써야 하는 건가 했는데 시간이 초과돼서 다른 사람 풀이를 봤다.

## 다른사람 풀이

```python
import sys
import heapq
input = sys.stdin.readline

n, m = map(int, input().split())
in_degree = [0] * (n + 1)
graph = [[] for _ in range(n + 1)]

for _ in range(m):
    a, b = map(int, input().split())

    in_degree[b] += 1
    graph[a].append(b)

queue = []

for i in range(1, n + 1):
    if in_degree[i] == 0:
        heapq.heappush(queue, i)

while queue:
    current = heapq.heappop(queue)
    print(current, end=" ")

    for g in graph[current]:
        in_degree[g] -= 1

        if in_degree[g] == 0:
            heapq.heappush(queue, g)

```

- 골드부터는 두 가지 이상의 개념이 섞이나 보다.
- 풀이 글을 보니 위상 정렬이라는 개념이 들어가 있다.
- 회고에서 위상 정렬에 대해서 공부해보자

## 시간 복잡도와 공간 복잡도

**시간 복잡도**:
1. `for _ in range(m):` 루프를 통해 그래프의 간선 정보를 입력 받고, 각 노드의 진입 차수를 계산하는 부분은 O(m)의 시간이 소요된다. 여기서 m은 간선의 수다.
2. 위상 정렬 알고리즘은 모든 노드를 한 번씩 방문하며 진입 차수를 갱신하고 큐에 노드를 추가하는 과정이다. 이 부분은 모든 노드와 간선에 대해 한 번씩 순회하므로 O(n + m)의 시간이 걸린다.

따라서 전체 시간 복잡도는 O(m + n + m)이며, 간단하게 표현하면 O(n + m)다.

**공간 복잡도**:
1. `in_degree` 리스트는 각 노드의 진입 차수를 저장하기 위한 리스트로 크기가 (n+1) 다. 즉, n개의 노드를 나타내기 위한 공간을 사용하므로 O(n)의 공간이 필요하다.
2. `graph` 리스트는 그래프를 나타내기 위한 인접 리스트다. 각 노드마다 연결된 노드들의 정보를 저장하기 위한 공간으로 사용된다. 크기가 (n+1)인 리스트를 만들었으므로 O(n)의 공간을 사용한다.
3. `queue` 리스트는 큐를 나타내며, 위상 정렬 시에 사용된다. 최악의 경우 모든 노드가 큐에 들어갈 수 있으므로 크기가 n인 리스트를 사용하며 O(n)의 공간을 차지한다.

따라서 전체 공간 복잡도는 O(n)다.

## 회고 과정

- 실버 문제는 이제 어렵지 않게 풀 수 있어서 골드도 풀 수 있겠지? 했지만 골드부터는 여러 가지 개념을 섞어서 푸는 거 같았다.
- 위상 정렬이라는 개념을 처음 접했는데 힙은 이제 어느 정도 풀 수 있으니까 이제 그래프를 풀어봐야겠다

### 위상 정렬이란?

위상 정렬(Topological Sorting)은 방향 그래프(Directed Graph)에서 노드(Node)들의 선행 순서를 지키면서 모든 노드를 나열하는 알고리즘이다. 이 알고리즘은 주로 의존 관계가 있는 작업들을 정렬할 때 사용된다. 예를 들어, 각 작업을 노드로 표현하고 작업 간의 의존 관계를 화살표로 표현한 그래프에서 위상 정렬을 수행하면 작업들을 순서대로 수행할 수 있는 순서를 찾을 수 있다.

위상 정렬 알고리즘의 주요 특징은 다음과 같다:

1. **방향 그래프 사용**: 위상 정렬은 방향 그래프에서만 가능하다. 이는 각 노드가 방향(선행과 후행)을 가지고 있어야 함을 의미한다.
2. **순환 없음**: 위상 정렬을 수행하기 위해서는 그래프가 순환 구조를 가져서는 안된다. 순환 구조가 있으면 위상 정렬이 불가능하다.
3. **다수의 정렬 가능**: 그래프에서 여러 가지 위상 정렬 순서가 있을 수 있다. 따라서 위상 정렬은 하나의 유일한 해결책이 아닐 수 있다.
4. **시간 복잡도**: 위상 정렬 알고리즘은 일반적으로 그래프의 모든 노드와 간선을 한 번씩 순회하므로 시간 복잡도는 O(V + E)입니다. 여기서 V는 노드의 수, E는 간선의 수를 나타낸다.

위상 정렬 알고리즘은 다양한 방식으로 구현할 수 있으며, 대표적인 알고리즘으로는 Kahn's 알고리즘과 Depth-First Search(DFS)를 활용한 알고리즘이 있다고 한다.      
이 알고리즘들은 주로 작업 스케줄링, 의존성 관리, 컴파일러 최적화 등 다양한 분야에서 활용된다.

![](https://media1.giphy.com/media/J93sVmfYBtsRi/giphy.gif?cid=ecf05e472edbfg6wkq1f78v1ytkiwmhyc4d1jimc7pa68n98&ep=v1_gifs_search&rid=giphy.gif&ct=g)

내일부터는 그래프 뿌신다!


