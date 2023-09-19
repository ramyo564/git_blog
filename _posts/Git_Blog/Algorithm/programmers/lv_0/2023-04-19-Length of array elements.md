---
layout: single
title: "프로그래머스 배열 원소의 길이 (알고리즘)"
categories: Algo
tag: [Java,Python,JavaScript,Lv_0," 배열 원소의 길이 "]
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

- 문제 -> https://school.programmers.co.kr/learn/courses/30/lessons/120854

## Java

```java
// 내가 푼 답
class Solution {
    public int[] solution(String[] strlist) {
        int[] answer = new int[strlist.length];
        for(int i = 0; i< strlist.length; i++){
            answer[i] = strlist[i].length();
        }
        
        return answer;
    }
}
// 다른사람풀이 
import java.util.Arrays;

class Solution {
    public int[] solution(String[] strList) {
        return Arrays.stream(strList).mapToInt(String::length).toArray();
    }
}
```
### 정리
- [참고](https://mine-it-record.tistory.com/126)



## Python
```python
# 내가 푼 답
def solution(strlist):
    return [len(x) for x in strlist]
# 다른 사람 풀이 1
def solution(strlist):
    answer = list(map(len, strlist))
    return answer

```
### 정리
- pass



## JavaScript

```javascript
// 내가 푼 답
function solution(strlist) {
    var answer = [];
    for (i=0; i<strlist.length; i++){
        answer[i] = strlist[i].length
    }
    return answer;
}
// 다른 사람 풀이
function solution(strlist) {
    return strlist.map((el) => el.length)
}
```
### 정리
- 자바스크립트는 진짜 잘 쓰려면 많이 알아야 될거 같다..ㅜ


출처 : 프로그래머스,


