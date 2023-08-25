---
layout: single
title: "프로그래머스 짝수 홀수 개수 (알고리즘)"
categories: Algo
tag: [Java,Python,JavaScript,Lv_0,"짝수 홀수 개수"]
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

- 문제 -> https://school.programmers.co.kr/learn/courses/30/lessons/120824

## Java

```java
// 내가 푼 답
class Solution {
    public int[] solution(int[] num_list) {
        int[] answer = {0,0};
        for(int i:num_list){
            if(i%2 == 0){
                answer[0]+=1;
            }else{
                answer[1]+=1;
            }
        }
        return answer;
    }
}
// 다른사람풀이 
class Solution {
    public int[] solution(int[] num_list) {
        int[] answer = new int[2];

        for(int i = 0; i < num_list.length; i++)
            answer[num_list[i] % 2]++;

        return answer;
    }
}
```
### 정리
- 배열 2개 짜리 만들고 각각 0 아니면 1만 나오게 해서 카운트 올려주면 if문 실행할 필요가 없음



## Python
```python
# 내가 푼 답
def solution(num_list):
    answer = [0,0]
    for num in num_list:
        if num % 2 == 0:
            answer[0] += 1;
        else:
            answer[1] += 1;
    return answer
    
# 다른 사람 풀이 1
def solution(num_list):
    answer = [0,0]
    for n in num_list:
        answer[n%2]+=1
    return answer

```
### 정리
- 위와 같은 맥락으로 index 값을 0 아니면 1만 나오게 짜면 됨



## JavaScript

```javascript
// 내가 푼 답
function solution(num_list) {
    var answer = [0,0];
    for(const i in num_list){
        answer[i%2]+=1;
    }
    return answer;
}

// 수정
function solution(num_list) {
    var answer = [0,0];
    for(const i of num_list){
        answer[i%2]+=1;
    }
    return answer;
}
// 다른 사람 풀이
function solution(num_list) {
    var answer = [0,0];

    for(let a of num_list){
        answer[a%2] += 1
    }

    return answer;
}
```
### 정리
- 자바스크립트의 for of와 for of의 차이...
>- for of 는 배열 순환
>- for in 은 객체순환이다.
>- 자바스크립트에서 배열도 객체라 객체에 대한 키값 -> index가 반환된다.


출처 : 프로그래머스,

