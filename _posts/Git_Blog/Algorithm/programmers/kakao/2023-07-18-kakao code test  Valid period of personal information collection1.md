---
layout: single
title: "프로그래머스 - [카카오 인턴] 경주로 건설 (알고리즘)"
categories: Algo
tag: [Java,Python,JavaScript,Lv_2,"[카카오 인턴] 경주로 건설"]
toc: true
toc_sticky: true
author_profile: false
sidebar:

---
![](https://i.imgur.com/MkrCHeJ.png)

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/67259)

```python
from collections import deque

def solution(board):
    def bfs(start):
        direc = {0:[-1, 0], 1:[0, 1], 2:[1, 0], 3:[0, -1]} # 북,동,남,서 순서
        length = len(board)
        visited = [[987654321]*length for _ in range(length)]
        visited[0][0] = 0

        q = deque([start]) # x, y, cost, dir
        while q:
            x, y, cost, d = q.popleft()
            for i in range(4): # 북,동,남,서 순서
                nx = x + direc[i][0]
                ny = y + direc[i][1]

                # board 안에 있고, 벽이 아닌지 확인
                if 0 <= nx < length and 0 <= ny < length and board[nx][ny] == 0:
                    
                    # 비용계산
                    if i == d : ncost = cost + 100
                    else : ncost =  cost + 600
                    # 최소 비용이면 갱신 후 endeque!
                    if ncost < visited[nx][ny]:
                        visited[nx][ny] = ncost
                        q.append([nx, ny, ncost, i])
                        
        return visited[-1][-1]
    
    return min([bfs((0, 0, 0, 1)), bfs((0, 0, 0, 2))])
```

## 코드 리뷰 

