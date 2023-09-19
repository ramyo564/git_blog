---
layout: single
title: "[자료구조 LinkedList] (알고리즘)"
categories: Algo
tags:
  - leetcode
  - Python
  - LinkedList
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
`Linked List`
# Linked list

- 링크드 리스트는 데이터를 삽입하고 삭제하는 연산이 많고 크기가 동적으로 변할 때 유용하며 배열과 같이 빠른 랜덤 엑세스가 필요하지 않을 때 적합하다.      
- 파이썬은 array가 리스트 형식으로 되어 있는데 다이나믹 어레이, 동적 배열로 구현되어 있다. 내부적으로 여분의 공간에 메모리가 할당 되어 있다. 데이터를 추가할 때마다 일정량의 메모리를 할당한다.

### 이게 뭔소리?

-> 자료구조 array 랑 비교를 해야하는데 array의 경우 메모리를 할당을 먼저 해준다. 그 후에 순차적으로 데이터를 넣어준다. 근데 array는 메모리 크키가 고정되어 있기 때문에 메모리가 꽉 차면 새로 메모리를 할당해줘야하고 이 때 불필요한 메모리가 낭비될 수 있는 문제가 있다.
## 단일 연결 리스트 (Singly Linked List):

- 단일 연결 리스트는 각 노드가 데이터와 다음 노드를 가리키는 포인터로 이루어진 선형 데이터 구조다.

### 개념

- 각 노드는 데이터와 다음 노드를 가리키는 포인터로 이루어져 있다.
- 가장 앞을 head 가장 뒤를 tail
- 마지막 노드의 포인터는 None 이며 리스트의 끝을 나타낸다.

### 요약

> 검색
> - 자신을 가리키고 있는 next pointer  를 통해서만 접근이 가능하기 때문에 n개의 노드를 갖고 있을 때 시간 복잡도는 O(n)
	- head 에서 순차적으로 순회해야되기 때문

>추가
>- 기본적으로 tail 노드 뒤에 붙임
>- 제일 끝을 찾아가기 때문에 O(n)
>- head의 포인터에 넣는 경우는 상수

>중간 삽입
>- 중간에 포인터를 조작해서 삽입이 가능하지만 찾아가는 과정은 처음부터 순차적으로 진행하기 때문에 시간복잡도는 O(n)

>삭제
>- O(n) -> (n 은 리스트의 크기) 포인터 조정을 통해서 조작 -> 가비지 컬랙터가 수거해감

>장점
>- 배열의 복사나 재할당 없이 데이터 추가 가능
>- 유연한 공간

>단점
>- 데이터 접근에 대한 시간이 늘어남

### 코드예시

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None

class SinglyLinkedList:
    def __init__(self):
        self.head = None

    def append(self, data):
        new_node = Node(data)
        if not self.head:
            self.head = new_node
        else:
            current = self.head
            while current.next:
                current = current.next
            current.next = new_node

    def delete_at_index(self, index):
        if not self.head:
            return

        if index < 0:
            raise IndexError("인덱스는 0 이상이어야 합니다")

        if index == 0:
            self.head = self.head.next
            return

        current = self.head
        prev = None
        count = 0

        while count < index:
            prev = current
            current = current.next
            count += 1

            if not current:
                raise IndexError("인덱스가 범위를 벗어납니다")

        prev.next = current.next

    def insert_at_index(self, data, index):
        if index < 0:
            raise IndexError("인덱스는 0 이상이어야 합니다")

        new_node = Node(data)

        if index == 0:
            # 헤드 위치에 삽입
            new_node.next = self.head
            self.head = new_node
        else:
            current = self.head
            count = 0

            while current:
                if count == index - 1:
                    new_node.next = current.next
                    current.next = new_node
                    return
                count += 1
                current = current.next

            if current is None:
                raise IndexError("인덱스가 범위를 벗어납니다")

    def display(self):
        current = self.head
        while current:
            print(current.data, end=' -> ')
            current = current.next
        print("None")
        
# 사용 예시
sll = SinglyLinkedList()
sll.append(1)
sll.append(2)
sll.append(3)
sll.display()  # 출력: 1 -> 2 -> 3 -> None

sll.delete_at_index(1)
sll.append(3)
sll.display()  # 출력: 1 -> 3 -> 3 -> None
sll.insert_at_index(4, 0)
sll.display() 


