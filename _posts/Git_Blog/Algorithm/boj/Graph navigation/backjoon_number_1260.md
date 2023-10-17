---
layout: single
title: "[백준 1260번 DFS와 BFS] (알고리즘)"
categories: Algo
tags:
  - Python
  - 백준
  - BFS
  - DFS
  - Graph
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
`Graph`
## 문제

`BFS` `DFS`



[문제 링크](https://www.acmicpc.net/problem/1260)

그래프를 DFS로 탐색한 결과와 BFS로 탐색한 결과를 출력하는 프로그램을 작성하시오. 단, 방문할 수 있는 정점이 여러 개인 경우에는 정점 번호가 작은 것을 먼저 방문하고, 더 이상 방문할 수 있는 점이 없는 경우 종료한다. 정점 번호는 1번부터 N번까지이다.
## 스스로 고민

지금까지 봤던 자료구조랑 알고리즘 문제들은 어떻게든 비빌 수 있었는데 BFS와 DFS에 대해서는 좀 더 많이 풀어봐야 겠다고 생각했다.     

예전에 몇 번 백준에서 문제를 풀려고 했을 때 답안지 출력이나 문제 입력이 번거로운 이유 때문에 미뤄왔는데 이제 더 이상 미루지 말고 여기서 연습하기로 했다.

![](https://media2.giphy.com/media/l0MYzAwMPl8s0jtSg/giphy.gif?cid=ecf05e47tmgqhb8tuanrf7v8r5xg1smrjz5ddy86hwxoyq92&ep=v1_gifs_search&rid=giphy.gif&ct=g)

- 사실 하기 싫어서 여기까지 작성하는데도 오래 걸렸다 하하하
- 근데 처음이 힘들지 하다보면 괜찮아질거란 믿음으로 고고
### 접근법

문제가 한국어로 써있는데도 전혀 반갑지가 않다.

![](https://media3.giphy.com/media/mCRJDo24UvJMA/giphy.gif?cid=ecf05e47khdqhiqeokj7n5qnbyizgqsathgjmyol1g9wb2qi&ep=v1_gifs_search&rid=giphy.gif&ct=g)
문제 입력이랑 출력부터 어떻게 하는지 찾아봤다.
(이겨내! 극복해!)

### 입력

첫째 줄에 정점의 개수 N(1 ≤ N ≤ 1,000), 간선의 개수 M(1 ≤ M ≤ 10,000), 탐색을 시작할 정점의 번호 V가 주어진다. 다음 M개의 줄에는 간선이 연결하는 두 정점의 번호가 주어진다. 어떤 두 정점 사이에 여러 개의 간선이 있을 수 있다. 입력으로 주어지는 간선은 양방향이다

예제가 다음과 같이 주어질 때
```
4 5 1
1 2
1 3
1 4
2 4
3 4
```

이걸 어떻게 넣는지 찾아보니 아래와 같이 해결할 수 있다.

```
N, M, V = map(int, input().split())
```

n은 정점,  m은 간선의 개수, v는 시작할 정점의 숫자
그리고 아래의 숫자들은 각 숫자 정점들이 어떻게 연결되어 있는지를 나타낸다.

![](https://i.imgur.com/aZKw9m1.png)

(이해가 안될 때는 시각화해야 된다.)

근데 그래서 이걸 또 어떻게 해야되는거지

![](https://media0.giphy.com/media/9CffOPMLx0Hf2/giphy.gif?cid=ecf05e47cb3du3n260iran13a7lvtgps7xps6dwj99ug997k&ep=v1_gifs_search&rid=giphy.gif&ct=g)

손가락 군대 출동

좀 잘 모르겠어서 힌트로 다른 사람의 풀이 방식을 염탐했다.

```
N, M, V = map(int, input().split())

graph = [[False] * (N + 1) for _ in range(N + 1)]

for _ in range(M):
    a, b = map(int, input().split())
    graph[a][b] = True
    graph[b][a] = True

visited1 = [False] * (N + 1)  # dfs의 방문기록
visited2 = [False] * (N + 1)  # bfs의 방문기록
```

다음과 같이 그래프를 일단 만들어야 된다.
중복 탐색이 되지 않도록 False 맵을 만든다.     

```
4 5 1
1 2
1 3
1 4
2 4
3 4
```

첫 줄 451이 통과되면
현재 n = 4,  m = 5, v = 1

```
graph = [[False] * (N + 1) for _ in range(N + 1)]
```

그러면 간선의 인과관계가 나온다.

```
[
	[False, False, False, False, False],
	[False, False, False, False, False],
	[False, False, False, False, False],
	[False, False, False, False, False],
	[False, False, False, False, False]
]
```


graph의 결과 값은 아래와 같다.

```
[
	[False, False, False, False, False],
	[False, False, True, True, True],
	[False, True, False, False, True],
	[False, True, False, False, True],
	[False, True, True, True, False]
]
```

![](https://media1.giphy.com/media/ah7KwjMNJlhtK/giphy.gif?cid=ecf05e47t27t8pbwaiy19gsln9n0o5newr9ylt50uzjuw6us&ep=v1_gifs_related&rid=giphy.gif&ct=g)

머리 아프지만 정리를 하자면 입력에서 각 줄은 두 개의 정점을 나타내며, 각 줄의 정점 쌍에 대해 `graph` 배열에서 해당 정점들을 연결하는 부분을 `True`로 설정한다. 예를 들어, `1 2`의 입력을 처리할 때, `graph[1][2]`와 `graph[2][1]`을 `True`로 설정하여 정점 1과 2 사이에 간선이 있음을 나타낸다.       

```
[
	[False, False, False, False, False],
	[False, False, True, False, False],
	[False, True, False, False, False],
	[False, False, False, False, False],
	[False, False, False, False, False]
]
```

입력이 `1 3`일 때도, `graph[1][3]`과 `graph[3][1]`을 `True`로 설정하여 정점 1과 3 사이에 간선이 있음을 나타내며, 이와 같은 방식으로 입력에 따라 `True`로 설정된다.

```
[
	[False, False, False, False, False],
	[False, False, True, True, False],
	[False, True, False, False, False],
	[False, True, False, False, False],
	[False, False, False, False, False]
]
```

위와 같이 그래프는 각 정점들이 어떻게 간선과 연결되어있는지 나타낸다.
이제 이걸 가지고 문제를 풀어보자!
## 의사코드

사람들 말로는 bfs 는 큐 dfs 는 재귀 혹은 스택을 이용한다고 한다.

bfs를 어떻게 만들까?
- 우선 중복방지를 위해 방문 기록 리스트를 만들어준다.
- 변수를 선언하고 deque에 해당 방문 기록 리스트를 넣어준다.
- 와일문 안에서 큐 값(맨 처음 값) 을 뽑아내고 정점 갯 수 만큼 반복문을 돌려준다.
- 




## 구현 코드

## 시간 복잡도와 공간 복잡도

## 회고 과정

### 지금 코드에서 뭘 개선할 수 있지?

### 다른 사람 코드를 보고 그 기준으로 어떤 부분을 개선 할 수 있는지 최종 회고
