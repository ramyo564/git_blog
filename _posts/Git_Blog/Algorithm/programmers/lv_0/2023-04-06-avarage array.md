---
layout: single
title: "프로그래머스 배열의 평균 값 (알고리즘)"
categories: Algo
tag: [Java,Python,JavaScript,Lv_0,"배열의 평균 값","파이썬함수len()","자스함수reduce"]
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
- 문제 ->https://school.programmers.co.kr/learn/courses/30/lessons/120817

## Java

```java
class Solution {
    public double solution(int[] numbers) {
        double answer = 0;
        for(int i=0; i<numbers.length; i++){
            answer += numbers[i];
        }
        return answer/numbers.length;
    }
}
// 다른사람풀이 

import java.util.Arrays;

class Solution {
    public double solution(int[] numbers) {
        return Arrays.stream(numbers).average().orElse(0);
    }
}
```
### 정리
- 여태 Arrays가 기본 내장 함수인줄 알았다
- Numpy처럼 불러와서 쓰면된다.



## Python
```python
# 내가 푼 답
def solution(numbers):
    return sum([i for i in numbers])/len(numbers)
    
# 다른 사람 풀이 1
import numpy as np
def solution(numbers):
    return np.mean(numbers)

```
### 정리
- len함수 사용법을 까먹었었다.
- 넘파이 진짜 짱이다 ㅋㅋㅋㅋ



## JavaScript

```javascript
// 내가 푼 답
function solution(numbers) {

    return numbers.reduce(function add(a,b){
        return (a+b);
    })/numbers.length;
}
// 다른 사람 풀이
function solution(numbers) {
    var answer = numbers.reduce((a,b) => a+b, 0) / numbers.length;
    return answer;
}
```
### 정리
- 화살표 함수로 사용하면 깔끔하게 표현 가능했다.
- reduce함수를 설명하자면 callback 함수고 배열을 순차적으로 순회하면서 배열의 값을 누적한다.

```javascript
const array1 = [1, 2, 3, 4];

// 0 + 1 + 2 + 3 + 4
const initialValue = 0;
const sumWithInitial = array1.reduce(
  (accumulator, currentValue) => accumulator + currentValue,
  initialValue
);

console.log(sumWithInitial);
// Expected output: 10

```

```javascript
arr.reduce(callback(accumulator, currentValue, index, array), initialValue)
```

**arr**
- 순회하고자 하는 배열
**accumulator**
- 누적되는 값
- callback 함수의 반환값을 누적
- **initialValue**를 설정한 경우 callback의 최초 호출시 initialValue로 값으로 초기화
- **initialValue**가 없을 경우 arr의 0번째 인덱스 값으로 초기화
**currentValue**
- 현재 배열의 요소
**index(생략 가능)**
- 현재 배열 요소의 index
**array(생략 가능)**
- reduce 함수를 호출한 배열
**initialValue(생략 가능)**
- callback의 최초 호출시 **accumulator** 초기값


출처 : 프로그래머스,https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce
,
https://developer-talk.tistory.com/146