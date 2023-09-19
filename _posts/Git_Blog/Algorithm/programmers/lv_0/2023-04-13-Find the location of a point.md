---
layout: single
title: "프로그래머스 점의 위치 구하기 (알고리즘)"
categories: Algo
tag: [Java,Python,JavaScript,Lv_0,"점의 위치 구하기","체크구조분해"]
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

- 문제 -> https://school.programmers.co.kr/learn/courses/30/lessons/120841

## Java

```java
// 내가 푼 답
class Solution {
    public int solution(int[] dot) {

        if(dot[0] > 0 && dot[1] > 0){
            return 1;
        }else if(dot[0] > 0 && dot[1] < 0){
            return 4;
        }else if(dot[0] < 0 && dot[1] < 0){
            return 3;
        }else{
            return 2;
        }

    }
}
// 다른사람풀이 

class Solution {
    public int solution(int[] dot) {
        int answer = 0;
        if(dot[0] > 0) 
            if(dot[1] > 0) answer = 1;
            else answer = 4;
        else 
            if(dot[1] > 0) answer = 2;
            else answer = 3;

        return answer;
    }
}
```
### 정리
- pass



## Python
```python
# 내가 푼 답
def solution(dot):
    if dot[0] > 0 and dot[1] > 0:
        return 1
    elif dot[0] > 0 and dot[1] < 0:
        return 4
    elif dot[0] < 0 and dot[1] < 0:
        return 3
    else:
        return 2
# 다른 사람 풀이 1

def solution(dot):
    quad = [(3,2),(4,1)]
    return quad[dot[0] > 0][dot[1] > 0]

```
### 정리
- 코드가 간편하긴한데 가독성이 좋은지는 잘 모르겠다.
- 논리는 짱
- x축이 양수면 1사분면과 4사분면만 나오도록 잘 짠거 같다.



## JavaScript

```javascript
// 내가 푼 답
function solution(dot) {
	if(dot[0] > 0 && dot[1] > 0){
		return 1;
	}else if(dot[0] > 0 && dot[1] < 0){
		return 4;
	}else if(dot[0] < 0 && dot[1] < 0){
		return 3;
	}else{
		return 2;
	}
}
// 다른 사람 풀이
function solution(dot) {
    const [num,num2] = dot;
    const check = num * num2 > 0;
    return num > 0 ? (check ? 1 : 4) : (check ? 3 : 2);
}
```
### 정리
- 배열이 위와 같은 식으로 나눠서 들어가질 수 도 있다.
- dot [1,2,3] 이렇게 있다면
- const [a,b,c] 이렇게 각각 배정가능
- 체크구조분해



출처 : 프로그래머스,