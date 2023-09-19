---
layout: single
title: "[88. Merge Sorted Array]  (알고리즘)"
categories: Algo
tags:
  - leetcode
  - Python
  - Array
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
`Array`
##  문제


[문제 링크](https://leetcode.com/problems/merge-sorted-array/?envType=study-plan-v2&envId=top-interview-150)

You are given two integer arrays `nums1` and `nums2`, sorted in **non-decreasing order**, and two integers `m` and `n`, representing the number of elements in `nums1` and `nums2` respectively.

**Merge** `nums1` and `nums2` into a single array sorted in **non-decreasing order**.

The final sorted array should not be returned by the function, but instead be _stored inside the array_ `nums1`. To accommodate this, `nums1` has a length of `m + n`, where the first `m` elements denote the elements that should be merged, and the last `n` elements are set to `0` and should be ignored. `nums2` has a length of `n`.

**Example 1:**

**Input:** nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3
**Output:** [1,2,2,3,5,6]
**Explanation:** The arrays we are merging are [1,2,3] and [2,5,6].
The result of the merge is [1,2,2,3,5,6] with the underlined elements coming from nums1.

**Example 2:**

**Input:** nums1 = [1], m = 1, nums2 = [], n = 0
**Output:** [1]
**Explanation:** The arrays we are merging are [1] and [].
The result of the merge is [1].

**Example 3:**

**Input:** nums1 = [0], m = 0, nums2 = [1], n = 1
**Output:** [1]
**Explanation:** The arrays we are merging are [] and [1].
The result of the merge is [1].
Note that because m = 0, there are no elements in nums1. The 0 is only there to ensure the merge result can fit in nums1.

**Constraints:**

- `nums1.length == m + n`
- `nums2.length == n`
- `0 <= m, n <= 200`
- `1 <= m + n <= 200`
- `-109 <= nums1[i], nums2[j] <= 109`

**Follow up:** Can you come up with an algorithm that runs in `O(m + n)` time?

## 스스로 고민 하기

### 접근법

- nums1과 nums2 배열을 하나의 오름차순으로 정렬된 배열로 합치기
- 정렬된 결과는 함수에서 반환하지 않고, 그 대신 nums1 배열 내부에 저장되어야 함
- nums1 배열은 길이가 m + n이며, 처음 m개의 요소는 합쳐져야 할 요소들을 나타내고, 나머지 마지막 n개의 요소는 0으로 설정되어 0은 무시된다고 보면 됨
- nums2 배열의 길이는 n임

요약하자면, nums1과 nums2 배열을 합쳐서 오름차순으로 정렬된 상태로 nums1 배열에 저장해야 하며, nums2 배열은 무시되어야 한다.

## 의사코드 1번째 시도

- num1 과 num2 를 합쳐준다
- 내림차순으로 정리해준다.
- 0을 제외해준다.

## 구현 코드 1번째 시도

```python
class Solution:

    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:

        """

        Do not return anything, modify nums1 in-place instead.

        """

        merged_num = nums1 + nums2

        merged_num.sort()

        remove_element = {0}

        nums1 = [i for i in merged_num if i not in remove_element]
```

- 맞는거 같은데 틀렸다 테스트 코드에서도 답이 이상하게 나와서 문제를 다시 보니 num1의 변수명을 만들어 값을 넣는 게 아닌 초기의 num1의 배열을 직접 조작해야 된다.

## 의사코드 2번째 시도

- nums1 의  갯수 m의 뒷 부분 0이 있다면 제거해준다.
- nums1 에 nums2의 원소를 넣어준다.
- nums1 을 정렬해준다.

## 구현코드 2번째 시도

```python
class Solution:

    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:

        """

        Do not return anything, modify nums1 in-place instead.

        """

        if 0 in nums1:

            for _ in range(len(nums1)):

                try:

                    nums1.remove(0)

                except:

                    pass

        nums1.extend(nums2)

        nums1.sort()
```

- 뭔가 이상해서 다시 문제를 살펴보니 마지막 0만 제외되는거였고 중간에 있는 0은 제외되지 않는다. 

```python
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        """
        Do not return anything, modify nums1 in-place instead.
        """
        del nums1[m:]
        nums1.extend(nums2)
        nums1.sort()
```

- 슬라이싱을 통해 nums1 배열에서 뒷 부분에 반복되는 0을 제거해준다.
- 그 후 nums1 배열 뒷 쪽에  nums2 를 이식해준다.
- 이식되어 합쳐진 nums1의 배열을 다시 정렬해준다.
## 시간 복잡도와 공간 복잡도

- 시간 복잡도와 공간 복잡도에 대한 개념이 확실하게 박혀있지 않아서 다시 간단하게 정리...

**시간 복잡도(Time Complexity)**:

- 시간 복잡도는 알고리즘이 입력 데이터의 크기에 대해 얼마나 많은 시간을 소비하는지를 나타내는 지표다.
- 주로 연산 횟수를 기반으로 한다. 어떤 연산을 몇 번 수행하는지를 분석하여 알고리즘의 성능을 평가한다.
- 시간 복잡도는 보통 빅오(Big-O) 표기법으로 나타내며, 입력의 크기에 대한 상한을 나타낸다. 예를 들어, O(n^2), O(n log n) 등이 있습니다.
- 시간 복잡도가 작을수록 알고리즘의 성능이 더 좋다고 판단할 수 있다.

**공간 복잡도(Space Complexity)**:

- 공간 복잡도는 알고리즘이 실행되는 동안 얼마나 많은 메모리 공간을 사용하는지를 나타내는 지표다.
- 주로 추가적으로 필요한 메모리의 양을 나타낸다. 주로 배열, 변수, 임시 데이터 구조 등의 메모리 사용량을 포함한다.
- 공간 복잡도도 빅오(Big-O) 표기법으로 나타내며, 필요한 추가 공간의 양을 표현한다.
- 공간 복잡도가 작을수록 알고리즘의 메모리 효율성이 더 좋다고 판단할 수 있다.

공간 복잡도를 구하는 방법은 주로 다음과 같다:

- 알고리즘이 실행되는 동안 필요한 모든 변수, 배열, 데이터 구조 등의 크기를 합산한다.
- 추가적으로 필요한 공간이 있는지 확인한다. 임시 변수, 재귀 호출 등으로 인해 필요한 메모리를 고려해야 한다.
- 필요한 메모리의 크기를 추정하거나 계산하여 공간 복잡도를 결정한다.

요약하면, 시간 복잡도는 알고리즘이 실행되는 데 걸리는 시간을 나타내고, 공간 복잡도는 알고리즘이 실행되는 동안 사용하는 메모리 공간을 나타낸다. 이 두 지표는 알고리즘의 성능을 평가하고 비교하는 데 중요한 역할을 한다.


### 시간 복잡도

- `del nums1[m:]` 은 리스트 nums1 의 뒷부분을 삭제하는데 O(n) 시간이 걸린다. 여기서 n 은 nums1 의 배열길이다.
-  `nums1.extend(nums2)`: 이 부분은 리스트 `nums1`의 뒤에 리스트 `nums2`를 추가하는데 O(n) 시간이 걸린다. 여기서 `n`은 `nums2` 배열의 길이다.
- `nums1.sort()`: 리스트 `nums1`을 정렬하는데 O((m+n)log(m+n)) 시간이 걸린다. 여기서 `m`은 `nums1` 배열의 길이이고, `n`은 `nums2` 배열의 길이이다.

따라서 전체 시간 복잡도는 O((m+n)log(m+n)) 다.

### 공간복잡도

- `del nums1[m:]`: 이 부분은 리스트 `nums1`의 뒷부분을 삭제하는데 추가 공간을 사용하지 않는다.
- `nums1.extend(nums2)`: 리스트 `nums1`의 뒤에 리스트 `nums2`를 추가하므로, 추가적인 공간 복잡도는 O(n)다. 여기서 `n`은 `nums2` 배열의 길이다.
- `nums1.sort()`: 정렬 알고리즘에 따라 추가 공간을 사용할 수 있다. 대부분의 정렬 알고리즘은 O(log(m+n))부터 O(m+n)까지의 공간을 사용하므로, 정렬에 따른 추가 공간 복잡도는 O(m+n) 정도로 볼 수 있다.

따라서 전체 공간 복잡도는 O(m+n) 다.


## 회고

### 지금 코드에서 뭘 개선할 수 있지?

- 시간 복잡도는 정렬 + 뒷 부분 0을 삭제한다면 해당 시간복잡도보다 더 최적화는 어려울 것 같다.
- 공간 복잡도를 봐도 어찌 되었든 nums1 의 뒷 부분에 nums2 를 추가하고 정렬을 해야되서 여기서 더 최적화는 생각이 안난다.

### 다른 사람 코드를 보고 그 기준으로 어떤 부분을 개선 할 수 있는지 최종 회고

내가 풀어본 방식과 전혀 다른 방식 + 내가 풀어본 방식과 비슷하지만 더 좋은 방식을 각각 찾았다.

```python
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        """
        Do not return anything, modify nums1 in-place instead.
        """
        for j in range(n):
            nums1[m+j]=nums2[j]
        nums1.sort()

```

- 반복문을 통해 문제를 해결한 케이스다.
- 뒷 부분에 0으로 채워지는 부분을 따로 제거하지 않고 해당 부분을 nums2의 배열을 순서대로 채운후 다시 정렬해서 문제를 해결한 방식이다.
- 시간 복잡도와 공간 복잡도의 차이는 없다.

```python
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        """
        Do not return anything, modify nums1 in-place instead.
        """
        del nums1[m:]
        
        nums1 += nums2[0:n]
        
        nums1.sort()

```

- 처음부터 끝까지 슬라이싱을 활용해서 문제를 해결한 방식이다.
- 코드가 훨씬 더 간결하고 가독성이 좋다고 생각했다.
- 가독성에 대해 좀 더 신경을 쓰는 습관을 가지는게 좋을 것 같다.