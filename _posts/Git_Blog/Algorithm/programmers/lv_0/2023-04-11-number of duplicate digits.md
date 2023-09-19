---
layout: single
title: 프로그래머스 중복된 숫자 개수 (알고리즘)
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

- 문제 -> https://school.programmers.co.kr/learn/courses/30/lessons/120583

## Java

```java
// 내가 푼 답
class Solution {
    public int solution(int[] array, int n) {
        int answer = 0;
        for(int i=0; i<array.length; i++){
            answer +=(array[i] == n) ? 1: 0;
        }
        return answer;
    }
}
// 다른사람풀이 

class Solution {
    public int solution(int[] array, int n) {
        int answer = 0;
        for (int num : array) {
            if (num == n) answer++;
        }
        return answer;
    }
}
```
### 정리
- for each도 연습해보자


## Python
```python
# 내가 푼 답
def solution(array, n):
    return len([i for i in array if n == i])
    
# 다른 사람 풀이 1
def solution(array, n):
    return array.count(n)

```
### 정리
- count로 바로 할 수 있다



## JavaScript

```javascript
// 내가 푼 답
function solution(array, n) {
    var answer = 0;
    for(i=0;i<array.length;i++){
        if(array[i] === n){
            answer +=1;
        }
    }
    return answer;
}
// 다른 사람 풀이
function solution(array, n) {
    var answer = 0;
    let Array = array.filter((item) => item === n)
    answer = Array.length

    return answer;
}
```
### 정리
- 자바스크립트에서 filter를 잘 사용 못하는 것 같다.
  filter 좀 연습해야겠다.


출처 : 프로그래머스,