---
layout: single
title: "[26. Remove Duplicates from Sorted Array] (알고리즘)"
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


[문제 링크](https://leetcode.com/problems/remove-duplicates-from-sorted-array/?envType=study-plan-v2&envId=top-interview-150)

Given an integer array `nums` sorted in **non-decreasing order**, remove the duplicates [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) such that each unique element appears only **once**. The **relative order** of the elements should be kept the **same**. Then return _the number of unique elements in_ `nums`.

Consider the number of unique elements of `nums` to be `k`, to get accepted, you need to do the following things:

- Change the array `nums` such that the first `k` elements of `nums` contain the unique elements in the order they were present in `nums` initially. The remaining elements of `nums` are not important as well as the size of `nums`.
- Return `k`.

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

## 스스로 고민

주어진 정수 배열 `nums`가 오름차순으로 정렬되어 있을 때, 중복된 원소를 제거하고 각 고유한 원소가 한 번씩만 나타나도록 배열 내에서 변경한다. 원소들의 상대적인 순서는 유지되어야 한다. 그리고 배열 내의 고유한 원소의 개수를 반환해야 한다.

요약 하자면 배열에서 중복된 애들은 다 삭제 하고 고유 값만 남기라는 소리다.
아쉽게도? `set`  과 같은 기능사용이 안됨..
따라서 직접 기존의 배열을 조작해서 값을 얻어야 한다.

### 의사코드

- 배열을 조작해야 하므로 기존 배열에서 값을 컨트롤해야한다.
	- 덮어쓰기, 변수 값 동일하게 해서 사용하는게 안된다.

- 배열의 크기 만큼 반복문을 만든다.
- nums 의 첫 부분을 추출한다.
- nums 안에 중복된 값이 없을 때 까지 계속 앞 부분을 제거해준다 (뒷 부분으로 해도 별 차이는 없다.)
- 더 이상 중복된 값이 없다면 추출한 값을 배열 맨 뒤에 붙여준다.

### 구현코드

```python
class Solution:

    def removeDuplicates(self, nums: List[int]) -> int:

        for x in range(len(nums)):

            z = nums.pop(0)

            if z not in nums:

                nums.append(z)

        k = len(nums)

        return k
```


## 시간 복잡도와 공간 복잡도

- 최악의 경우 `O(n^2)`  
    - `for` 루프를 통해 `nums` 배열의 각 원소를 순회합니다.
    - 내부에서 `pop(0)` 메서드를 사용하면서 원소를 하나씩 제거하고 뒤쪽의 원소들을 한 칸씩 앞으로 당겨오는 연산이 필요합니다. 이 작업은 `O(n)` 시간이 걸립니다. 여기서 `n`은 `nums` 배열의 길이입니다.
    - `if z not in nums:` 라인에서 `nums` 배열을 또 다시 순회하면서 원소의 존재 여부를 확인하는데, 이 또한 최대 `O(n)` 시간이 소요됩니다.
    - 따라서, `for` 루프 하나당 최대 `O(n)`의 시간이 걸리며, 루프 안에서 `pop`과 `in` 연산을 하기 때문에 최악의 경우 `O(n^2)`의 시간 복잡도가 됩니다.
- 공간 복잡도: `O(1)`
    
    - 제공된 코드는 주어진 배열 `nums`를 직접 수정하며, 추가적인 배열이나 데이터 구조를 사용하지 않습니다. 따라서 공간 복잡도는 상수인 `O(1)`입니다.

## 회고 과정

### 지금 코드에서 뭘 개선할 수 있지?

- deque 을 사용하면 더 좋지만 리트코드에서는 사용이 불가능한 상환이 많은거 같다.
- pop 이나 append를 사용하지 않고 더 단순화 하면 개선될 수 있다고 생각했다. 그래서 좀 찾아봤다.

### 다른 사람 코드를 보고 그 기준으로 어떤 부분을 개선 할 수 있는지 최종 회고

```python
f = open("user.out", 'w')
for l in stdin:
    print('[', end='', file=f)
    print(*sorted(set(map(int, l.rstrip()[1:-1].split(',')))), file=f, sep=',', end='')
    print(']', file=f)
exit()

```

1. `f = open("user.out", 'w')`: 출력 파일 "user.out"을 열어 쓰기 모드로 준비한다.
    
2. `for l in stdin:`: 표준 입력(stdin)으로부터 한 줄씩 입력을 받아온다. 여기서 `l`은 입력된 각 줄을 나타낸다.
    
3. `print('[', end='', file=f)`: 출력 파일에 결과를 저장할 때, 각 줄의 시작 부분에 `[`를 출력한다.
    
4. `print(*sorted(set(map(int, l.rstrip()[1:-1].split(',')))), file=f, sep=',', end=''``:
    
    - `l.rstrip()[1:-1].split(',')`: 입력된 줄을 가공하여 숫자들을 분리한다. 예를 들어, 입력이 "[1, 2, 3, 2]"라면 문자열 "[1, 2, 3, 2]"을 `rstrip()`으로 양쪽 공백을 제거한 후 "[1, 2, 3, 2]"로 만듭다. 그리고 `[1, 2, 3, 2]`를 `split(',')`를 통해 쉼표를 기준으로 나누어 숫자들을 리스트로 만든다.
    - `map(int, ...)` : 각 문자열 숫자를 정수로 변환한다.
    - `set(...)` : 중복을 제거하기 위해 숫자들을 집합으로 변환한다.
    - `sorted(...)` : 고유한 숫자들을 정렬한다.
    - `*` : 리스트를 언패킹하여 개별 요소로 전달한다.
    - `sep=','` : `print` 함수가 개별 숫자를 출력할 때 쉼표로 구분한다.
    - `end=''` : `print` 함수가 줄바꿈을 하지 않도록 한다.
5. `print(']', file=f)`: 출력 파일에 결과를 저장할 때, 각 줄의 끝 부분에 `]`를 출력한다.
    
6. `exit()`: 프로그램을 종료한다.
    

이 코드는 입력된 각 줄을 처리하여 중복을 제거하고 고유한 원소를 정렬한 후 출력 파일에 저장한다. 이러한 방식으로 입력 데이터를 효율적으로 처리하며, 중복 제거와 정렬에 대한 성능은 입력 데이터의 크기에 따라 달라진다.

다음에 중복 제거를 사용할 때 한 번 연습겸 사용해봐야겠다.