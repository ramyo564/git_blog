---
layout: single
title: 프로그래머스 피자 나눠 먹기 (1) (알고리즘)
categories: Algo
tags:
  - Java
  - Python
  - JavaScript
  - Lv_0
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# Lv 0

## 문제 푸는건 5초 정리는 10분...

- 하루 시작은 알고리즘 한 문제 풀기로 시작
- 자바가 특히 헷갈려서 언어에 익숙해질겸 lv_0부터 다시 하는게 나을 듯
- 어차피 기본기가 안되면 뭘 배워도 응용이 안됨

- 문제 -> https://school.programmers.co.kr/learn/courses/30/lessons/120814

## Java

```java
// 내가 푼 답
class Solution {
    public int solution(int n) {
        int answer = (n - 1) / 7;
        return answer+1;
    }
}
// 다른사람풀이 

class Solution {
    public int solution(int n) {
        return (n + 6) / 7;
    }
}
```
### 정리
- 생각의 틀을 깨는게 쉽지 않네 ㅋㅋ ㅜ



## Python
```python
# 내가 푼 답
def solution(n):
    return int(n/7) if n%7 == 0 else int(n/7) +1
# 다른 사람 풀이 1
def solution(n):
    return (n - 1) // 7 + 1

```
### 정리
- 내가 한 건 뭔가 파이썬스럽지 않게 짠거 같다.



## JavaScript

```javascript
// 내가 푼 답
function solution(n) {
    var answer = 0;
    if(n%7 !== 0){
        return parseInt(n/7) + 1;
    }
    return n/7 ;
}
// 다른 사람 풀이

function solution(n) {
    return Math.ceil(n / 7)
}
```
### 정리
- 자바스크립트에는 진짜 별에별 희안한 함수가 많다.
- math.ceil 는 0.000001 이라도 있으면 다 반올림 해준다.
- 반대로 음수일때는 반 내림한다

![](https://i.imgur.com/KKpgQKZ.png)


출처 : 프로그래머스,https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/ceil