---
layout: single
title: "프로그래머스 머쓱이보다 키 큰 사람 (알고리즘)"
categories: Algo
tag: [Java,Python,JavaScript,Lv_0,"머쓱이보다 키 큰 사람","자스함수filter"]
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

- 문제 -> https://school.programmers.co.kr/learn/courses/30/lessons/120585

## Java

```java
// 내가 푼 답
class Solution {
    public int solution(int[] array, int height) {
        int answer = 0;
        for(int i = 0; i < array.length; i++){
            if(array[i]>height){
                answer +=1;
            }
        }
        return answer;
    }
}
// 다른사람풀이 
class Solution {
    public int solution(int[] array, int height) {
        int answer = 0;
        for(int i: array){
            answer += (i>height) ? 1 : 0;
        }
        return answer;
    }
}
```
### 정리
- 음.. 삼항연산자 연습해봐야겠다.



## Python
```python
# 내가 푼 답
def solution(array, height):
    return len([i for i in array if height < i])
    
# 다른 사람 풀이 1
def solution(array, height):
    array.append(height)
    array.sort(reverse=True)
    return array.index(height)

# 다른 사람 풀이 2
def solution(array, height):
    return sum(1 for a in array if a > height)

```
### 정리
- 내가 조금 더 간편하게 잘한 듯
- 컴프리핸션에서 그냥 변수를 1로 설정해서 총 합을 구해도 된다.


## JavaScript

```javascript
// 내가 푼 답
function solution(array, height) {
    array.sort();
    for(i=0; i < array.length; i++){
        if(array[i] > height){
            return i
        }
        return i
    }
}
// 다시 수정
function solution(array, height) {
    array.sort(); 
    for(let i=0; i <array.length; i++){
        if(array[i] > height){
            return array.length - i;
        }
    }
    return 0;
}


// 다른 사람 풀이

function solution(array, height) {
    var answer = array.filter(item => item > height);
    return answer.length;
}
```
### 정리
#### 문제점
1. 처음 푼 문제에서 i가 선언이 안돼서 그런 줄 알았는데 상관없다.
2. 반복문이 끝까지 실행되지 않고 바로 return 으로 빠져 나왔다.
	- return 위치가 잘못되어 있었다. 저 위치에서 return 해버리면 반복문이 빠져나가는데 { }를 잘못 봤다.
3. 문제 자체를 잘못 읽었다
	- 몇 번째로 반에서 키가 큰 건지인 줄 알았는데 계속 틀려서 도저히 이해가 안 갔다.
	- 문제를 다시 보니 반에서 자기보다 키 큰 애들이 몇 명인가를 세는 거였다 하하 
4. 자바스크립트가 자바보다 더 어려운 거 같다
5. filter 쓰면 간편하게 해결 가능하다.

https://www.opentutorials.org/module/570/4962

출처 : 프로그래머스,