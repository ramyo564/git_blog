---
layout: single
title: "[80. Remove Duplicates from Sorted Array II] (알고리즘)"
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
## 문제

[문제 링크](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/?envType=study-plan-v2&envId=top-interview-150)

Given an integer array `nums` sorted in **non-decreasing order**, remove some duplicates [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) such that each unique element appears **at most twice**. The **relative order** of the elements should be kept the **same**.

Since it is impossible to change the length of the array in some languages, you must instead have the result be placed in the **first part** of the array `nums`. More formally, if there are `k` elements after removing the duplicates, then the first `k` elements of `nums` should hold the final result. It does not matter what you leave beyond the first `k` elements.

Return `k` _after placing the final result in the first_ `k` _slots of_ `nums`.

Do **not** allocate extra space for another array. You must do this by **modifying the input array [in-place](https://en.wikipedia.org/wiki/In-place_algorithm)** with O(1) extra memory.

**Custom Judge:**

The judge will test your solution with the following code:

int[] nums = [...]; // Input array
int[] expectedNums = [...]; // The expected answer with correct length

int k = removeDuplicates(nums); // Calls your implementation

assert k == expectedNums.length;
for (int i = 0; i < k; i++) {
    assert nums[i] == expectedNums[i];
}

If all assertions pass, then your solution will be **accepted**.

**Example 1:**

**Input:** nums = [1,1,1,2,2,3]
**Output:** 5, nums = [1,1,2,2,3,_]
**Explanation:** Your function should return k = 5, with the first five elements of nums being 1, 1, 2, 2 and 3 respectively.
It does not matter what you leave beyond the returned k (hence they are underscores).

**Example 2:**

**Input:** nums = [0,0,1,1,1,1,2,3,3]
**Output:** 7, nums = [0,0,1,1,2,3,3,_,_]
**Explanation:** Your function should return k = 7, with the first seven elements of nums being 0, 0, 1, 1, 2, 3 and 3 respectively.
It does not matter what you leave beyond the returned k (hence they are underscores).

**Constraints:**

- `1 <= nums.length <= 3 * 104`
- `-104 <= nums[i] <= 104`
- `nums` is sorted in **non-decreasing** order.
## 스스로 고민

### 접근법

- 배열에 각 원소마다 얼마나 반복되는지 파악
- 최소 반복 횟수보다 많은 원소는 그만큼 삭제 최소는 2번
- 딕셔너리에서 숫자 키값과 벨류 값을 확인하고 최소 원소를 뺀 만큼 pop 

## 의사코드

- 각 원소들을 카운트할 딕셔너리를 만들어줌
- 반복횟수 중 가장 낮은 횟수를 변수를 선언
- 반복횟수가 가장 작은 원소에 대한 변수 
- nums 배열을 반복문을 통해 원소가 반복되는지 확인
- 만약에 한 개만 존재한다면 else 문으로 가서 1개로 카운트
- 여러개가 존재한다면 반복되는 게 없어질 때까지 카운트가 계속 올라감
- 반복문이 끝나면 딕셔너리에 저장된 키 값과 벨류 값을 꺼냄
- 반복 횟수가 2보다 크면 2로 통일 시킴
- nums 배열의 각 원소마다 카운트 한 후 반복횟수보다 많다면 삭제해준다.
## 구현 코드

```python
class Solution:

    def removeDuplicates(self, nums: List[int]) -> int:

        num_count = {}

        minimum_number_cycles = 0

        minimum_elements = 0

        for num in nums:

            if num in num_count:

                num_count[num] += 1

            else:

                num_count[num] = 1

        for num, count in num_count.items():

            if minimum_number_cycles == 0:

                minimum_number_cycles = count

                minimum_elements = num

            else:

                if count > 1:

                    minimum_number_cycles = count

                    minimum_elements = num

        if minimum_number_cycles > 2:

            minimum_number_cycles = 2

        for num, count in num_count.items():

            while nums.count(num) > minimum_number_cycles:

                nums.remove(num)

        k = len(nums)

        return k
```

## 시간 복잡도와 공간 복잡도

### 시간 복잡도: 최악의 경우 `O(n^2)`

- 첫 번째 `for` 루프는 `nums` 배열을 순회하며 각 원소를 `num_count` 딕셔너리에 추가하거나 갱신하는데, 이 작업은 `O(n)` 시간이 소요된다.
- 두 번째 `for` 루프도 최대 `O(n)` 시간이 걸립니다. `num_count` 딕셔너리의 각 항목을 순회하며 중복 횟수를 확인하고 최소 중복 횟수를 찾는데 시간이 소요된다.
- 세 번째 `for` 루프는 `num_count` 딕셔너리의 각 항목을 순회하며 중복 횟수를 확인하고, `nums.count(num)`를 호출하여 중복 횟수를 확인하는 작업을 한다. 이 부분이 비효율적이며 최대 `O(n^2)`의 시간 복잡도를 만들 수 있다.

#### 성능문제: 

- 주어진 코드는 `nums` 배열을 중복 제거하는 과정에서 중복 원소의 개수를 세고, 해당 원소를 최대 2개까지만 남기도록 조작하는 방식으로 동작한다.
- 중복을 확인하고 제거하기 위해 `num_count` 딕셔너리를 사용하고, 중복을 2번까지만 남기도록 제어하기 위해 반복적으로 `nums.remove()`를 사용한다.
- `nums.remove()` 메서드는 원소를 제거한 후 배열의 뒤쪽 원소들을 한 칸씩 앞으로 이동시켜야 하므로 배열 크기에 비례하는 시간이 필요하다.
- 이러한 과정이 중첩되면서 중복 제거 작업의 효율성이 떨어지고 최악의 경우 시간 복잡도가 높아진다.

#### 최대 `O(n^2)` 시간 복잡도가 나오는 이유

- 코드의 중첩된 루프와 `nums.remove()` 메서드의 사용으로 인해 최악의 경우 시간 복잡도가 `O(n^2)`이 될 수 있다.
- 두 번째 `for` 루프는 `num_count` 딕셔너리의 모든 항목을 순회하면서 중복 횟수를 확인하는데, 이 작업은 최대 `O(n)` 시간이 걸린다.
- 세 번째 `for` 루프에서 `nums.remove()` 메서드가 호출될 때마다 원소를 제거하고 나머지 원소들을 이동시키는 작업이 필요하다. 이 작업도 최악의 경우 `O(n)` 시간이 걸린다.
- 따라서 두 번째와 세 번째 루프가 중첩되므로 최대 `O(n^2)`의 시간 복잡도를 가질 수 있다.

### 공간 복잡도: O(n)

- `num_count` 딕셔너리를 사용하여 각 원소의 중복 횟수를 저장하므로 추가적인 공간이 필요하다. 따라서 공간 복잡도는 입력 배열의 크기인 `O(n)` 이다.

## 회고 과정

-  중복 제거 작업의 효율성을 높이는 방법중에 더 좋은 방법이 당장 생각나지 않아서 다른 사람은 어떻게 했는지 궁금해서 찾아봤다.

### 다른 사람 코드를 보고 그 기준으로 어떤 부분을 개선 할 수 있는지 최종 회고

```python
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        n=len(nums)
        l=1
        f=1
        for i in range(1,n):
            if nums[i]>nums[l-1]:
                nums[l]=nums[i]
                l+=1
                f=1
            elif nums[i]==nums[l-1] and f<2:
                nums[l]=nums[i]
                l+=1
                f+=1
        return l
```

1. **단일 루프:** 주어진 코드는 하나의 `for` 루프만 사용한다. 이 루프는 `nums` 배열을 한 번 순회하면서 중복을 제거하고 최대 2개의 중복을 허용하는 방식으로 배열을 수정한다. 따라서 단일 루프만으로 중복 처리 작업을 처리하며 다른 루프나 반복문이 중첩되지 않는다.
    
2. **효율적인 중복 제거:** 코드는 현재 원소와 이전 원소를 비교하여 중복을 판단한다. 만약 현재 원소가 이전 원소보다 크다면 중복이 아니므로 `nums[l]`에 해당 원소를 저장하고 `l`을 증가시킨다. 이 때, 중복 횟수 `f`도 1로 초기화된다. 만약 현재 원소가 이전 원소와 같다면 중복이지만 중복 횟수가 2 이하일 때만 중복으로 처리하며, 이때도 `nums[l]`에 해당 원소를 저장하고 `l`을 증가시키고 `f`를 증가시킨다.
    
3. **불필요한 작업 회피:** 중복이 2번 이상이라면 해당 원소를 무시하고 그 다음 원소를 검사하게 된다. 이는 최대 2번의 중복만 허용하므로 중복 원소를 2번까지만 추가로 저장하고 다음 원소로 넘어가게 된다. 이로써 불필요한 중복 처리 작업이 회피된다.
    

위와 같은 이유로 내가 풀어본 방법 보다 더 효율적으로 중복 처리 작업이 가능하다.
