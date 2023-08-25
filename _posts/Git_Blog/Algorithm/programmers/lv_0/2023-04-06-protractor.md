---
layout: single
title: "프로그래머스 각도기 (알고리즘)"
categories: Algo
tag: [Java,Python,JavaScript,Lv_0,"각도기","자스함수filter"]
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

- 문제 -> https://school.programmers.co.kr/learn/courses/30/lessons/120829

## Java

```java
class Solution {
    public int solution(int angle) {
        if (angle < 90){
            return 1;
        } else if(angle == 90){
            return 2;
        } else if(angle > 90 && 180 > angle){
            return 3;
        } 
        return 4;
    }
}
// 다른사람풀이 
class Solution {
    public int solution(int angle) {
        return angle == 180 ? 4 : angle < 90 ? 1 : angle == 90 ? 2 : angle > 90 ? 3 : 0;
    }
}
```
### 정리
- 다음에 삼항연산자 사용해보기



## Python
```python
# 내가 푼 답
def solution(angle):
    if angle < 90:
        return 1
    elif angle == 90:
        return 2 
    elif 90 < angle < 180:
        return 3
    else:
        return 4
# 다른 사람 풀이 1
def solution(angle):
    answer = (angle // 90) * 2 + (angle % 90 > 0) * 1
    return answer

```
### 정리
- angle 부분이 t or f 로 만들어서 0 혹은 1로 만들어줌



## JavaScript

```javascript
// 내가 푼 답
function solution(angle) {
        if (angle < 90){
            return 1;
        } else if(angle == 90){
            return 2;
        } else if(angle > 90 && 180 > angle){
            return 3;
        } 
        return 4;
}
// 다른 사람 풀이
function solution(angle) {
    return [0, 90, 91, 180].filter(x => angle>=x).length;
}
```
### 정리
- 기본적인 If 문은 자바나 자바스크립트나 동일하나 표현 방법에 대한 다양성이 자바스크립트에 더 많은 것 같다.


출처 : 프로그래머스,