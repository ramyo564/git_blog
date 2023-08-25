---
layout: single
title: "프로그래머스 최댓값 만들기 (1) (알고리즘)"
categories: Algo
tag: [Java,Python,JavaScript,Lv_0,"최댓값 만들기 (1)","자바함수sort"]
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

- 문제 -> https://school.programmers.co.kr/learn/courses/30/lessons/120847

## Java

```java
// 내가 푼 답
class Solution {
    public int solution(int[] numbers) {
        int max = numbers[0];
        int prev = 0;
        for(int i=1; i<numbers.length; i++){
            if(max < numbers[i]){
                prev = max;
                max = numbers[i];
            }else if(prev < numbers[i]){
                prev = numbers[i];
            }
        }

        return prev*max;
    }
}
// 다른사람풀이 
import java.util.Arrays;

class Solution {
    public int solution(int[] numbers) {
        // 배열 오름차순으로 정렬
        Arrays.sort(numbers);
        // 정렬한 배열의 마지막 전 숫자와 마지막 숫자 곱을 구한다.
        return numbers[numbers.length-2]*numbers[numbers.length-1];
    }
}
```


### 정리
- 논리를 제대로 구현 못 했음...ㅜ
- Array 를 활용하자
- sort로 값 크기 비교 가능



## Python
```python
# 내가 푼 답
def solution(numbers):
    numbers.sort()
    return numbers[-1] * numbers[-2]
# 다른 사람 풀이 1
다 비슷비슷함

```
### 정리
- sort() 는 반환 값이 없음
- sorted()로 해야 반환 값이 있음



## JavaScript

```javascript
// 내가 푼 답
function solution(numbers) {
    numbers.sort(function(a, b)  {
    return b - a;
});
    return numbers[0] * numbers[1];
}
// 다른 사람 풀이
  
function solution(numbers) {
    numbers.sort((a,b)=>b-a);
    return numbers[0]*numbers[1];
}
```
### 정리
- 화살표 함수를 아직 잘 쓰지 못 하는 것 같다.


출처 : 프로그래머스,