![](https://i.imgur.com/2dzymEx.png)

동쪽으로 출발하는 경우와 남쪽으로 출발하는 경우가 있다.       
두 가지 방법을 비교해서 제일 비용이 낮은 경로를 리턴하는 로직이다.     

### 방향설정
```python
direc = {0:[-1, 0], 1:[0, 1], 2:[1, 0], 3:[0, -1]} # 북,동,남,서 순서
```
딕셔너리 0번은 북, 1번은 동 이런식으로 direc 딕셔너리를 만들어준다.

### 비용 계산하기
```python
visited = [[987654321]*length for _ in range(length)]
visited[0][0] = 0
```

우선 제한사항에서 배열의 크기는 3이상 25이하다.     
직선비용은 100원 곡선 비용은 500원이다.    
장애물이 없다는 가정하에 2 x 2 는 의미가 없으니 3 x 3 으로 봤을 때 최솟값이 900원이다.
따라서 4 x 4 에서는 최솟값이 900원 보다는 높을 것이다.
배열이 25 x 25까지 될 수 있으니 최대 값은 높게 설정해 놓으면 알아서 업데이트 되지만 애초에 나올 수 없는 숫자는 들어가면 안된다.     
예를 들어서 배열이 4x4 인데 `visited = [[900]*length for _ in range(length)]`  으로 한다면
어차피 제일 낮은 가격이 900원이므로 테스트에 통과할 수 없다.      
따라서 테스트에 통과할 만한 큰 수를 사용하면 된다.     
`visited[0][0] = 0`  처음 시작하는 부분을 0원으로 초기화 해준다.      

## 입력설정하기

북동남서 방향, 시작지점 (0,0) 기준으로 
- x 현재 위치의 좌표 x
- y 현재 위치의 좌표 y
- cost 현재까지 이동한 비용
- d 현재방향

```python
return min([bfs((0, 0, 0, 1)), bfs((0, 0, 0, 2))])
```
동쪽으로 시작하는 경우와 남쪽으로 시작하는 경우를 비교해 가장 낮은 코스트를 리턴한다.
튜플 첫 번째는 x 두 번째는 y 세 번째는 비용 네 번째는 방향이다.

## 반복문 설정

```python
q = deque([start]) # x, y, cost, dir
while q:
	x, y, cost, d = q.popleft()
	for i in range(4): # 북,동,남,서 순서
		nx = x + direc[i][0]
		ny = y + direc[i][1]

		# board 안에 있고, 벽이 아닌지 확인
		if 0 <= nx < length and 0 <= ny < length and board[nx][ny] == 0:
			
			# 비용계산
			if i == d : ncost = cost + 100
			else : ncost =  cost + 600
			# 최소 비용이면 갱신 후 endeque!
			if ncost < visited[nx][ny]:
				visited[nx][ny] = ncost
				q.append([nx, ny, ncost, i])
```

start 부분은 동쪽 방향과 남쪽방향이 차례대로 들어오며 deque로 진행된다.     
우선 동쪽 방향으로 시작될 경우를 설명해본다면 가장 맨 왼쪽 x 좌표가 꺼내진다.

### 1. 배열에서 벗어날 때
반복문이 진행될 때 0,1,2,3 -> 북동남서 순서로 진행이되며 처음 0번 북쪽 방향이다.     
nx = 0 + `direct[0][0]`      
ny = 0 + `direct[0][1]`      
`direc = {0:[-1, 0], 1:[0, 1], 2:[1, 0], 3:[0, -1]} # 북,동,남,서 순서`     
nx = 0 + -1     
ny = 0 + 0     
현재 좌표 nx, ny (-1, 0)     
`if 0 <= nx < length and 0 <= ny < length and board[nx][ny] == 0: `     
nx 는 현재 좌표를 벗어났으므로 false     

### 2. 배열 안에 있고 벽을 만나지 않았을 때
1. 동쪽으로 시작하는 경우 `bfs((0, 0, 0, 1))`
2. 첫 시작에서 동쪽으로 이동하는 경우

nx = 0 + `direct[1][0]`      
ny = 0 + `direct[1][1]`      
nx = 0 + 0      
ny = 0 + 1      
현재 좌표 (0,1)      
`board[nx][ny] == 0` 현재 좌표가 0이라면 True     
비용계산 시작!     

```python
# 비용계산
if i == d : ncost = cost + 100
else : ncost =  cost + 600
# 최소 비용이면 갱신 후 endeque!
if ncost < visited[nx][ny]:
	visited[nx][ny] = ncost
	q.append([nx, ny, ncost, i])

```
동쪽으로 시작했으니 d = 1 , i 값도 현재 1     
ncost = 0 + 100     
만약에 방향이 달라지면 꺾은 것이므로 비용은 600 추가
현재 새롭게 측정된 ncost가 이전에 값이랑 비교해서 적다면 최소 비용이므로 해당 좌표에 ncost 업데이트 후 초기 q 값 `(0, 0, 0, 1)` 를 비용 측정한 값으로 업데이트 -> (0, 0, 100 , 1)   

배열에 맞는 visited의 모든 값은  `[987654321]` 인데 ncost 값은 이보다 무조건 작으므로 visited 배열에서 nx,ny에 맞는 배열에 ncost 로 비용 업데이트     
업데이트 된 내용은 다시 오른쪽 방향으로 채워지고 처음 q의 처음 값과 달라지는 경우 while 문 종료      
처음 시작한 `[bfs((0, 0, 0, 1)), bfs((0, 0, 0, 2))]` 중에 가장 작은 값 리턴

## 시간 복잡도

먼저, 주어진 미로의 한 변의 길이를 N

1. **`visited`** 리스트 초기화: **`visited`** 리스트는 미로의 모든 셀을 **`999999999`**로 초기화 ,이 단계에서 시간 복잡도는 O(N^2)
2. BFS 탐색: BFS 탐색은 모든 셀을 방문하면서 인접한 셀들을 확인하는 과정, 이때 모든 셀을 한 번씩만 방문하므로 BFS 탐색의 시간 복잡도는 O(N^2)

따라서 전체 알고리즘의 시간 복잡도는 O(N^2)

## 공간 복잡도

1. **`visited`** 리스트: **`visited`** 리스트는 모든 셀에 대해 최소 비용을 저장하기 위한 2차원 리스트다 따라서 공간 복잡도는 O(N^2)
2. **`q`** 큐: 큐 **`q`**는 BFS 탐색에 사용되며, 큐에 최대 N^2개의 셀 정보가 저장될 수 있다. 각 셀 정보는 **`(x, y, cost, d)`**의 튜플 형태로 저장되며, 튜플 하나당 4개의 값을 저장하므로 각 셀 정보는 상수 공간을 사용한다. 따라서 큐 **`q`**의 공간 복잡도는 O(N^2)

따라서 전체 알고리즘의 공간 복잡도는 O(N^2), 이는 미로의 크기에 비례하는 공간만 사용하므로 상대적으로 적은 메모리를 요구한다.