---
layout: single
title: "프로그래머스 피자 나눠 먹기 (3) (알고리즘)"
categories: Algo
tag: [Java,Python,JavaScript,Lv_0,"피자 나눠 먹기"]
toc: true
toc_sticky: true
author_profile: false
sidebar:

---
# Java vs JavaScript vs Python
## 문제 푸는건 5초 정리는 10분...

- 하루 시작은 알고리즘 한 문제 풀기로 시작
- 자바가 특히 헷갈려서 언어에 익숙해질겸 lv_0부터 다시 하는게 나을 듯
- 어차피 기본기가 안되면 뭘 배워도 응용이 안됨

- 문제 -> https://school.programmers.co.kr/learn/courses/30/lessons/120816

## Java

```java
// 내가 푼 답
class Solution {
    public int solution(int slice, int n) {

        return (n + slice-1) / slice;
    }
}
// 다른사람풀이 
class Solution {
    public int solution(int slice, int n) {
        return n % slice > 0 ? n/slice+1 : n/slice;
    }
}
```
### 정리
- 삼항연산자 뭔가 딱 먼저 떠오르지가 않는데 연습해야겠다.



## Python
```python
# 내가 푼 답

# 다른 사람 풀이 1


```
### 정리
- pass



## JavaScript

```javascript
// 내가 푼 답

// 다른 사람 풀이

```
### 정리
- pass


출처 : 프로그래머스,