```



## 이중 연결 리스트 (Doubly Linked List):

- 이중 연결 리스트는 각 노드가 데이터와 이전 노드 및 다음 노드를 가리키는 포인터로 이루어진 선형 데이터 구조다.

### 개념

- 각 노드는 데이터와 이전 노드 및 다음 노드를 가리키는 포인터로 이루어져 있다..
- 처음 노드와 마지막 노드의 이전 노드 포인터는 None 이다.

### 요약

>검색
>- 반으로 쪼개서 하기 때문에 싱글보다는 2배 빠름

>추가
>- O(1) tail 앞에 넣을 수 있다.

>중간삽입
>-  인덱스를 찾을 때 head 랑 tail 중 가까운 부터 찾아서 들어감
>-  1/2 배 빠르지만 표기법상 O(n)

>삭제
>- O(n) -> (n 은 리스트의 크기) 포인터 조정을 통해서 조작 -> 가비지 컬랙터가 수거해감

>장점
>- 역방향 탐색 가능 -> 양방향 탐색이 필요한 경우에 유용
>- 삽입 및 삭제 효율성 향상 : 더블 링크드 리스트는 각 노드가 이전 노드를 가리키므로 노드를 삭제하거나 삽입하는 작업이 더 효율적으로 수행된다. 이전 노드를 직접 참조하므로 다음 노드의 연결만 변경하면 도니다.
>- 양방향 반복 가능

>단점
>-메모리 소모: 이전 노드를 가리키므로 싱글 링크드 리스트보다 더 많은 메모리를 소비한다. 공간 효율성 측면에서 단점이다.
>-구현 복잡성 : 두 개의 포인터를 가지므로 구현이 좀 더 복잡하고 디버깅이나  유지보수가 더 어려울 수 있다.
>-연산 속도와 캐시 지역성 : 두 개의 포인터를 업데이트해야 하므로 일부 연산은 싱글 링크드 리스트보다 더 느릴 수 있다. 또한 데이터가 불 연속적으로 저장되므로 CPU 캐시 지역성을 활용하기 어려울 수 있다.

### 코드 예시

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.prev = None
        self.next = None

class DoublyLinkedList:
    def __init__(self):
        self.head = None
        self.tail = None

    def append(self, data):
        new_node = Node(data)
        if not self.head:
            self.head = new_node
            self.tail = new_node
        else:
            new_node.prev = self.tail
            self.tail.next = new_node
            self.tail = new_node

    def insert_at_index(self, data, index):
        if index < 0:
            raise IndexError("인덱스는 0 이상이어야 합니다")

        new_node = Node(data)

        if index == 0:
            # 헤드 위치에 삽입
            new_node.next = self.head
            self.head.prev = new_node
            self.head = new_node
        else:
            current = self.head
            count = 0

            while current:
                if count == index - 1:
                    new_node.prev = current
                    new_node.next = current.next
                    if current.next:
                        current.next.prev = new_node
                    current.next = new_node
                    return
                count += 1
                current = current.next

            if current is None:
                raise IndexError("인덱스가 범위를 벗어납니다")

    def delete_at_index(self, index):
        if index < 0:
            raise IndexError("인덱스는 0 이상이어야 합니다")

        if index == 0:
            if self.head:
                self.head = self.head.next
                if self.head:
                    self.head.prev = None
        else:
            current = self.head
            count = 0

            while current:
                if count == index:
                    if current.prev:
                        current.prev.next = current.next
                    if current.next:
                        current.next.prev = current.prev
                    return
                count += 1
                current = current.next

            if current is None:
                raise IndexError("인덱스가 범위를 벗어납니다")
            
    def delete_last(self):
        if not self.head:
            return

        current = self.head

        # 리스트에 노드가 하나만 있는 경우
        if not current.next:
            self.head = None
            return

        while current.next:
            current = current.next

        current.prev.next = None

    def display(self):
        current = self.head
        while current:
            print(current.data, end=' <-> ')
            current = current.next
        print("None")

# 사용 예시
dll = DoublyLinkedList()
dll.append(1)
dll.append(2)
dll.append(3)
dll.insert_at_index(4, 1)  # 인덱스 1 위치에 4를 삽입
dll.display()  # 출력: 1 <-> 4 <-> 2 <-> 3 <-> None
dll.delete_at_index(2)  # 인덱스 2 위치의 노드 삭제
dll.display()  # 출력: 1 <-> 4 <-> 3 <-> None
```

## 원형 연결 리스트  (Circular Linked List)

- 원형 연결 리스트는 마지막 노드가 첫 번째 노드를 가리키는 연결 리스트입니다.

### 개념

- 마지막 노드의 포인터가 첫 번째 노드를 가리키며, 리스트는 끝이 없이 계속 된다.

### 요약

> 장점
> 1. **순회가 용이:** 원형 연결 리스트는 처음 노드부터 순회를 시작하더라도 끝 노드로 이동할 수 있어서 순회 작업이 간편하다. 순회가 끝난 후 다시 처음 노드로 돌아올 수 있기 때문에 반복 순회가 쉽다.
    2. **삽입 및 삭제가 효율적:** 리스트의 중간에 노드를 삽입하거나 삭제할 때, 해당 노드의 앞뒤 노드의 연결만 수정하면 되므로 연산이 빠르다. 특히, 리스트의 끝에 노드를 추가하거나 삭제하는 경우에도 빠르게 수행할 수 있다.

>단점
>1. **헤드 노드 탐색이 어려움:** 원형 연결 리스트는 처음 노드부터 시작하기 때문에 리스트의 시작부터 특정 노드에 접근하려면 전체 리스트를 순회해야 한다. 이로 인해 특정 노드에 빠르게 접근하기 어려울 수 있다.
>2. **루프 발생 가능성:** 잘못 구현된 경우 무한 루프가 발생할 수 있으며, 노드를 순회하는 동안 끝 노드로 돌아올 수 있는지 확인해야 한다.
>3. **메모리 공간 사용:** 각 노드는 다음 노드에 대한 참조를 가지고 있으므로 메모리 공간을 더 많이 사용할 수 있다.


