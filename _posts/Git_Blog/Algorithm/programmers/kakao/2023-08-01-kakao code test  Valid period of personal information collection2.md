---
layout: single
title: "프로그래머스 - [카카오] - 개인정보 수집 유효기간 (알고리즘)"
categories: Algo
tag: [Java,Python,JavaScript,Lv_2,"[카카오] - 개인정보 수집 유효기간"]
toc: true
toc_sticky: true
author_profile: false
sidebar:

---

[문제링크](https://school.programmers.co.kr/learn/courses/30/lessons/150370?language=python3)

```python
today = "2022.05.19"
terms =  ["A 6", "B 12", "C 3"]
privacies = ["2021.05.02 A", "2021.07.01 B", "2022.02.19 C", "2022.02.20 C"]

answer = []
answer_number = 1
# 날짜 정리
today = today.split(".")
today_year = int(today[0])
today_month = int(today[1])
today_day = int(today[2])
# 기간 정리
terms_dic = {}
for x in terms:
    x.split(" ")
    terms_dic[x[0]] = x[2:]

for x in privacies:

    check_terms = x.split(" ")[1:]
    term_month = int(terms_dic[check_terms[0]])
    
    check_today = x.split(" ")[:1]
    check_today = check_today[0].split(".")
    
    if int(term_month) % 12 == 0:
        new_check_month = int(check_today[1])
        new_check_year = int(check_today[0]) + (term_month // 12)
        new_check_day = int(check_today[2])
    
    else:
        if (int(check_today[1]) + int(term_month)) % 12 == 0:
            new_check_month = 12
            new_check_year = int(check_today[0])
        else:
            new_check_month = (int(check_today[1]) + int(term_month)) % 12
            new_check_year = int(check_today[0]) + ((int(check_today[1]) + int(term_month)) // 12)
        
        new_check_day = int(check_today[2])
        
    if today_year > new_check_year:
        answer.append(answer_number)
        answer_number += 1
    
    elif today_year == new_check_year and today_month > new_check_month:
        answer.append(answer_number)
        answer_number += 1
        
    elif today_year == new_check_year and today_month == new_check_month and today_day >= new_check_day:
        answer.append(answer_number)
        answer_number += 1

    else:
        
        answer_number += 1
    
print(answer)

```
처음에 짠 코드인데 1번과 17번에서 통과를 하지 못 했다 결국 해결을 하지 못 해서 다른 사람들의 코드를 참고했다.     


```python
def solution(today, terms, privacies):
    answer = []
    terms_dic = {t[0]: int(t[2:]) * 28 for t in terms}  
    # 약관 코드를 key값으로, 유효기간을 value값으로 하는 dict

    today = list(map(int, today.split('.')))
    today = today[0] * 12 * 28 + today[1] * 28 + today[2]  
    # 오늘 날짜를 일 단위로 변환

    for idx in range(len(privacies)):
        day, code = privacies[idx].split(' ')  
        # 개인정보 수집일과 약관 코드를 분리
        day = list(map(int, day.split('.')))
        day = day[0] * 12 * 28 + day[1] * 28 + day[2]  
        # 개인정보 수집일을 일 단위로 변환
        if day + terms_dic[code] <= today:  
        # 개인정보 수집일 + 유효기간이 오늘 날짜보다 이전이면
            answer.append(idx + 1)

    return answer
```

위 코드로는 문제 없이 통과가 되는데 내 코드가 통과하지 못한 점을 분석해보자

1. 주어진 데이터를 나누는 데는 문제가 없었는데 날짜 처리를 올바르게 하지 못 한 것 같다.
2. 확실하게 날짜 처리가 이루어지지 않으니 코드가 점점 더러워졌다.
3. 주어진 문제에서 한 달을 28일로 정의 했으니 그 부분을 그대로 시행하면 되었는데 귀찮아서 대충한 것도 문제였다.

## 몰랐던 부분, 잘 하지 못 했던 부분

```python
terms_dic = {t[0]: int(t[2:]) * 28 for t in terms}  
```

1. 딕셔너리 컴프리핸션에 대해서 편하게 다루지 못 했었던 것 같다.
2. 위와 같이 코드를 쓰면 깔끔하게 정리가 되는데 정리를 못 하니까 코드가 지저분해지고, 지저분한게 보기 싫으니 마음데로 생략해버려서 테스트에 통과하지 못 한 것 같다.

```python
list(map(int, today.split('.')))
```

1. map 에 대해서 여태까지 그냥 대충 넘겼던거 같다. map 에 대해서 제대로 알아보자면 map은 첫번째 인수의 함수를 두번째 인수에 전부 적용한다고 생각하면 된다.
	1. 위 코드에서는 today에서 . 으로 나눠진 데이터 3개가 모두 int로 매칭된 후 list에 담겨졌다고 생각하면 된다.

## 시간 복잡도 & 공간 복잡도

**시간 복잡도:**

1. **`terms_dic = {t[0]: int(t[2:]) * 28 for t in terms}`**: 이 부분에서 **`terms`** 리스트의 길이를 n이라고 하면, **`for`** 루프를 통해 모든 **`terms`**의 항목을 순회하면서 **`terms_dic`** 딕셔너리를 생성한다. 따라서 이 부분의 시간 복잡도는 O(n) 다.
2. **`today = list(map(int, today.split('.')))`**: 오늘 날짜를 숫자 리스트로 변환하는데, 오늘 날짜를 **`y.m.d`** 형식으로 표현하므로 **`map()`** 함수를 사용하여 3개의 숫자를 변환한다. 따라서 이 부분의 시간 복잡도는 O(1)다.
3. **`for idx in range(len(privacies)):`**: **`for`** 루프를 통해 **`privacies`** 리스트의 모든 항목을 순회한다. 따라서 이 부분의 시간 복잡도는 O(k)다. 여기서 k는 **`privacies`** 리스트의 길이다.
4. **`day = list(map(int, day.split('.')))`**: 개인정보 수집일을 숫자 리스트로 변환하는데, 개인정보 수집일을 **`y.m.d`** 형식으로 표현하므로 **`map()`** 함수를 사용하여 3개의 숫자를 변환한다. 따라서 이 부분의 시간 복잡도도 O(1) 다.
5. **`if day + terms_dic[code] <= today:`**: 각 개인정보 수집일과 약관 코드를 **`for`** 루프를 통해 비교하고, 해당하는 경우에는 결과 리스트 **`answer`**에 값을 추가한다. 이 부분의 시간 복잡도는 O(k)다. 여기서 k는 **`privacies`** 리스트의 길이다.

따라서 전체 시간 복잡도는 O(n + k)다.

**공간 복잡도:**

1. **`terms_dic = {t[0]: int(t[2:]) * 28 for t in terms}`**: **`terms_dic`** 딕셔너리를 생성하는데, **`terms`** 리스트의 길이를 n이라고 하면, n개의 항목이 딕셔너리에 저장된다. 따라서 이 부분의 공간 복잡도는 O(n)다.
2. **`today = list(map(int, today.split('.')))`**: 오늘 날짜를 숫자 리스트로 변환하는데, 오늘 날짜를 **`y.m.d`** 형식으로 표현하므로 3개의 숫자가 저장된다. 따라서 이 부분의 공간 복잡도는 O(1)다.
3. **`day = list(map(int, day.split('.')))`**: 개인정보 수집일을 숫자 리스트로 변환하는데, 개인정보 수집일을 **`y.m.d`** 형식으로 표현하므로 3개의 숫자가 저장된다. 따라서 이 부분의 공간 복잡도도 O(1)다.
4. **`answer`**: 결과 리스트인 **`answer`**는 최악의 경우 **`privacies`** 리스트의 길이와 동일한 길이가 될 수 있다. 따라서 이 부분의 공간 복잡도는