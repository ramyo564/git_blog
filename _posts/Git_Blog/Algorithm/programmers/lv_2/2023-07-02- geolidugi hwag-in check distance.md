---
layout: single
title: "프로그래머스 거리두기 확인하기 (알고리즘)"
categories: Algo
tag: [Java,Python,JavaScript,Lv_2,"거리두기 확인하기"]
toc: true
toc_sticky: true
author_profile: false
sidebar:

---


# 느낀점
개인 포폴도 하나 완성했고 팀 프로젝트도 새로 시작했으나 코테는 전혀 준비를 해 놓은 게 없어서 이번에 코테 스터디에 가입했다.     

이전에는 그냥 문법 안 까먹는 느낌으로 LV0 풀다가 이 스터디에서는 LV 2를 주로 푼다길래 그런가 보다 했는데 오늘 풀어보니까 WOW       

일단 문제가 무슨 소리를 하는지 모른다는 게 나의 첫 번째 문제였고 두 번째 문제는 무슨 알고리즘 써서 풀었다는데 나에게는 그런 개념 조차 없다는 점이였다.     

근데 뭐 이런 적이 한 두 번도 아니고 끝까지 가면 내가 이긴다라는 마인드로 시작했다.      

우선 나에게는 기본 알고리즘 개념이 없으니 단순 코드 보다는 중간 중간 이해 안 가는 부분들을 좀 자세히 공부할 계획이다.

https://school.programmers.co.kr/learn/courses/30/lessons/81302#fn1

## 멘해튼 거리

우선 맨해튼 거리가 뭔 소린가 했는데 최소, 최대 거리를 구하는 공식이라고 한다.    
맨해튼 거리를 찾아보니 유클리드 거리라는 것도 나오는데
밑에 블로그를 가보면 정말 잘 정리 되어있다.     

