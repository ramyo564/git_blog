---
layout: single
title: "[122. Best Time to Buy and Sell Stock II] (알고리즘)"
categories: Algo
tags:
  - leetcode
  - Python
  - Array
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
`Array`
## 문제 

[문제 링크](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/)

You are given an integer array `prices` where `prices[i]` is the price of a given stock on the `ith` day.

On each day, you may decide to buy and/or sell the stock. You can only hold **at most one** share of the stock at any time. However, you can buy it then immediately sell it on the **same day**.

Find and return _the **maximum** profit you can achieve_.

**Example 1:**

**Input:** prices = [7,1,5,3,6,4]
**Output:** 7
**Explanation:** Buy on day 2 (price = 1) and sell on day 3 (price = 5), profit = 5-1 = 4.
Then buy on day 4 (price = 3) and sell on day 5 (price = 6), profit = 6-3 = 3.
Total profit is 4 + 3 = 7.

**Example 2:**

**Input:** prices = [1,2,3,4,5]
**Output:** 4
**Explanation:** Buy on day 1 (price = 1) and sell on day 5 (price = 5), profit = 5-1 = 4.
Total profit is 4.

**Example 3:**

**Input:** prices = [7,6,4,3,1]
**Output:** 0
**Explanation:** There is no way to make a positive profit, so we never buy the stock to achieve the maximum profit of 0.
## 스스로 고민

### 접근법

- 현재 가격이 다음날 가격 보다 싸면 구매
- 구매한 가격을 다음날 판매하는 것과 그 다음날 파는 것과 비교해서 더 큰 값으로 계속 갱신
- 만약 지금 산 가격이 1 내일이 5 그 다음이 3라면 가격이 내려가기 전날 판매
- 판매한 다음날 주식 구매

## 의사코드

- 기록을 하면서 구현 해보고 다시 의사코드를 작성하고 수정하는 식으로 해야되는데 이 때 무지성으로 하다보니 기록을 제대로 하지 못 했다..

## 구현 코드

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        current_price = prices[0]
        profit = 0
        index = len(prices)
        jump = 0
        for i in range(index-1):
            if prices[i] == jump:
                continue

            if current_price > prices[i + 1]:
                current_price = prices[i + 1]
                jump = current_price
            else:
                if current_price == 0:
                    continue
                else:
                    if prices[i] - current_price < prices[i + 1] - current_price:
                        if index < 4:
                            profit = prices[i + 1] - current_price
                            jump = prices[i + 1]
                        else:
                            if i + 2 == index:
                                profit += prices[index-1] - current_price

                            else:
                                pass
                    else:
                        profit += prices[i] - current_price
                        jump = prices[i+1]
                        current_price = jump
        if index == 3 and prices[index-1] - current_price > prices[index-2] - current_price:
            profit = prices[index-1] - current_price
        elif index == 3 and prices[index-1] - current_price < prices[index-2] - current_price:
            profit = prices[index-2] - current_price
        elif index == 2 and prices[1] > prices[0]:
            profit = prices[1] - prices[0]
        elif index == 2 and prices[1] < prices[0]:
            profit = 0
        elif index < 2:
            profit = 0
            
        return profit
```

- 연속으로 문제만 풀다보니 집중력이 떨어져서 계속 오류만 나는 코드를 만들었다.
- 코드가 이런 식으로 작성이 된다면 무조건 쉬어야 한다.
- 쉬면서 공간복잡도에 대해 신경 쓰고 최대한 변수를 적게 쓰면서 해결할 방법이 있을지 생각했다.

### 구현 코드

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        profit = 0

        for i in range(1,len(prices)):

            if prices[i] > prices[i-1]:

                profit += prices[i] - prices[i-1]

        return profit
```

- 다이어트한다는 생각으로 변수를 최대한 적게 사용한다 (메모리 최적화? 를 위해서)
- 일단 profit 하나만 사용해서 최대한 해결할 수 있는 방법을 생각해봤다.
- 단순하게 내일 가격보다 오늘 가격이 싸면 수익화 시켜서 누적 시킨다.
- 처음에 좀 잘 생각을 하지 못 했던 부분이 징검 다리부분이였는데 위와 같이 그냥 단순히 가격이 꺾이는 부분에 (오늘보다 내일이 더 비쌀 때) 팔면 된다. 아닐 경우는 계속 지나가게 되니깐 더 복잡하게 생각할 필요가 없어진다.

## 시간 복잡도와 공간 복잡도

### 시간 복잡도

- 주어진 배열을 한 번 순회하면서 인접한 두 날짜의 가격 차이를 계산하고, 이 차이가 양수인 경우에만 이익으로 간주하여 누적한다. 배열의 길이를 `n`이라고 할 때, 코드는 `n-1`번의 순회를 수행하며 각 순회에서 상수 시간 연산이 이루어진다. 따라서 전체 코드의 시간 복잡도는 O(n) 다.

### 공간 복잡도

- 주어진 코드에서 사용되는 추가적인 공간은 상수 공간을 사용하는 `total` 변수 하나뿐이다. 따라서 공간 복잡도는 O(1) 다.

## 회고 과정

### 지금 코드에서 뭘 개선할 수 있지?

- 코드 개선에 앞서 항상 단순화 시킬 수 있으면 최대한 단순화 시키려는 연습을 해야겠다고 생각했다.
- 그래서 반복문이 아닌 while 문으로 하면 더 단순해지지 않을까 생각해서 찾아봤다. 


### 다른 사람 코드를 보고 그 기준으로 어떤 부분을 개선 할 수 있는지 최종 회고

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        i, j = 0, 0
        profit = 0

        while i < len(prices):
            if prices[i] < prices[j]:
                j = i
            elif prices[i] - prices[j] > 0:
                profit += prices[i] - prices[j]
                j = i
            i += 1
        
        return profit
```

- 다음에는 while을 이용해서 문제를 풀어봐야겠다.