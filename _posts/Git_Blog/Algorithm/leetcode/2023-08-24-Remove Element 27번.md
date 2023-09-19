---
layout: single
title: "[27. Remove Element] (알고리즘)"
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


[문제 링크](https://leetcode.com/problems/remove-element/?envType=study-plan-v2&envId=top-interview-150)

Given an integer array `nums` and an integer `val`, remove all occurrences of `val` in `nums` [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm). The order of the elements may be changed. Then return _the number of elements in_ `nums` _which are not equal to_ `val`.

Consider the number of elements in `nums` which are not equal to `val` be `k`, to get accepted, you need to do the following things:

- Change the array `nums` such that the first `k` elements of `nums` contain the elements which are not equal to `val`. The remaining elements of `nums` are not important as well as the size of `nums`.
- Return `k`.

**Custom Judge:**

The judge will test your solution with the following code:

int[] nums = [...]; // Input array
int val = ...; // Value to remove
int[] expectedNums = [...]; // The expected answer with correct length.
                            // It is sorted with no values equaling val.

int k = removeElement(nums, val); // Calls your implementation

assert k == expectedNums.length;
sort(nums, 0, k); // Sort the first k elements of nums
for (int i = 0; i < actualLength; i++) {
    assert nums[i] == expectedNums[i];
}

If all assertions pass, then your solution will be **accepted**.

**Example 1:**

**Input:** nums = [3,2,2,3], val = 3
**Output:** 2, nums = [2,2,_,_]
**Explanation:** Your function should return k = 2, with the first two elements of nums being 2.
It does not matter what you leave beyond the returned k (hence they are underscores).

**Example 2:**

**Input:** nums = [0,1,2,2,3,0,4,2], val = 2
**Output:** 5, nums = [0,1,4,0,3,_,_,_]
**Explanation:** Your function should return k = 5, with the first five elements of nums containing 0, 0, 1, 3, and 4.
Note that the five elements can be returned in any order.
It does not matter what you leave beyond the returned k (hence they are underscores).

**Constraints:**

- `0 <= nums.length <= 100`
- `0 <= nums[i] <= 50`
- `0 <= val <= 100`


## 스스로 고민

### 접근법

- 정수 배열 `nums`와 정수 `val`이 주어지며, `nums`에서 모든 `val` 의 값이 포함되면 안된다.

## 의사코드

- 반복문을 통해 val 포함 되는지 검사한 후 val이 포함되지 않는 배열을 다시 만든다.
- val 의 값이 제외된 배열의 갯수를 K 로 리턴

## 구현 코드

```python
class Solution:

    def removeElement(self, nums: List[int], val: int) -> int:

        return len([x for x in nums if x != val])
```

- 틀렸다. 계속 틀려서 문제를 자세히 보니까 평가 기준에  assert 문에 nums를 불러서 오류 검사를 한다... nums 변수를 만들어서 리턴해도 마찬가지라서 단순히 값만 찾는 게 아닌 직접 nums를 조작해야 한다. 

```python
class Solution:

    def removeElement(self, nums: List[int], val: int) -> int:

        k = 0

        for x in range(len(nums)):

            if nums[x] != val:

                nums[k] = nums[x]

                k += 1

        return k
```

-  nums 의 길이만큼 반복문이 돌아가고 배열의 첫 부분부터 val 의 값이 nums 배열에 포함되어 있는지 하나씩 검사한다.
- 만약 nums 배열에 val 의 값이 존재하지 않는다면 K를 인덱스로 사용해서 현재 해당 값을 nums 에 새로 넣어준다.
- 만약 nums 배열에 val 값이 포함 한다면 False가 뜨므로 k 값은 올라가지 않고 넘어간다.
- 그리고 k 값뒤에 뭐가 있던 상관이 없다.

```
**  
Example 1:**

**Input:** nums = [3,2,2,3], val = 3
**Output:** 2, nums = [2,2,_,_]
**Explanation:** Your function should return k = 2, with the first two elements of nums being 2.
It does not matter what you leave beyond the returned k (hence they are underscores).
```

## 시간 복잡도와 공간 복잡도

### 시간복잡도(Time Complexity):

- 주어진 배열의 길이를 `n`이라고 할 때, `for` 루프는 배열의 모든 원소를 한 번씩 순회하므로 `O(n)`의 시간이 걸린다. 각 원소를 순회하면서 `k` 개수만큼의 요소를 덮어쓰기 작업을 하므로 모든 작업은 `O(n)` 시간 안에 수행된다.

### 공간 복잡도:

- 주어진 배열 내에서 작업이 수행되며, 추가적인 배열이나 데이터 구조를 사용하지 않는다. 따라서 공간 복잡도는 상수인 `O(1)` 다.

## 회고

- 무조건 지금 보다 더 좋은 방법이 있다고 생각한다.

### 지금 코드에서 뭘 개선할 수 있지?

- 솔직히 지금도 간단한 코드인데 여기서 더 간단하게 하려면 어떻게 하면 좋을까
- 정말 곰곰히 생각해봐도 잘 떠오르지 않아서 다른 사람들은 어떻게 했는지 구경했다.

```python
class Solution:
    def removeElement(self, nums: List[int], val: int) -> int:
        while val in nums:
            nums.remove(val)
```

- 역시 방법은 무조건 있다. 최대한 스스로 생각해보고 방법이 생각나지 않는 다면 다른 사람들은 어떻게 했는지 학습하는 것도 좋은 방법이라고 생각한다.
- 반복문을 통해 val 이 nums 에 존재하지 않을 때까지 삭제하면서 계속 반복시킨다.
	- nums 안에 val 값이 더 이상 존재하지 않는다면 while 문은 자동으로 종료
- 코드의 가독성 + 무조건 방법이 있다는 믿음 이 두 가지를 항상 마음에 새겨야 될거 같다.