![](https://i.imgur.com/R19H4vn.png)

해당 글 들을 보면서 내가 느낀 점은 평소 lv.0은 그냥 퀴즈 느낌으로 재밌게 풀었는데 lv.2는 말 그대로 알아야 풀 수 있는 시험 느낌이라서 최대한 많이 풀어보는 게 더 낫다고 생각했다.

[맨해튼 거리 출처](https://rebro.kr/60)

## BFS?

다들 BFS BFS 해서 뭔지 찾아봤다.     

BFS는 "너비 우선 탐색" (Breadth-First Search)의 약자다.     
BFS는 그래프나 트리와 같은 자료구조에서 노드를 탐색하는 방법 중 하나다. 이 알고리즘은 시작 노드에서부터 거리가 점점 멀어지는 순서대로 탐색을 진행한다.     

BFS는 다음과 같은 특징을 가지고 있다:     

1. 너비 우선 탐색: BFS는 이름 그대로 너비를 우선으로 탐색을 진행한다. 즉, 한 단계의 인접한 노드들을 모두 탐색한 후에 그 다음 단계로 넘어간다. 따라서 시작 노드로부터 먼 노드들은 나중에 탐색된다.
    
2. 큐를 활용: BFS는 큐(Queue) 자료구조를 사용하여 탐색을 진행한다. 시작 노드를 큐에 넣고, 큐에서 노드를 하나씩 꺼내면서 인접한 노드들을 큐에 추가한다. 이렇게 큐를 활용하여 탐색을 진행하면서 너비 우선으로 탐색할 수 있다.
    
3. 최단 경로 탐색: BFS는 시작 노드로부터 다른 모든 노드까지의 최단 경로를 찾을 수 있다. 이는 너비 우선으로 탐색하면서 시작 노드에서부터의 거리를 계산하여 도달할 수 있는 최단 경로를 찾기 때문이다.
    

BFS는 그래프 탐색, 최단 경로 찾기, 연결 요소 확인 등 다양한 문제에 활용된다. 예를 들어, 네트워크의 노드 간 연결 여부를 확인하거나, 미로 탐색에서 최단 경로를 찾는 등의 문제를 해결할 때 BFS를 사용할 수 있다.

https://velog.io/@sem/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EC%8A%A4-LEVEL2-%EA%B1%B0%EB%A6%AC%EB%91%90%EA%B8%B0-%ED%99%95%EC%9D%B8%ED%95%98%EA%B8%B0-Python

## 나는 어떻게 풀었나

감조차 오지 않아 다른 사람들의 풀이를 쭉 보니가 대부분 데이터의 좌표 값을 새로 매기고 그걸 토대로 계산하는 방식들을 구현했다.      

하나하나 좌표를 구하는 방식을 구현 하는 게 너무 귀찮았는데 아무리 생각해봐도 좌표 없이 공간에 대한 값을 구할 수 있는 꼼수? 가 생각나는 게 없었다.     

우선 좌표 값부터 구했다.      

데이터 형식은 다음과 같이 들어온다.     

```python
[["POOOP", "OXXOX", "OPXPX", "OOXOX", "POXXP"], ["POOPX", "OXPXP", "PXXXO", "OXXXO", "OOOPP"], ["PXOPX", "OXOXP", "OXPOX", "OXXOP", "PXPOX"], ["OOOXX", "XOOOX", "OOOXX", "OXOOX", "OOOOO"], ["PXPXP", "XPXPX", "PXPXP", "XPXPX", "PXPXP"]]
```

```python
class Distance:
    def __init__(self, data):
        self.data = data
        self.row = 0
        self.column = 0
        
    def count(self):
        result = []
        start = []
        row = self.row  # 초기값 설정
        column = self.column  # 초기값 설정
        
        for data in self.data[row]:
            column += 1
            if data == "P":
                if len(start) == 0:
                    start.append(row)
                    start.append(column)
                elif abs(row - start[0]) - abs(column - start[1]) > 2:
                    start[0] = row
                    start[1] = column
                else:
                    result.append(0)
                    row += 1
                    column = 0
                    
            elif data == "X":
                start[0] = row
                start[1] = column
            
            if column == 5:
                column = 0    
    
            row += 1
            
        return result
        

datas = [["POOOP", "OXXOX", "OPXPX", "OOXOX", "POXXP"], ["POOPX", "OXPXP", "PXXXO", "OXXXO", "OOOPP"], ["PXOPX", "OXOXP", "OXPOX", "OXXOP", "PXPOX"], ["OOOXX", "XOOOX", "OOOXX", "OXOOX", "OOOOO"], ["PXPXP", "XPXPX", "PXPXP", "XPXPX", "PXPXP"]]

x = Distance(datas)
result = x.count()
print(result)

```

처음에 이렇게 했는데 문제가 많았다. 우선 x가 나왔을 때 새로 값이 갱신되다 보니까
pooox
poopo 
이런 식으로 되면 거리를 지킨 식으로 되버렸다.

개인적으로 알고리즘 푸는거 정말 재밌지만 시간 싸움이라 어느정도 시간안에만 풀고 못 풀면 답안지를 보면서 공부하라고 봤다.
혼자 더 생각해보고 싶었지만 답을 확인해봤는데....
```python
from collections import deque
places = [["POOOP", "OXXOX", "OPXPX", "OOXOX", "POXXP"], ["POOPX", "OXPXP", "PXXXO", "OXXXO", "OOOPP"], ["PXOPX", "OXOXP", "OXPOX", "OXXOP", "PXPOX"], ["OOOXX", "XOOOX", "OOOXX", "OXOOX", "OOOOO"], ["PXPXP", "XPXPX", "PXPXP", "XPXPX", "PXPXP"]]

def bfs(p):
    start = []
    
    for i in range(5): # 시작점이 되는 P 좌표 구하기
        for j in range(5):
            if p[i][j] == 'P':
                start.append([i, j])
    
    for s in start:
        queue = deque([s])  # 큐에 초기값
        visited = [[0]*5 for i in range(5)]   # 방문 처리 리스트
        distance = [[0]*5 for i in range(5)]  # 경로 길이 리스트
        visited[s[0]][s[1]] = 1
        
        while queue:
            y, x = queue.popleft()
        
            dx = [-1, 1, 0, 0]  # 좌우
            dy = [0, 0, -1, 1]  # 상하

            for i in range(4):
                nx = x + dx[i]
                ny = y + dy[i]

                if 0<=nx<5 and 0<=ny<5 and visited[ny][nx] == 0:
                    
                    if p[ny][nx] == 'O':
                        queue.append([ny, nx])
                        visited[ny][nx] = 1
                        distance[ny][nx] = distance[y][x] + 1
                    
                    if p[ny][nx] == 'P' and distance[y][x] <= 1:
                        return 0
    return 1


def solution(places):
    answer = []
    
    for i in places:
        answer.append(bfs(i))
    
    return answer


print(solution(places))


```

### BFS?

BFS가 뭐야...    
일단 메모...      

#### BFS feat. Chat GPT 

BFS는 너비 우선 탐색(Breadth-First Search)의 약어입니다. 그래프나 트리 구조에서 시작 정점에서부터 인접한 정점을 먼저 모두 탐색하는 방식의 알고리즘입니다.

BFS는 두 가지 주요한 개념인 큐(Queue)와 방문 표시(Visited)를 사용합니다. 일반적인 동작 과정은 다음과 같습니다:

1. 시작 정점을 큐에 넣고 방문 표시를 합니다.
2. 큐가 비어있을 때까지 다음을 반복합니다:
    - 큐에서 정점을 하나 꺼내고 해당 정점에 대한 처리를 합니다.
    - 해당 정점과 인접한 모든 정점을 방문하지 않았다면 큐에 넣고 방문 표시를 합니다.
3. 큐가 비어있으면 탐색이 종료됩니다.

BFS는 최단 경로를 찾는 문제나 상태 공간 탐색과 같은 다양한 문제에 사용될 수 있습니다. 또한 BFS는 모든 정점을 한 번씩 방문하므로 그래프의 구조를 파악하는 데에도 유용합니다.

일반적으로 BFS 알고리즘은 큐를 사용하여 구현됩니다. Python에서는 `collections` 모듈의 `deque`를 사용하여 큐를 구현할 수 있습니다. 큐에는 방문할 정점들이 저장되며, 방문 표시를 위해 추가적인 자료 구조(예: 2차원 배열)를 사용할 수 있습니다.

BFS는 너비를 기준으로 탐색하기 때문에 시작 정점에서 가까운 정점들을 먼저 탐색하고, 그 다음으로 더 멀리 떨어진 정점들을 탐색합니다. 이러한 특성 때문에 BFS는 최단 경로 문제에서 많이 사용됩니다.

다음은 BFS의 간단한 예시 코드입니다:

```python
from collections import deque

def bfs(graph, start):
    visited = set()
    queue = deque([start])
    
    while queue:
        node = queue.popleft()
        if node not in visited:
            visited.add(node)
            # node에 대한 처리
            
            # 인접한 정점들을 큐에 추가
            for neighbor in graph[node]:
                queue.append(neighbor)

# 그래프 예시
graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D', 'E'],
    'C': ['A', 'F'],
    'D': ['B'],
    'E': ['B', 'F'],
    'F': ['C', 'E']
}

bfs(graph, 'A')

```

위의 코드는 `graph`를 인접 리스트 형태로 표현한 후, 'A'를 시작 정점으로 BFS를 수행하는 예시입니다. BFS를 활용하여 그래프를 탐색하고 처리하는 로직을 구현할 수 있습니다.      

#### set() 은 또 뭐야? feat. GPT

`set()`은 Python에서 제공하는 내장 자료형 중 하나로, 중복되지 않는 고유한 값을 저장하는 컬렉션입니다. `set`은 수학적인 집합(set) 개념을 구현한 자료형으로, 집합에 대한 연산을 지원합니다.

`set()`을 사용하면 중복된 값을 제거하고, 원소 간에 순서가 없는(unique) 컬렉션을 만들 수 있습니다. `set`은 중괄호 `{}`를 사용하여 생성하며, 각 원소는 쉼표로 구분합니다.

예를 들어, 다음과 같이 `set`을 생성할 수 있습니다:
```python 
fruits = {'apple', 'banana', 'orange'}
print(fruits)  # 출력: {'apple', 'banana', 'orange'}
```

또는 `set()` 생성자를 사용하여 빈 `set`을 만들 수도 있습니다:

```python
empty_set = set()
print(empty_set)  # 출력: set()
```

`set`은 다음과 같은 주요 특징을 가지고 있습니다:

- 중복된 값이 허용되지 않습니다. 같은 값을 여러 번 추가해도 최종적으로는 하나의 원소만 남습니다.
- 순서가 없습니다. 원소 간에 순서를 보장하지 않습니다. 따라서 인덱스를 사용하여 특정 원소에 접근할 수 없습니다.
- 원소간에 변경 가능합니다. `set`은 가변(mutable) 자료형이므로, 추가, 삭제 등의 작업을 통해 원소를 변경할 수 있습니다.

위의 코드에서 `visited`는 방문한 노드를 저장하기 위해 `set`을 사용하고 있습니다. `visited`는 방문한 노드의 집합으로, 중복된 노드를 허용하지 않습니다. 이미 방문한 노드를 확인하기 위해 `if node not in visited`와 같이 `in` 연산자를 사용합니다.

`set`은 주로 중복을 제거하거나 멤버십 테스트, 집합 연산 등에 활용됩니다.


똑똑하군..     
lv.0 때는 다른 언어도 보면서 공부했는데 알고리즘은 아는 게 너무 없어서 일단 파이썬이라도 제대로 해야겠다고 생각했다.     

근데 큐는 또 뭐지 ㅋㅋㅋ 이전에 큐 구현도 했었는데 기억이 안난다.. ㅜㅜ     

####  큐 feat. Chat GPT 
큐(Queue)는 데이터를 임시적으로 저장하는 자료 구조입니다. 큐는 데이터를 먼저 넣은 순서대로 처리하는 선입선출(First-In-First-Out, FIFO) 원칙을 따릅니다. 이러한 특성 때문에 큐는 데이터를 순차적으로 처리해야 할 때 유용하게 사용됩니다.

큐는 일상 생활에서 줄을 서는 것과 유사한 개념입니다. 가장 먼저 온 사람이 가장 먼저 서비스를 받는 것과 같이, 큐에서 가장 먼저 들어온 데이터가 가장 먼저 처리되는 것입니다.

큐는 보통 두 개의 주요 연산을 지원합니다:

1. Enqueue: 큐에 데이터를 추가하는 연산입니다. 새로운 데이터는 큐의 뒤쪽에 추가됩니다.
2. Dequeue: 큐에서 데이터를 제거하고 반환하는 연산입니다. 가장 앞쪽에 있는 데이터가 제거됩니다.

큐는 주로 리스트나 연결 리스트로 구현될 수 있습니다. Python에서는 큐를 구현하기 위해 `collections` 모듈의 `deque`를 사용하는 것이 일반적입니다. `deque`는 더블 엔드 큐(Double-Ended Queue)로, 큐의 양쪽 끝에서 데이터를 추가하거나 제거하는 데 효율적인 자료 구조입니다.

다음은 Python에서 `deque`를 사용한 간단한 큐의 예시입니다:
```python
from collections import deque

# 빈 큐 생성
queue = deque()

# 데이터 enqueue
queue.append(1)
queue.append(2)
queue.append(3)

# 큐의 가장 앞쪽 데이터 확인
front = queue[0]
print(front)  # 출력: 1

# 데이터 dequeue
data = queue.popleft()
print(data)  # 출력: 1

# 큐의 현재 상태 출력
print(queue)  # 출력: deque([2, 3])

```
위의 예시에서 `deque`를 사용하여 큐를 생성하고, `append()`로 데이터를 추가하고 `popleft()`로 데이터를 제거합니다. 큐의 상태는 리스트처럼 출력할 수 있습니다.     

이렇게 큐를 사용하면 데이터를 일시적으로 저장하고, 선입선출의 원칙에 따라 순차적으로 처리할 수 있습니다. 큐는 다양한 알고리즘과 데이터 구조에서 유용하게 활용됩니다.     

넘나 똑똑하고 유용한 것!     

### 내가 풀지 못 한 원인 분석

우선 BFS 개념은 이해했는데 그럼 또 어떤 문제 때문에 내가 못 풀었는지 분석해 봤다.
1. BFS 알고리즘 같은 걸 몰랐다 -> 이러한 로직을 생각하지도 못 했다 .. ㅠ
2. 큐에 대해서 제대로 알고 있는 상태가 아니였다.
3. set() 이런 기본적인 것도 제대로 알고 있는 상태가 아니였다.

모르는 게 많다면 배우기만 하면 됨! 부정적으로 생각해봤자 변하는 건 없다 하하하

## 다시 풀어보기

```python
from collections import deque
places = [["POOOP", "OXXOX", "OPXPX", "OOXOX", "POXXP"], ["POOPX", "OXPXP", "PXXXO", "OXXXO", "OOOPP"], ["PXOPX", "OXOXP", "OXPOX", "OXXOP", "PXPOX"], ["OOOXX", "XOOOX", "OOOXX", "OXOOX", "OOOOO"], ["PXPXP", "XPXPX", "PXPXP", "XPXPX", "PXPXP"]]

def bfs(p):
    start = []
    
    for i in range(5): 
    # 처음에 i 가 0으로 시작한다면 밑에 j는 0,1,2,3,4 를 반복한다.
        for j in range(5):
            if p[i][j] == 'P':
            # 반복문으로 처음 들어오는 게
            # ["POOOP", "OXXOX", "OPXPX", "OOXOX", "POXXP"] 라고 한다면
            # p[0] 은 "POOOP"
            # p[0][0] 은 "P"
                start.append([i, j])
                # start 에는 좌표가 들어간다. 위의 예시로 본다면 
                # 첫 번째 "POOOP" 에대한 P의 좌표는  [0,0], [0,4] 가 있고
                # 해당 좌표는 start 리스트에 저장된다.
    
    for s in start:
	    # 위의 고정된 데이터 값으로 본다면 첫 s 는 [0,0]
	    
        queue = deque([s])  # 큐에 초기값
        # queue 값은 deque([[0, 0]])
        
        visited = [[0]*5 for i in range(5)]   # 방문 처리 리스트
        distance = [[0]*5 for i in range(5)]  # 경로 길이 리스트
        # [0]*5 는 [0,0,0,0,0] 
        # visited 와 distance 는 모두 2차원 리스트다.
        # [[0,0,0,0,0] ,[0,0,0,0,0] ,[0,0,0,0,0] ,[0,0,0,0,0] ,[0,0,0,0,0]]
        
        visited[s[0]][s[1]] = 1
        # 위에서 처음 들어오는 값은 [0,0]
        # visited[s[0]][s[1]] 는 visited[0][0] 이고 
        # visited[0] 는 [0,0,0,0,0]
        # visited[0][0] 는 0 이다. 이걸 1로 바꿔준다.
        
        while queue:
            y, x = queue.popleft()
	        # 처음 뽑은 x와 y는 각각 0,0
            dx = [-1, 1, 0, 0]  # 좌우
            # 왼쪽으로 이동(-1), 오른쪽으로 이동(1), 상(0), 하(0) 방향을 나타낸다.
            dy = [0, 0, -1, 1]  # 상하
            # 상(0), 하(0), 위로 이동(-1), 아래로 이동(1) 방향을 나타낸다.

            for i in range(4):
                nx = x + dx[i]
                ny = y + dy[i]

                if 0<=nx<5 and 0<=ny<5 and visited[ny][nx] == 0:
                    # 0<=nx<5 와 0<=ny<5 는 5x5 안에 있는지 를 체크
                    # visited[ny][nx] == 0 는 방문학적이 있는 지 없는지 체크
                    # visited[ny][nx] == 1 이라면 방문한 적이 있는 상태
                    
                    if p[ny][nx] == 'O':
                    # 다음 위치가 O 인 경우 진행
                        queue.append([ny, nx])
                        # 큐에 다음 위치를 추가
                        visited[ny][nx] = 1
                        # 해당 위치를 방문 했다고 표시
                        distance[ny][nx] = distance[y][x] + 1
		                # 현재까지의 경로 길이를 저장하기 위해서 진행 
		                # 이전 위치 y,x 까지의 경로 길이에 1을 더한 값이
		                # 다음 취기 ny, nx 까지의 경로 길이가 된다.
		                
                    if p[ny][nx] == 'P' and distance[y][x] <= 1:
                    # 다음 위치 ny, nx가 P인 경우 
                    # 그리고 distance[y][x] <= 1 인 경우 현재 위치에서 인접한 곳에
                    # 사람이 있기 때문에 거리규칙 위반 -> 0 반환
                    # answer 에 0이 쌓임
                        return 0
    return 1


def solution(places):
    answer = []
    
    for i in places:
        answer.append(bfs(i))
    
    return answer


print(solution(places))

```

## 시간복잡도 

O(N^2)

- N 은 각 장소의 크기 장소는 5 x 5 → N = 5
- start 리스트는 장소를 순회하면서 P의 위치를 찾아 start 리스트에 저장합니다. 해당 과정은 장소의 크기에 비례하므로 O(N^2) 시간이 걸립니다.
- bfs 는 start 리스트의 각 좌표에 대해 bfs 탐색을 수행합니다. bfs 탐색은 최악의 경우 모든 노드를 방문해야 하므로, 이 경우에는 최대 N^2 노드를 방문하게 됩니다. 따라서 BFS 탐색의 시간 복잡도는 O(N^2) 입니다.

출처 : 프로그래머스,