---
layout: single
title: "[121. Best Time to Buy and Sell Stock] (알고리즘)"
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

[문제 링크](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/?envType=study-plan-v2&envId=top-interview-150)

You are given an array `prices` where `prices[i]` is the price of a given stock on the `ith` day.

You want to maximize your profit by choosing a **single day** to buy one stock and choosing a **different day in the future** to sell that stock.

Return _the maximum profit you can achieve from this transaction_. If you cannot achieve any profit, return `0`.

**Example 1:**

**Input:** prices = [7,1,5,3,6,4]
**Output:** 5
**Explanation:** Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.
Note that buying on day 2 and selling on day 1 is not allowed because you must buy before you sell.

**Example 2:**

**Input:** prices = [7,6,4,3,1]
**Output:** 0
**Explanation:** In this case, no transactions are done and the max profit = 0.

**Constraints:**

- `1 <= prices.length <= 105`
- `0 <= prices[i] <= 104`
## 스스로 고민

### 접근법

- 가장 싸게 사서 비싸게 판다.
- 오늘보다 내일이 더 싼 경우 내일 사는 방식이다.
- 오늘 가격보다 내일이 비쌀 경우 오늘 구매한다.

## 의사코드

- 우선 제약조건을 보면 가격에 대한 최대 길이는 100,000
- 반복문을 진행하면서 현재 가격이 최대길이보다 낮다면 현재 가격은 사야되는 가격으로 정의해둔다.
- 다음날에 판매 가격이 저렴하다면 사야될 가격을 갱신
- 만약 내일 판매가격이 오늘보다 비싸다면 수익을 실현한다.
- 그렇게 반복문을 진행하면서 수익실현이 가장 높은 값으로 업데이트해서 리턴한다.
  
## 구현 코드

```python
class Solution:

    def maxProfit(self, prices: List[int]) -> int:

        buy = 10000

        earn = 0

        expected_earn = 0

        for i in range(len(prices)-1):

            if prices[i] < buy:

                buy = prices[i]

            if buy > prices[i+1]:

                buy = prices[i+1]

            if buy < prices[i+1]:

                expected_earn = prices[i+1] - buy

            if expected_earn > earn:

                earn = expected_earn

        return earn
```

## 시간 복잡도와 공간 복잡도

### 시간 복잡도

- 주어진 코드는 `prices` 리스트를 한 번 순회하면서 각각의 가격을 확인하고, 이익을 계산하는 과정을 수행한다. 이 때 리스트의 길이를 `n`이라고 하면, 코드의 for 루프는 `n-1`번 실행되므로 시간 복잡도는 O(n) 다. for 루프 내에서 수행되는 각 연산들은 상수 시간이 걸리므로, 전체 코드의 시간 복잡도는 O(n)다.
### 공간 복잡도

- 주어진 코드에서 사용되는 추가적인 공간은 변수 `buy`, `earn`, `expected_earn` 세 개다. 이 변수들은 상수 공간을 사용하므로, 공간 복잡도는 O(1)다.


## 회고 과정

### 지금 코드에서 뭘 개선할 수 있지?

```python 

class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices:
            return 0
        
        min_price = prices[0]
        max_profit = 0
        
        for price in prices:
            if price < min_price:
                min_price = price
            else:
                max_profit = max(max_profit, price - min_price)
        
        return max_profit

```

1. `buy` 변수를 `min_price`로 변경하여 구매 가격을 올바르게 추적한다.
2. `expected_earn` 변수를 제거하고, 직접 이익을 계산한다.
3. 이익을 계산할 때, 구매 가격보다 높은 가격인 경우에만 이익을 계산한다.
4. 리스트가 비어있을 경우에 대한 처리를 추가하여 초기 이익을 0으로 반환한다.

코드 구현도 문제인데 변수명과 반복되는 부분을 줄이는게 정말 중요한것 같다.

### 다른 사람 코드를 보고 그 기준으로 어떤 부분을 개선 할 수 있는지 최종 회고

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        ret_val = 0

        lowest = prices[0]
        for price in prices:
            if price < lowest:
                lowest = price
            if ret_val < price - lowest:
                ret_val = price - lowest
        return ret_val
```

- 시간 복잡도와 공간 복잡도는 다르지 않지만 현재 코드는 주식의 최대 이익을 계산하는 데 필요한 정보만을 추적하고, 각 가격을 단 한 번만 순회하므로 효율적이라고 생각헀다. 
- 이 코드는 최저 가격과 최대 이익을 한 번의 순회로 계산하면서도 간결하고 가독성이 높다고 생각했다. 
- 주식 거래 관련 문제에서 흔히 사용되는 "최소값 찾기"와 "최대 이익 계산" 패턴을 잘 활용한 예라고 생각했다.