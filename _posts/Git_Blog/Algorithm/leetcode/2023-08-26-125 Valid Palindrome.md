---
layout: single
title: "[125. Valid Palindrome] (알고리즘)"
categories: Algo
tags:
  - leetcode
  - Python
  - TwoPointers
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
`Two Pointers`
## 문제

[문제 링크](https://leetcode.com/problems/valid-palindrome/description/?envType=study-plan-v2&envId=top-interview-150)
A phrase is a **palindrome** if, after converting all uppercase letters into lowercase letters and removing all non-alphanumeric characters, it reads the same forward and backward. Alphanumeric characters include letters and numbers.

Given a string `s`, return `true` _if it is a **palindrome**, or_ `false` _otherwise_.

**Example 1:**

**Input:** s = "A man, a plan, a canal: Panama"
**Output:** true
**Explanation:** "amanaplanacanalpanama" is a palindrome.

**Example 2:**

**Input:** s = "race a car"
**Output:** false
**Explanation:** "raceacar" is not a palindrome.

**Example 3:**

**Input:** s = " "
**Output:** true
**Explanation:** s is an empty string "" after removing non-alphanumeric characters.
Since an empty string reads the same forward and backward, it is a palindrome.

**Constraints:**

- `1 <= s.length <= 2 * 105`
- `s` consists only of printable ASCII characters.

## 스스로 고민

### 접근법

- 스윙스는 거꾸로 해도 스윙스
- 문자열이 앞으로 하나 뒤로 하나 똑같으면 True.
- 숫자와 영문을 제외한 나머지 문자들은 무시되어야한다.

### 아는 패턴

- 투 포인터

## 의사코드

- 투 포인터
	- 정규식으로 특수문자 같은 것들을 제외해준다
		- 영문과 숫자만 포함
	- 공백을 제거해준다.
	- 모두 소문자로 변환해준다.
	- 배열을 반으로 나눠준 후 앞과 뒤를 확인해주면서 검사한다.
	- 반복문으로 두 개의 값이 같은지 확인한다.
		- 공백을 제거 했을 경우 0이면 true를 반환한다.

## 구현 코드

```python
import re

class Solution:

    def isPalindrome(self, s: str) -> bool:

        s = re.sub(r"[^a-zA-Z0-9]", "", s)

        s = s.lower()

        print(s)

        if len(s) == 0:

            return True

  

        else:

            x = int(len(s)/2)

            for i in range(x):

                if s[i] == s[-i-1]:

                    pass

                else:

                    return False

            return True
```

## 시간 복잡도와 공간 복잡도

- 시간 복잡도: 문자열 길이를 n이라고 할 때, 코드는 문자열을 한 번씩 탐색하면서 정규식을 적용하고 검사한다. 이때 정규식 적용은 O(n) 시간이 걸리며, 팰린드롬 검사는 최대 n/2번의 비교가 수행된디. 따라서 전체 시간 복잡도는 O(n) 다.
    
- 공간 복잡도: 주어진 문자열을 정규식을 적용하여 변환하는 과정에서 새로운 문자열이 생성된다. 이 새로운 문자열의 크기는 주어진 문자열의 길이에 비례하므로 O(n)의 공간이 사용된다. 추가적으로 사용되는 변수들의 공간 복잡도도 O(1)로 상수 크기다. 따라서 전체 공간 복잡도는 O(n)다.
## 회고 과정

### 지금 코드에서 뭘 개선할 수 있지?

- 반복문은 필수로 사용해야되는거 같아서 시간 복잡도를 줄이기는 어려울거 같았다.
- 공간 복잡도 또한 줄이기 어려울거 같았다.

### 다른 사람 코드를 보고 그 기준으로 어떤 부분을 개선 할 수 있는지 최종 회고

```python
class Solution:

    def isPalindrome(self, s: str) -> bool:

        s=s.lower()

        print(s)

        s = re.sub(r'[^a-zA-Z0-9]', '', s)

        print(s)  

        if(len(s)==0):

            return True

        else:

            reverse=s[::-1]

            print(reverse)

            if(s==reverse):

                return True

            else:

                return False
```

그냥 뒤집은 다음에 검사를 하면 반복문을 사용할 필요가 없다.
실제로 반복문을 통해 절반씩만 검사하는 것 보다는 이렇게 하는게 성능이 더 좋았다.

```python
  

import re

class Solution:

    def isPalindrome(self, s: str) -> bool:

        s = re.sub(r"[^a-zA-Z0-9]", "", s)

        s = s.lower()

        back = s[::-1]

  

        if len(s) == 0:

            return True

  

        else:

            x = int(len(s)/2)

            if s[:x] == back[:x]:

                pass

            else:

                return False

            return True
```

- 근데 이렇게 만들 경우 len 과 int 함수를 사용함므로 더 좋은지는 모르겠다.
- 만약에 s 에 대한 값이 매우 크다면 지금 방법이 더 좋을 수도 있을 것 같다.