---
layout: single
title: "[코드트리 바이러스 검사] (알고리즘)"
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

`BFS` `DFS`

[문제 링크](https://www.codetree.ai/training-field/frequent-problems/problems/virus-detector/description?page=3&pageSize=20)
바이러스의 확산을 막기 위해 총 n개의 식당에 있는 고객들의 체온을 측정하고자 합니다. 체온을 측정하는 검사자는 검사팀장과 검사팀원으로 나뉘어집니다. 팀장과 팀원이 검사할 수 있는 고객의 수가 다르며, 한 가게당 팀장은 오직 한 명, 팀원은 여러명 있을 수 있습니다. 하지만 가게당 팀장 한 명은 무조건 필요합니다. 가게에 검사팀원만 존재하는 경우는 있을 수 없습니다. 팀장이든 팀원이든 담당한 가게에 대해서만 검사합니다.

n개의 식당 고객들의 체온을 측정하기 위해 필요한 검사자 수의 최솟값을 구하는 프로그램을 작성해주세요.

## [](https://www.codetree.ai/training-field/frequent-problems/problems/virus-detector/description?page=3&pageSize=20#%EC%9E%85%EB%A0%A5-%ED%98%95%EC%8B%9D)입력 형식

첫째 줄에는 식당의 수 n이 주어집니다.

둘째 줄에는 각 식당에 있는 고객의 수가 공백을 사이에 두고 주어집니다.

셋째줄에는 검사팀장이 검사할 수 있는 최대 고객 수와 검사팀원이 검사할 수 있는 최대 고객 수가 공백을 사이에 두고 주어집니다.

- 1 ≤ n ≤ 1,000,000
    
- 1 ≤ (각 식당에 있는 고객의 수) ≤ 1,000,000
    
- 1 ≤ (팀장 혹은 팀원 한 명이 검사 가능한 최대 고객의 수) ≤ 1,000,000
    

## [](https://www.codetree.ai/training-field/frequent-problems/problems/virus-detector/description?page=3&pageSize=20#%EC%B6%9C%EB%A0%A5-%ED%98%95%EC%8B%9D)출력 형식

n개의 식당의 고객들을 모두 검사하기 위한 검사자의 최소의 수를 출력하세요

#### 입출력 예제

#### 예제1

입력:

```
1
1
2 2
```

출력:

```
1
```

#### 예제2

입력:

```
5
999999 999999 999999 999999 999999
111111 5
```

출력:

```
888895
```

#### 예제3

입력:

```
3
10 15 13
7 14
```

출력:

```
6
```
## 스스로 고민

### 접근법

### 입력

## 의사코드




## 구현 코드

## 시간 복잡도와 공간 복잡도

## 회고 과정

### 지금 코드에서 뭘 개선할 수 있지?

### 다른 사람 코드를 보고 그 기준으로 어떤 부분을 개선 할 수 있는지 최종 회고
