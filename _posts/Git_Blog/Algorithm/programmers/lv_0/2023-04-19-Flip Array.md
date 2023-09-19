---
layout: single
title: "프로그래머스 배열 뒤집기 (알고리즘)"
categories: Algo
tag: [Java,Python,JavaScript,Lv_0," 배열 뒤집기 "]
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

- 문제 -> https://school.programmers.co.kr/learn/courses/30/lessons/120821

## Java

```java
// 내가 푼 답
class Solution {
    public int[] solution(int[] num_list) {
        int[] arr = new int[num_list.length];
        int count = 0;
        for(int i = num_list.length-1; i > -1; i-- ){
            arr[count] = num_list[i];
            count += 1;
        }

        return arr;
    }
}
// 다른사람풀이 
class Solution {
    public int[] solution(int[] num_list) {
        int[] answer = new int[num_list.length];
        for(int i = 0;i<num_list.length;i++){
            answer[i]=num_list[num_list.length-i-1];
        }
        return answer;
    }
}
// 다른사람풀이 2
import java.util.Collections;
import java.util.List;
import java.util.stream.Collectors;
import java.util.Arrays;

class Solution {
    public int[] solution(int[] numList) {
        List<Integer> list = Arrays.stream(numList).boxed().collect(Collectors.toList());

        Collections.reverse(list);
        return list.stream().mapToInt(Integer::intValue).toArray();
    }
}
```
### 정리
- 굳이 어렵게 써야되나 싶긴함...
- 실제로 이런식으로 복잡하게 썼을 때 어떤 이득이 있는지 궁금함.
- 그냥 유틸이나 그런거 위주로 공부 해야할지 아니면 사고력 기르는 방법으로 알고리즘을 공부해야할지 좀 고민됨
- 자바스크립트는 함수 사용법 위주로 공부해야할거 같긴 함..



## Python
```python
# 내가 푼 답
def solution(num_list):
    return num_list[::-1]
# 다른 사람 풀이 1


```
### 정리
- 슬라이싱은 원본은 유지되고 따로 바뀌는거 없음

```python
>> arr = range(10)
>> arr
[0,1,2,3,4,5,6,7,8,9]
>> arr[::2] # 처음부터 끝까지 두 칸 간격으로
[0,2,4,6,8]
>> arr[1::2] # index 1 부터 끝까지 두 칸 간격으로
[1,3,5,7,9]
>> arr[::-1] # 처음부터 끝까지 -1칸 간격으로 ( == 역순으로)
[9,8,7,6,5,4,3,2,1,0]
>> arr[::-2] # 처음부터 끝까지 -2칸 간격으로 ( == 역순, 두 칸 간격으로)
[9,7,5,3,1]
>> arr[3::-1] # index 3 부터 끝까지 -1칸 간격으로 ( == 역순으로)
[3,2,1,0]
>> arr[1:6:2] # index 1 부터 index 6 까지 두 칸 간격으로
[1,3,5]
```

## JavaScript

```javascript
// 내가 푼 답
function solution(num_list) {
    return num_list.reverse();
}
// 다른 사람 풀이

```
### 정리
- pass


출처 : 프로그래머스,