### 코드 예시:

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None

class CircularLinkedList:
    def __init__(self):
        self.head = None
        self.tail = None

    def append_first(self, data):
        new_node = Node(data)
        if not self.head:
            self.head = new_node
            self.tail = new_node
            new_node.next = self.head
        else:
            new_node.next = self.head
            self.head = new_node
            self.tail.next = self.head

    def append_last(self, data):
        new_node = Node(data)
        if not self.head:
            self.head = new_node
            self.tail = new_node
            new_node.next = self.head
        else:
            new_node.next = self.head
            self.tail.next = new_node
            self.tail = new_node

    def insert_at_index(self, data, index):
        if index < 0:
            raise IndexError("인덱스는 0 이상이어야 합니다")

        new_node = Node(data)

        if index == 0:
            new_node.next = self.head
            self.head = new_node
            self.tail.next = new_node
        else:
            current = self.head
            count = 0

            while count < index - 1:
                count += 1
                current = current.next
                if current == self.head:
                    raise IndexError("인덱스가 범위를 벗어납니다")

            new_node.next = current.next
            current.next = new_node

    def delete_at_index(self, index):
        if not self.head:
            return

        if index < 0:
            raise IndexError("인덱스는 0 이상이어야 합니다")

        if index == 0:
            if self.head == self.tail:
                self.head = None
                self.tail = None
            else:
                self.head = self.head.next
                self.tail.next = self.head
        else:
            current = self.head
            prev = None
            count = 0

            while count < index:
                prev = current
                current = current.next
                count += 1
                if current == self.head:
                    raise IndexError("인덱스가 범위를 벗어납니다")

            prev.next = current.next
            if current == self.tail:
                self.tail = prev

    def get(self, index):
        if not self.head:
            raise IndexError("리스트가 비어있습니다")

        if index < 0:
            raise IndexError("인덱스는 0 이상이어야 합니다")

        current = self.head
        count = 0

        while count < index:
            current = current.next
            count += 1
            if current == self.head:
                raise IndexError("인덱스가 범위를 벗어납니다")

        return current.data

    def display(self):
        if not self.head:
            return

        current = self.head

        while True:
            print(current.data, end=' -> ')
            current = current.next
            if current == self.head:
                break

        print("Head")

# 사용 예시
cll = CircularLinkedList()
cll.append_first(1)
cll.append_first(0)  # 0 -> 1
cll.append_last(2)   # 0 -> 1 -> 2
cll.insert_at_index(3, 2)  # 인덱스 2 위치에 3을 삽입: 0 -> 1 -> 3 -> 2
cll.display()  # 출력: 0 -> 1 -> 3 -> 2 -> Head
print(cll.get(2))  # 인덱스 2 위치의 요소 가져오기 (출력: 3)
cll.delete_at_index(1)  # 인덱스 1 위치의 노드 삭제: 0 -> 3 -> 2
cll.display()  # 출력: 0 -> 3 -> 2 -> Head


```



## Linked List 정리

- 데이터를 저장하는 데 사용되는 선형 데이터 구조다.
- 링크드 리스트는 데이터 요소를 노드라고 하는 개체로 구성하며, 각 노드는 데이터 필드와 다음 노드를 가리키는 포인터로 이루어져 있다.
- 링크드 리스트는 배열과 달리 데이터 요소들이 메모리에서 연속적으로 저장되지 않고 각 노드가 독립적으로 힙 메모리에 할당되며 서로 연결되어 있는 구조를 갖는다.

### 장점

- 크기조절 용이성
	- 링크드 리스트는 동적으로 크기를 조절하기 쉽다. 새로운 노드를 추가하거나 삭제할 때 메모리를 효율적으로 사용할 수 있다.
- 빠른 삽입 및 삭제
	- 특정 위치에 노드를 삽입하거나 삭제하는 연산이 배열에 비해 더 빠르다. 배열의 경우 요소를 밀거나 당기는 작업이 필요하지 않기 때문이다.
- 메모리 관리
	- 링크드 리스트는 각 노드가 독립적으로 할당 되므로 메모리 사용을 효율적으로 관리할 수 있다.

### 단점

- 랜덤 엑세스 불가능
	- 배열과 달리 링크드 리스트는 특정 인덱스에 직접 접근하는 것이 어렵다. 원하는 요소에 접근하기 위해서는 처음부터 시작하여 차례로 순회해야 한다. 시간 복잡도가 O(n)다.
- 메모리 오버헤드
	- 링크드 리스트는 각 노드마다 포인터를 저장해야 하므로 배열에 비해 메모리 헤드가 크다.
- 캐시 지역성 부족
	- 연속적인 메모리 위치에 저자되지 않기 때문에 CPU 캐시 지역성을 활용하기 어렵다.