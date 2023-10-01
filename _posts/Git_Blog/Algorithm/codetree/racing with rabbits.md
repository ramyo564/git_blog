---
layout: single
title: "[코드트리 토끼와 경주] (알고리즘)"
categories: Algo
tags:
  - Python
  - 코드트리
toc: true
toc_sticky: true
author_profile: false
sidebar:
---

## 문제

[문제 링크](https://www.codetree.ai/training-field/frequent-problems/problems/rabit-and-race/description?page=1&pageSize=20)

토끼들이 점수를 건 경주를 진행하려고 합니다. 경주에 관한 정보는 아래와 같습니다.

(1) **경주 시작 준비**

P 마리의 토끼가 N×M 크기의 격자 위에서 경주를 진행할 준비를 합니다. 각 토끼에게는 고유한 번호가 붙어있으며, 한번 움직일 시 꼭 이동해야 하는 거리도 정해져 있습니다. i번 토끼의 고유번호는 pidi​, 이동해야 하는 거리는 di​ 입니다. 처음 토끼들은 전부 (1행, 1열)에 있습니다.

(2) **경주 진행**

가장 우선순위가 높은 토끼를 뽑아 멀리 보내주는 것을 K번 반복합니다.

우선순위는 순서대로 (현재까지의 총 점프 횟수가 적은 토끼, 현재 서있는 행 번호 + 열 번호가 작은 토끼, 행 번호가 작은 토끼, 열 번호가 작은 토끼, 고유번호가 작은 토끼) 순입니다. 첫 번째 우선순위가 높은 토끼가 한마리 뿐이라면 바로 결정되는 것이고, 동률이라면 두 번째 우선순위를 보고, .. 이러한 규칙에 의해 가장 우선순위가 높은 토끼가 결정됩니다.

우선순위가 가장 높은 토끼가 결정이 되면 이 토끼를 i번 토끼라 했을 때 상하좌우 네 방향으로 각각 di​만큼 이동했을 때의 위치를 구합니다. 이때 이동하는 도중 그 다음 칸이 격자를 벗어나게 된다면 방향을 반대로 바꿔 한 칸 이동하게 됩니다. 이렇게 구해진 4개의 위치 중 (행 번호 + 열 번호가 큰 칸, 행 번호가 큰 칸, 열 번호가 큰 칸) 순으로 우선순위를 두었을 때 가장 우선순위가 높은 칸을 골라 그 위치로 해당 토끼를 이동시킵니다. 이 칸의 위치를 (ri​,ci​)라 했을 때 i번 토끼를 제외한 나머지 P−1마리의 토끼들은 전부 ri​+ci​만큼의 점수를 동시에 얻게 됩니다.

이렇게 K번의 턴 동안 가장 우선순위가 높은 토끼를 뽑아 멀리 보내주는 것을 반복하게 되며, 이 과정에서 동일한 토끼가 여러번 선택되는 것 역시 가능합니다.

K번의 턴이 모두 진행된 직후에는 (현재 서있는 행 번호 + 열 번호가 큰 토끼, 행 번호가 큰 토끼, 열 번호가 큰 토끼, 고유번호가 큰 토끼) 순으로 우선순위를 두었을 때 가장 우선순위가 높은 토끼를 골라 점수 S를 더해주게 됩니다. 단, 이 경우에는 K번의 턴 동안 한번이라도 뽑혔던 적이 있던 토끼 중 가장 우선순위가 높은 토끼를 골라야만 함에 꼭 유의합니다.

(3) **이동거리 변경**

고유번호가 pidt​인 토끼의 이동거리를 L배 해줍니다. 단, 계산 도중 특정 토끼의 이동거리가 10억을 넘어가는 일은 발생하지 않음을 가정해도 좋습니다.

(4) **최고의 토끼 선정**

각 토끼가 모든 경주를 진행하며 얻은 점수 중 가장 높은 점수를 출력합니다.

  

Q번에 걸쳐 명령을 순서대로 진행하며 최고의 토끼를 선정해주는 프로그램을 작성해보세요.

### [](https://www.codetree.ai/training-field/frequent-problems/problems/rabit-and-race/description?page=1&pageSize=20#%EC%9E%85%EB%A0%A5-%ED%98%95%EC%8B%9D)입력 형식

첫 번째 줄에 명령의 수 Q가 주어집니다.

두 번째 줄부터는 Q개의 줄에 걸쳐 명령의 정보가 주어집니다. 각 명령에 따른 형태는 다음과 같습니다.

- 경주 시작 준비
    
    `100 N M P pid_1 d_1 pid_2 d_2 ... pid_p d_p` 형태로 공백을 사이에 두고 주어집니다. 이는 P 마리의 토끼가 N×M 크기의 격자 위에서 경주를 진행하며 i번 토끼의 고유 번호는 pidi​, 이동해야 하는 거리가 di​임을 뜻합니다. **이 명령은 항상 첫 번째 명령으로만 주어지며, 항상 주어집니다.** 또한, 이 명령에 대해서는 출력할 값이 없습니다.
    
- 경주 진행
    
    `200 K S` 형태로 공백을 사이에 두고 주어집니다. **이 명령은 최대 2000번까지만 주어집니다.**
    
- 이동거리 변경
    
    `300 pid_t L` 형태로 공백을 사이에 두고 주어집니다. **이 명령은 최대 2000번까지만 주어집니다.**
    
- 최고의 토끼 선정
    
    `400` 형태로 주어집니다. **이 명령어는 정확히 마지막 명령으로만 주어지며, 항상 주어집니다.**
    

- 2 ≤ Q ≤ 4,002
- 2 ≤ N,M ≤ 100,000
- 1 ≤ P ≤ 2,000
- 1 ≤ pidi​, pidt​ ≤ 10,000,000
- 1 ≤ di​ ≤ 1,000,000,000
- 1 ≤ K ≤ min(100,P)
- 1 ≤ S ≤ 1,000,000
- 1 ≤ L ≤ 1,000,000,000

### [](https://www.codetree.ai/training-field/frequent-problems/problems/rabit-and-race/description?page=1&pageSize=20#%EC%B6%9C%EB%A0%A5-%ED%98%95%EC%8B%9D)출력 형식

400명령어에 대해 P마리의 토끼의 최종 점수 중 최댓값을 출력합니다. 400 명령어는 맨 끝에 단 한번만 주어지기에, 답은 첫 번째 줄에만 출력하면 됩니다.

#### 입출력 예제

#### 예제1

입력:

```
5
100 3 5 2 10 2 20 5
200 6 100
300 10 2
200 3 20
400
```

출력:

```
151
```

### [](https://www.codetree.ai/training-field/frequent-problems/problems/rabit-and-race/description?page=1&pageSize=20#%EC%98%88%EC%A0%9C-%EC%84%A4%EB%AA%85)예제 설명

첫 번째 예제에 대해 자세히 알아보겠습니다.

먼저 `100` 명령과 함께 경주에 대한 정보가 3 x 5 크기의 격자에서 (1, 1)에 토끼 2마리가 놓여져 있으며 각 토끼는 (고유번호 10, 이동거리 2), (고유번호 20, 이동거리 5)임을 알 수 있습니다.

![](https://contents.codetree.ai/problems/3507/images/8f603b1b-3a38-4372-a6d9-7d5eb47d5698.png)

이제 `200 6 100` 명령이 주어졌기에 총 6번의 라운드가 진행됩니다.

첫 번째 라운드에서는 (현재까지의 총 점프 횟수, 현재 서있는 행 번호 + 열 번호, 행 번호, 열 번호)가 전부 동일하기에 고유번호가 더 작은 빨간 토끼가 움직이게 됩니다. 빨간 토끼의 최종 위치는 (3, 1)이 되므로 파란 토끼는 점수 4점을 얻게 됩니다.

![](https://contents.codetree.ai/problems/3507/images/899b1fef-11bf-4b07-9408-77cce317c404.png)

두 번째 라운드에서는 현재까지의 총 점프 횟수가 더 적은 파란 토끼가 움직이게 됩니다. 파란 토끼의 최종 위치는 (1, 4)가 되므로 빨간 토끼는 점수 5점을 얻게 됩니다.

![](https://contents.codetree.ai/problems/3507/images/70ff8876-db5e-4217-9d98-0996b8092780.png)

이렇게 3번째 라운드부터 6번째 라운드까지도 순서대로 진행하게 됩니다.

![](https://contents.codetree.ai/problems/3507/images/226430a3-919d-4d8b-af84-4061fce3328f.png)

![](https://contents.codetree.ai/problems/3507/images/24343eb2-bb54-4a2f-b143-6cc6d07c323c.png)

![](https://contents.codetree.ai/problems/3507/images/95649aba-fb01-4b14-8358-05c94aa2779b.png)

![](https://contents.codetree.ai/problems/3507/images/605f7706-0992-4f60-a11d-c3dcb1638fba.png)

이제 6번째 라운드가 종료되었기에 (현재 서있는 행 번호 + 열 번호가 큰 토끼, 행 번호가 큰 토끼, 열 번호가 큰 토끼, 고유번호가 큰 토끼) 순으로 우선순위를 두었을 때 가장 우선순위가 높은 토끼인 빨간 토끼가 100점을 얻어가게 됩니다.

![](https://contents.codetree.ai/problems/3507/images/0c462c2a-4a1e-4516-9ea3-9f8b9526ae87.png)

다음에는 `300 10 2` 명령이 주어졌기에 pid값이 10인 빨간 토끼의 이동거리가 2배가 되어 4가 됩니다.

![](https://contents.codetree.ai/problems/3507/images/b5fd0ee7-5d5c-4df5-8aa5-ea682b0f1f9f.png)

이제다시 `200 3 20` 명령이 주어졌기에 총 3번의 라운드가 아래와 같이 진행됩니다.

![](https://contents.codetree.ai/problems/3507/images/e58f47e3-f5ef-4433-bdcd-af1467ba2f9e.png)

![](https://contents.codetree.ai/problems/3507/images/94e97296-b826-455d-b733-33bf378d05b7.png)

![](https://contents.codetree.ai/problems/3507/images/d9558ad5-a1e3-48e3-810c-892be1f80785.png)

이제 3번째 라운드가 종료되었기에 (현재 서있는 행 번호 + 열 번호가 큰 토끼, 행 번호가 큰 토끼, 열 번호가 큰 토끼, 고유번호가 큰 토끼) 순으로 우선순위를 두었을 때 가장 우선순위가 높은 토끼인 빨간 토끼가 20점을 얻어가게 됩니다.

![](https://contents.codetree.ai/problems/3507/images/9978eb8e-5757-40ed-9b12-b911316c7f4c.png)

최종적으로 `400`의 명령이 주어지게 되어 가장 점수가 큰 값인 151이 답이 됩니다.

#### 제한

```
시간 제한: 2500ms
메모리 제한: 80MB
```
## 스스로 고민

- 알고리즘 스터디에서 해당 문제를 같이 풀어보자고 했다.
- 다들 구현하기 빡세다, 이것 저것 해야될 게 많아 보인다 라고들 했다.

![](https://media1.giphy.com/media/LMS8UP0DyfC4E/giphy.gif?cid=ecf05e477uk0i8vf0u0kto85gqcbvzn4qben5awu1kfzvyvw&ep=v1_gifs_search&rid=giphy.gif&ct=g)

- 하지만 난 뭐 부터 해야될 지 모르겠는 걸...
- 하나씩 차근차근 풀어보자
### 접근법

- 토끼가 경주를 시작한다. 
- 토끼는 P 마리
- N x M의 격자 위에서 경주를 시작한다.
- 각 토끼는 고유한 번호가 있고 한 번 움직일 때 이동 거리는 정해져 있음
- i번 토끼의 고유번호는 pid 고 d는 이동거리
- 가장 우선순위가 높은 토끼를 뽑아 멀리 보내주는 것을 k 번 반복
- 우선 순위는 아래와 같다.
	- 총 점프 횟수가 적은 토끼
	- 현재 서있는 행 번호 + 열 번호가 작은 토끼
	- 행 번호가 작은 토끼
	- 열 번호가 작은 토끼
	- 고유번호가 작은 토끼
- 우선 순위가 높은 토끼가 한 마리 뿐이라면 바로 결정 되며
- 동률이라면 두 번쨰 우선순위를 보고 이러한 규칙으로 가장 우선순위가 높은 토끼가 결정된다.

> 1. 우선순위가 가장 높은 토끼가 결정이 되면 이 토끼를 i 번 토끼라 하면 상하좌우 네 방향으로 각각 d 만큼 이동했을 때의 위치를 구한다.
> 2. 이 때 이동하는 도중 그 다음 칸이 격자를 벗어나게 된다면 방향을 반대로 바꿔 한 칸 이동하게 된다.
> 3. 이렇게 구해진 4개의 위치 중 다음과 같은 우선순위로 두었을 때 가장 우선순위가 높은 칸을 골라 그 위치로 해당 토끼를 이동시킨다.
> 	1. 행 번호 + 열 번호가 큰 칸
> 	2. 행 번호가 큰 칸, 열 번호가 큰칸
> 4. 이 칸의 위치를 (r, c) 라 했을 때 i번 토끼를 제외한 나머지 P -1마리의 토끼들은 전부 r + c 만큼의 점수를 동시에 얻게 된다.

이렇게 K 번의 턴 동안 가장 우선순위가 높은 토끼를 뽑아 멀리 보내주는 것을 반복하며 이 과정에서 동일한 토끼가 여러번 선택되는 것 역시 가능하다.     
K 번의 턴 동안 모두 진행된 직후에는 아래와 같은 순서로 우선순위를 두었을 때 가장 우선순위가 높은 토끼를 골라 점수 S를 더하게 된다.
( 단 이경우에는 K번의 턴 동안 한 번이라도 뽑았던 적이 있던 토끼 중 가장 우선순위가 높은 토끼를 골라야한다.)
- 현재 서있는 행 번호 + 열 번호가 큰 토끼, 행 번호가 큰 토끼, 열 번호가 큰 토끼, 고유번호가 큰 토끼

고유번호가 pid 인 토끼의 이동가리를 L 배 해준다. 단 계산 도중 특정 토끼의 이동거리가 10억을 넘어가는 일은 발생하지 않도록 해도 된다.

각 토끼가 모든 경주를 진행하며 얻은 점수 중 가장 높은 점수를 출력한다.

![](https://media0.giphy.com/media/rqOiHrynEe543tASYu/giphy.gif?cid=ecf05e470u0ivtf5fdtag8876dm8dtnfqlt7eggly53j8ots&ep=v1_gifs_search&rid=giphy.gif&ct=g)

- 뭔 소리지....
- 어떤 자료구조를 사용하면 효율적인지 조차 떠오르지가 않는다.
- 우선 각 조건을 나열하고 조건에 일치할 때마다 값을 업데이트 하는 방식으로 가야 될 거 같은데 그렇게 되면 반복문이 많이 사용될 거 같았다.
- 그럼 해시 맵으로 값을 불러오면서 해야 될까?
- 토끼들은 각 조건이 변할 때마다 우선순위도 변하므로 해당 값을 지속적으로 확인하고 꺼내올 수 있는 해시맵을 만들어서 풀어보려고 계획했다.

## 의사코드

- 우선 입력 값에 대한 전처리부터 시작해보자

```python
p = input()
x = list(map(int, input().split()))
box = []
box.append(x[1])
box.append(x[2])
# 총 토끼 수
rabit_num = x[3]
# 각 토끼에 대한 정보
rabbits = x[4:]
# 토끼 정보를 담을 딕셔너리
rabbits_dic = {}
# 토끼 데이터 딕셔너리로 처리
for i in range(len(rabbits) // 2):
    key = rabbits[i * 2]
    value = rabbits[i * 2 + 1]
    rabbits_dic[key] = value

```

이렇게 만들고 여러가지 고민을 했는데 1시간이 넘어가서 정답을 보고 분석하면서 공부하기로 했다.

![](https://media3.giphy.com/media/26sL67MuXgAX7AIjoL/giphy.gif?cid=ecf05e474cyyd95zs8se2ym333umva1yr6wfgsrenafe5dfv&ep=v1_gifs_search&rid=giphy.gif&ct=g)


### 문제 정답

```python
import heapq
MAX_N = 2000

# 변수 선언 및 입력:
n, m, p = 0, 0, 0

# 각 토끼의 id를 기록해줍니다.
pid = [0] * (MAX_N + 1)

# 각 토끼의 이동거리를 기록해줍니다.
pw = [0] * (MAX_N + 1)

# 각 토끼의 점프 횟수를 기록해줍니다.
jump_cnt = [0] * (MAX_N + 1)

# 각 토끼의 점수를 기록해줍니다.
result = [0] * (MAX_N + 1)

# 각 토끼의 현재 위치(좌표)를 기록해줍니다.
point = [(0, 0)] * (MAX_N + 1)

# 각 토끼의 id를 인덱스 번호로 변환해줍니다.
id_to_idx = {}

# 각각의 경주에서 토끼가 달렸는지 여부를 기록해줍니다.
is_runned = [0] * (MAX_N + 1)

# 하나를 제외한 모든 토끼의 점수를 더하는 쿼리를 편하게 하기 위해
# total_sum이라는 변수를 추가해줍니다.
total_sum = 0

# 구조체 rabbit을 정리해 관리합니다.
class Rabbit:
    def __init__(self, x, y, j, pid):
        self.x = x
        self.y = y
        self.j = j
        self.pid = pid

    # 이동할 토끼를 결정하기 위해 정렬함수를 만들어줍니다.
    def __lt__(self, other):
        if self.j != other.j:
            return self.j < other.j
            
        if self.x + self.y != other.x + other.y:
            return self.x + self.y < other.x + other.y

        if self.x != other.x:
            return self.x < other.x

        if self.y != other.y:
            return self.y < other.y

        return self.pid < other.pid


# 가장 긴 위치를 판단하기 위해 정렬함수를 하나 더 만들어줍니다.
def compare(a, b):
    if a.x + a.y != b.x + b.y:
        return a.x + a.y < b.x + b.y

    if a.x != b.x:
        return a.x < b.x

    if a.y != b.y:
        return a.y < b.y

    return a.pid < b.pid

  
# 경주 시작 준비 쿼리를 처리해줍니다.
def init(inp):
    global n, m, p

    n, m, p, *rabbits = inp
    for i in range(1, p + 1):
        pid[i] = rabbits[i * 2 - 2]
        pw[i] = rabbits[i * 2 - 1]
        id_to_idx[pid[i]] = i
        point[i] = (1, 1)

# 토끼를 위로 이동시킵니다.
def get_up_rabbit(cur_rabbit, dis):
    up_rabbit = cur_rabbit
    dis %= 2 * (n - 1)

    if dis >= up_rabbit.x - 1:
        dis -= (up_rabbit.x - 1)
        up_rabbit.x = 1

    else:
        up_rabbit.x -= dis
        dis = 0
        
    if dis >= n - up_rabbit.x:
        dis -= (n - up_rabbit.x)
        up_rabbit.x = n

    else:
        up_rabbit.x += dis
        dis = 0

    up_rabbit.x -= dis

    return up_rabbit

# 토끼를 아래로 이동시킵니다.
def get_down_rabbit(cur_rabbit, dis):
    down_rabbit = cur_rabbit
    dis %= 2 * (n - 1)

    if dis >= n - down_rabbit.x:
        dis -= (n - down_rabbit.x)
        down_rabbit.x = n

    else:
        down_rabbit.x += dis
        dis = 0

    if dis >= down_rabbit.x - 1:
        dis -= (down_rabbit.x - 1)
        down_rabbit.x = 1

    else:
        down_rabbit.x -= dis
        dis = 0
        
    down_rabbit.x += dis
    return down_rabbit

# 토끼를 왼쪽으로 이동시킵니다.
def get_left_rabbit(cur_rabbit, dis):
    left_rabbit = cur_rabbit
    dis %= 2 * (m - 1)

    if dis >= left_rabbit.y - 1:
        dis -= (left_rabbit.y - 1)
        left_rabbit.y = 1

    else:
        left_rabbit.y -= dis
        dis = 0

    if dis >= m - left_rabbit.y:
        dis -= (m - left_rabbit.y)
        left_rabbit.y = m

    else:
        left_rabbit.y += dis
        dis = 0

    left_rabbit.y -= dis
    
    return left_rabbit

  
# 토끼를 오른쪽으로 이동시킵니다.
def get_right_rabbit(cur_rabbit, dis):
    right_rabbit = cur_rabbit
    dis %= 2 * (m - 1)

    if dis >= m - right_rabbit.y:
        dis -= (m - right_rabbit.y)
        right_rabbit.y = m

    else:
        right_rabbit.y += dis
        dis = 0

    if dis >= right_rabbit.y - 1:
        dis -= (right_rabbit.y - 1)
        right_rabbit.y = 1

    else:
        right_rabbit.y -= dis
        dis = 0
    right_rabbit.y += dis

    return right_rabbit

def copy_rabbit(rabbit):
    return Rabbit(rabbit.x, rabbit.y, rabbit.j, rabbit.pid)

  
# 경주를 진행합니다.
def start_round(inp):
    global total_sum

    k, bonus = inp
    rabbit_pq = []

    for i in range(1, p + 1):
        is_runned[i] = False

    # 우선 p마리의 토끼들을 전부 priority queue에 넣어줍니다.
    for i in range(1, p + 1):
        x, y = point[i]
        new_rabbit = Rabbit(x, y, jump_cnt[i], pid[i])
        heapq.heappush(rabbit_pq, new_rabbit)

    # 경주를 k회 진행합니다.
    for _ in range(k):
        # 우선순위가 가장 높은 토끼를 priority queue에서 뽑아옵니다.
        cur_rabbit = heapq.heappop(rabbit_pq)
        
        # 해당 토끼를 상, 하, 좌, 우 4개의 방향으로 이동시킵니다.
        # 각각의 방향으로 이동시킨 후 최종 위치를 구하고
        # 가장 시작점으로부터 멀리 있는 위치를 찾아줍니다.
        dis = pw[id_to_idx[cur_rabbit.pid]]
        nex_rabbit = copy_rabbit(cur_rabbit)
        nex_rabbit.x = 0
        nex_rabbit.y = 0

        # 토끼를 위로 이동시킵니다.
        up_rabbit = get_up_rabbit(copy_rabbit(cur_rabbit), dis)
        # 지금까지의 도착지들보다 더 멀리 갈 수 있다면 도착지를 갱신합니다.
        if compare(nex_rabbit, up_rabbit):
            nex_rabbit = up_rabbit

        # 토끼를 아래로 이동시킵니다.
        down_rabbit = get_down_rabbit(copy_rabbit(cur_rabbit), dis)

        # 지금까지의 도착지들보다 더 멀리 갈 수 있다면 도착지를 갱신합니다.
        if compare(nex_rabbit, down_rabbit):
            nex_rabbit = down_rabbit

        # 토끼를 왼쪽으로 이동시킵니다.
        left_rabbit = get_left_rabbit(copy_rabbit(cur_rabbit), dis)

        # 지금까지의 도착지들보다 더 멀리 갈 수 있다면 도착지를 갱신합니다.
        if compare(nex_rabbit, left_rabbit):
            nex_rabbit = left_rabbit

        # 토끼를 오른쪽으로 이동시킵니다.
        right_rabbit = get_right_rabbit(copy_rabbit(cur_rabbit), dis)
        # 지금까지의 도착지들보다 더 멀리 갈 수 있다면 도착지를 갱신합니다.
        if compare(nex_rabbit, right_rabbit):
            nex_rabbit = right_rabbit

        # 토끼의 점프 횟수를 갱신해주고, priority queue에 다시 집어넣습니다.
        nex_rabbit.j += 1
        heapq.heappush(rabbit_pq, nex_rabbit)

        # 실제 point, jump_cnt 배열에도 값을 갱신해줍니다.
        nex_idx = id_to_idx[nex_rabbit.pid]
        point[nex_idx] = (nex_rabbit.x, nex_rabbit.y)
        jump_cnt[nex_idx] += 1

        # 토끼가 달렸는지 여부를 체크해줍니다.
        is_runned[nex_idx] = True

        # 토끼가 받는 점수는 (현재 뛴 토끼)만 (r + c)만큼 점수를 빼주고,
        # 모든 토끼(total_sum)에게 (r + c)만큼 점수를 더해줍니다.
        # 최종적으로 i번 토끼가 받는 점수는 result[i] + total_sum이 됩니다.
        result[nex_idx] -= (nex_rabbit.x + nex_rabbit.y)
        total_sum += (nex_rabbit.x + nex_rabbit.y)

    # 보너스 점수를 받을 토끼를 찾습니다.
    # 이번 경주때 달린 토끼 중 가장 멀리있는 토끼를 찾습니다.
    bonus_rabbit = Rabbit(0, 0, 0, 0)
    while rabbit_pq:
        cur_rabbit = heapq.heappop(rabbit_pq)

        # 달리지 않은 토끼는 스킵합니다.
        if not is_runned[id_to_idx[cur_rabbit.pid]]:
            continue

        if compare(bonus_rabbit, cur_rabbit):
            bonus_rabbit = cur_rabbit

    # 해당 토끼에게 bonus만큼 점수를 추가해줍니다.
    result[id_to_idx[bonus_rabbit.pid]] += bonus

# 이동거리를 변경합니다.
def power_up(inp):
    pid, t = inp
    idx = id_to_idx[pid]
    pw[idx] *= t

# 최고의 토끼를 선정합니다.
def print_result():
    ans = 0
    for i in range(1, p + 1):
        ans = max(ans, result[i] + total_sum)
    print(ans)

q = int(input())
for _ in range(q):
    query, *inp = list(map(int, input().split()))
    
    # 경주 시작 준비 쿼리를 처리해줍니다.
    if query == 100:
        init(inp)

    # 경주를 진행합니다.
    if query == 200:
        start_round(inp)

    # 이동거리를 변경합니다.
    if query == 300:
        power_up(inp)

    # 최고의 토끼를 선정합니다.
    if query == 400:
        print_result()
```


### 느낀점

- 내가 생각한 것 보다 훨씬 복잡했다.
- 우선 해답을 보고 가장 문제라고 생각한 점은 생각보다 문제들을 잘게 쪼개서 생각하지 못 했던 점이라고 생각한다.
- 두 번째로는 힙에 대해서 배웠는지만 적용할 생각을 하지 못 했다. 이건 내가 힙에 대해서 응용을 할 줄 모른다는 소리랑 같다.
- 슬프지만 내 수준에 맞는 문제가 아닌 것 같다. 너무 어렵다.
	- 아직 내 수준에 맞지 않는다고 생각해서 백준 힙 문제 실버로 넘어갔다
	- 실버 문제를 풀면서 느낀 점은 내가 힙에 대해 잘 알고 있다고 착각하고 있었다는 것!
	- 힙을 풀면서 레벨 업이 되었다고 생각할 때 마다 계속 이어서 글을 쓰고 있다 ㅋㅋ
	- 오래 걸려도 완벽히 이해할 것 !

### 해답보고 공부하기

- 스터디에서 1주일에 한 문제씩 숙제로 나온다.
- 문제를 풀 수 없다면 내가 다른 사람에게 설명해줄 수 있을 정도로 이해를 하자

#### 내가 몰랐던 점 

위에서 데이터를 받아 정리할 때 데이터의 갯수가 달라질 경우의 처리를 스라이싱으로 해결했는데  `*` 를 사용하면 쉽게 해결 할 수 있는 걸 알게 되었다.     


```python
q = int(input())
for _ in range(q):
    query, *inp = list(map(int, input().split()))

    # 경주 시작 준비 쿼리를 처리해줍니다.
    if query == 100:
        init(inp)
        
    # 경주를 진행합니다.
    if query == 200:
        start_round(inp)

    # 이동거리를 변경합니다.
    if query == 300:
        power_up(inp)

    # 최고의 토끼를 선정합니다.
    if query == 400:
        print_result()
```

이 부분을 보고 `q = int(input())` 하나로 입력단을 해결하면서 `query, *inp ` 가 각각 어떤 식으로 되는지 궁금해서 찾아봤다.     

##### 언팩킹

`*inp`는 파이썬에서 사용되는 별표(`*`) 연산자를 통해 가변 개수의 인자를 받을 때 사용하는 문법이다. 이를 "언패킹"이라고 한다.     

위 코드에서 `*inp`는 입력값을 공백으로 나눈 결과를 리스트로 저장하는 부분이다. 즉, `map(int, input().split())`를 통해 입력된 값을 공백을 기준으로 나눈 후, 그 결과를 정수로 변환하여 리스트에 저장한다.

예를 들어, 다음과 같은 입력이 주어진다고 가정해본다면

```python
3
100 3 5 2 10 2 20 5
200 6 100
300 10 2
```

첫 번째 루프에서 `query`는 `100`이 되고, `*inp`는 `[3, 5, 2, 10, 2, 20, 5]`가 된다. 두 번째 루프에서 `query`는 `200`이 되고, `*inp`는 `[6, 100]`이 된다. 즉, `*inp`를 사용하면 입력값을 리스트로 편리하게 처리할 수 있다.

(`*inp` 는 예약어가 아니고 그냥 변수 앞에 `*` 만 붙여서 사용할 수 있다.)


#### 다시 문제로 돌아가서

```python
q = int(input())
for _ in range(q):
    query, *inp = list(map(int, input().split()))

    # 경주 시작 준비 쿼리를 처리해줍니다.
    if query == 100:
        init(inp)
        
    # 경주를 진행합니다.
    if query == 200:
        start_round(inp)

    # 이동거리를 변경합니다.
    if query == 300:
        power_up(inp)

    # 최고의 토끼를 선정합니다.
    if query == 400:
        print_result()
```

입출력은 다음과 같이 나오는데

```
5
100 3 5 2 10 2 20 5
200 6 100
300 10 2
200 3 20
400
```

처음에 100,200 이 값을 왜 주나 했는데 위와 같이 쿼리처럼 쓰라는 거였다. 나는 이걸 몰라서 따로 슬라이싱 하고 딕셔너리를 만들고 있었으니 여러모로 비효율적이였다고 생각했다.      

(이런 부분 때문에 문제가 어렵더라도 포기하면 안된다고 생각했다. 시간이 오래 걸리고 문제를 풀지 못 했더라고 이해하고 넘어가자!)

![](https://media0.giphy.com/media/YRzCch08Pmg4zkwlAV/giphy.webp)

아무튼 위와 같이 가변변수를 언팩킹으로 해결하고 난 다음 각 쿼리가 어떻게 진행되는지 보자      

#### 100 이라는 입력을 받았을 때

```python
    # 경주 시작 준비 쿼리를 처리해줍니다.
    if query == 100:
        init(inp)

# 100에 관련된 입력 값
100 3 5 2 10 2 20 5
```


```python
# 경주 시작 준비 쿼리를 처리해줍니다.
def init(inp):
    global n, m, p
    n, m, p, *rabbits = inp
    
    for i in range(1, p + 1):
        pid[i] = rabbits[i * 2 - 2]
        pw[i] = rabbits[i * 2 - 1]
        id_to_idx[pid[i]] = i
        point[i] = (1, 1)
```

경주 시작에 대한 초기 설정이 이루어진다.     
전역변수로 n,m,p 가 있는데 그 부분을 불러오면 아래와 같다.     

```
# 변수 선언 및 입력:
n, m, p = 0, 0, 0
```

n = 행, m = 열, p = 총 토끼 수
그리고 그 이외는 언팩킹으로 처리한다.     

```
100 3 5 2 10 2 20 5
```


```python
# 경주 시작 준비 쿼리를 처리해줍니다.
def init(inp):
    global n, m, p
    n, m, p, *rabbits = inp
    
    for i in range(1, p + 1):
        pid[i] = rabbits[i * 2 - 2]
        pw[i] = rabbits[i * 2 - 1]
        id_to_idx[pid[i]] = i
        point[i] = (1, 1)
```

이제 토끼에 대한 정보들을 넣어준다.

```python
MAX_N = 2000

# 각 토끼의 id를 기록해줍니다.
pid = [0] * (MAX_N + 1)

# 각 토끼의 이동거리를 기록해줍니다.
pw = [0] * (MAX_N + 1)

# 각 토끼의 id를 인덱스 번호로 변환해줍니다.
id_to_idx = {}
```


```
100 3 5 2 10 2 20 5
```

- rabbits 는 `[10,2,20,5]` 의 리스트 형식으로 저장되고 반복문이 돌아가면서 각 정보가 알아서 들어간다.
- 각 토끼들을 1,1 로 위치를 초기화 시켜준다.

#### 200 이라는 입력을 받았을 때

```python
    # 경주를 진행합니다.
    if query == 200:
        start_round(inp)
```


```python
  
# 경주를 진행합니다.
def start_round(inp):
    global total_sum
    k, bonus = inp
    rabbit_pq = []

    for i in range(1, p + 1):
        is_runned[i] = False

    # 우선 p마리의 토끼들을 전부 priority queue에 넣어줍니다.
    for i in range(1, p + 1):
        x, y = point[i]
        new_rabbit = Rabbit(x, y, jump_cnt[i], pid[i])
        heapq.heappush(rabbit_pq, new_rabbit)

    # 경주를 k회 진행합니다.
    for _ in range(k):
        # 우선순위가 가장 높은 토끼를 priority queue에서 뽑아옵니다.
        cur_rabbit = heapq.heappop(rabbit_pq)

        # 해당 토끼를 상, 하, 좌, 우 4개의 방향으로 이동시킵니다.
        # 각각의 방향으로 이동시킨 후 최종 위치를 구하고
        # 가장 시작점으로부터 멀리 있는 위치를 찾아줍니다.

        dis = pw[id_to_idx[cur_rabbit.pid]]
        nex_rabbit = copy_rabbit(cur_rabbit)
        nex_rabbit.x = 0
        nex_rabbit.y = 0

        # 토끼를 위로 이동시킵니다.
        up_rabbit = get_up_rabbit(copy_rabbit(cur_rabbit), dis)

        # 지금까지의 도착지들보다 더 멀리 갈 수 있다면 도착지를 갱신합니다.
        if compare(nex_rabbit, up_rabbit):
            nex_rabbit = up_rabbit

        # 토끼를 아래로 이동시킵니다.
        down_rabbit = get_down_rabbit(copy_rabbit(cur_rabbit), dis)

        # 지금까지의 도착지들보다 더 멀리 갈 수 있다면 도착지를 갱신합니다.
        if compare(nex_rabbit, down_rabbit):
            nex_rabbit = down_rabbit

        # 토끼를 왼쪽으로 이동시킵니다.
        left_rabbit = get_left_rabbit(copy_rabbit(cur_rabbit), dis)

        # 지금까지의 도착지들보다 더 멀리 갈 수 있다면 도착지를 갱신합니다.
        if compare(nex_rabbit, left_rabbit):
            nex_rabbit = left_rabbit

        # 토끼를 오른쪽으로 이동시킵니다.
        right_rabbit = get_right_rabbit(copy_rabbit(cur_rabbit), dis)

        # 지금까지의 도착지들보다 더 멀리 갈 수 있다면 도착지를 갱신합니다.
        if compare(nex_rabbit, right_rabbit):
            nex_rabbit = right_rabbit

        # 토끼의 점프 횟수를 갱신해주고, priority queue에 다시 집어넣습니다.
        nex_rabbit.j += 1
        heapq.heappush(rabbit_pq, nex_rabbit)

        # 실제 point, jump_cnt 배열에도 값을 갱신해줍니다.
        nex_idx = id_to_idx[nex_rabbit.pid]
        point[nex_idx] = (nex_rabbit.x, nex_rabbit.y)
        jump_cnt[nex_idx] += 1

        # 토끼가 달렸는지 여부를 체크해줍니다.
        is_runned[nex_idx] = True

        # 토끼가 받는 점수는 (현재 뛴 토끼)만 (r + c)만큼 점수를 빼주고,
        # 모든 토끼(total_sum)에게 (r + c)만큼 점수를 더해줍니다.
        # 최종적으로 i번 토끼가 받는 점수는 result[i] + total_sum이 됩니다.

        result[nex_idx] -= (nex_rabbit.x + nex_rabbit.y)
        total_sum += (nex_rabbit.x + nex_rabbit.y)

    # 보너스 점수를 받을 토끼를 찾습니다.
    # 이번 경주때 달린 토끼 중 가장 멀리있는 토끼를 찾습니다.
    bonus_rabbit = Rabbit(0, 0, 0, 0)
    while rabbit_pq:
        cur_rabbit = heapq.heappop(rabbit_pq)

        # 달리지 않은 토끼는 스킵합니다.
        if not is_runned[id_to_idx[cur_rabbit.pid]]:
            continue

        if compare(bonus_rabbit, cur_rabbit):
            bonus_rabbit = cur_rabbit

    # 해당 토끼에게 bonus만큼 점수를 추가해줍니다.
    result[id_to_idx[bonus_rabbit.pid]] += bonus
```

##### 너무 길어서 싹둑

```python
# 경주를 진행합니다.
def start_round(inp):
    global total_sum
    k, bonus = inp
    rabbit_pq = []

    for i in range(1, p + 1):
        is_runned[i] = False

    # 우선 p마리의 토끼들을 전부 priority queue에 넣어줍니다.
    for i in range(1, p + 1):
        x, y = point[i]
        new_rabbit = Rabbit(x, y, jump_cnt[i], pid[i])
        heapq.heappush(rabbit_pq, new_rabbit)
```

```
200 6 100
```

여기서 200은 짤리므로 inp 값은 `[6, 10]`
- k = 6, bonus = 10

```python
# 각각의 경주에서 토끼가 달렸는지 여부를 기록해줍니다.
is_runned = [0] * (MAX_N + 1)

# 각 토끼의 현재 위치(좌표)를 기록해줍니다.
point = [(0, 0)] * (MAX_N + 1)

# 각 토끼의 점프 횟수를 기록해줍니다.
jump_cnt = [0] * (MAX_N + 1)

# 각 토끼의 id를 기록해줍니다.
pid = [0] * (MAX_N + 1)
```

#### 300 이라는 입력을 받았을 때


#### 400 이라는 입력을 받았을 때

