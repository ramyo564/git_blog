---
layout: single
title: " [딥러닝 기초] 신경망 학습!"
categories: ML_DL
tag: [Python,"[밑딥] 경사 하강법"]
toc: true
toc_sticky: true
author_profile: false

---
# 신경망 학습이란?

## 신경망 학습 요약

1. 여기서 학습이란 훈련 데이터로부터 가중치 매개변수의 최적값을 자동으로 획득하는 것을 뜻한다.
2. 책에서는 신경망이 학습할 수 있도록 해주는 지표인 손실 함수가 소개된다.
3. 이 손실 함수의 결괏값을 가장 작게 만드는 가중치 매개변수를 찾는 것이 학습의 목표다.
4. 손실 함수의 값을 가급적 작게 만드는 기법으로 함수의 기울기를 활용하는 경사법이 소개된다.

## 데이터에서 학습한다는게 무슨 소리일까?
- 데이터에서 학습한다는 것은 가중치 매개변수의 값을 데이터를 보고 자동으로 결정한다는 뜻이다.
- 2장 퍼셉트론에서는 진리표를 보면서 사람이 수작업으로 매개변수 값을 설정했지만 이 때는 매개변수가 겨우 3개 였다.
- 신경망에서는 수억개가 넘는데 이를 수작업으로 하는 것은 불가능하다.

## 데이터 주도학습

![](https://i.imgur.com/2woErGu.png)

여기서 숫자 5를 인식하려면 어떻게 알고리즘을 짜야할까?

![](https://i.imgur.com/FlqlJGH.png)
> - 신경망의 특징은 이미지를 '있는 그대로' 학습한다.
> - 이러한 이유로 딥러닝을 종단간 기계학습이라고도 한다.
> - 사람의 개입없이 처음부터 끝까지라는 목표한 결과를 출력한다.

## 훈련 데이터와 시험 데이터
왜 훈련데이터와 시험 데이터를 나눌까?
>- 데이터셋 하나만으로 매개변수의 학습과 평가를 수행하면 범용 능력 검증이 안된다.
>- 현재 데이터셋은 잘 맞춰도 다른 데이터셋에서는 엉망일 수도 있는데 이렇게 한 데이터셋에만 지나치게 최적화된 상태를 ***오버피팅***이라고 한다.

## 손실 함수
- 신경망 학습에서 현재의 상태를 하나의 지표로 표현한다.
- 이 지표를 가장 좋게 만들어주는 가중치 매개변수의 값을 탐색
- 이 손실 함수는 임의의 함수를 사용할 수도 있지만 일반적으로 ***오차제곱함***과 ***교차 엔트로피 오차***를 사용한다.

### 오차제곱합

![](https://i.imgur.com/HEL0Vps.png)

- y_k는 신경망의 출력(신경망이 추정한 값)
- t_k 는 정답 레이블
- k 는 데이터의 차원 수를 나타낸다.
> - 이를 테면 "3.6 손글씨 숫자 인식" 예에서 y_k 와 t_k는 다음과 같은 원소 10개 짜리 데이터다
> - ![](https://i.imgur.com/RIC3CXa.png)
> - 여기에서 신경망의 출력 y는 소프트맥스 함수의 출력이다.
> - 소프트맥스 함수의 출력은 확률로 해석할 수 있으므로 이미지가 0일 확률은 0.1 이다.
> - t는 숫자 0부터 시작 따라서 2가 정답일 확률이 0.6, 정답은 2다.

### 파이썬으로 구현
```python
def sum_squares_error(y, t):
    return 0.5 * np.sum((y-t)**2)
```

#### 예시

![](https://i.imgur.com/sXYJpeD.png)
- 위에서 첫 번째의 예는 정답이 2 신경망의 출력도 2에서 가장 높은 경우다.
- 두 번째에서 정답은 똑같이 2지만 신경망의 출력은 7에서 가장 높다.
- 오차제곱합 기준으로 첫 번째 추정 결과가 정답에 더 가까울 것으로 판단할 수 있다.
	- (첫 번째 오차가 더 작다)

### 교차 엔트로피 오차
![](https://i.imgur.com/PsE2WLt.png)

- 여기에서 로그는 밑이 자연로그다.
- y_k는 신경망의 출력
- t_k는 정답 레이블
- t_k는 정답에 해당하는 인덱스의 원소만 0이고 나머지는 0 (원-핫 인코딩)
>- 예를 들어 정답 레이블은 2가 정답이라고 하고 이 때의 신경망 출력이 0.6이라고 한다면
>- 교차 엔트로피 오차는 -log0.6 = 0.51이 된다.
>- 같은 조건에서 신경망 출력이 0.1 이라면 -log0.1 = 2.30이 된다.
>	- 즉 교차 엔트로피 오차는 정답일 때의 출력이 전체 값을 정하게 된다.

![](https://i.imgur.com/a60ZY8x.png)

![](https://i.imgur.com/Qo0fo7z.png)

### 파이썬으로 구현
```python
# 교차 엔트로피 오차
def cross_entropy_error(y, t):
    delta = 1e-7
    # 1e-7 = 0.0000001
    return -np.sum(t * np.log(y + delta))
```

> - np.log() 함수에 0을 입력하면 마이너스 무한대가 되어 더 이상 계산을 진행할 수 없다
> - 따라서 아주 작은 값을 더해 절대 0이 되지 않도록 만들어준다.

#### 예시
![](https://i.imgur.com/gF8roLC.png)

- 첫 번째 예는 정답일 때의 출력이 0.6 -> 교차 엔트로피 오차는 약 0.51
- 두 번째 예에서는 정답의 출력이 더 낮은 0.1 -> 교차 엔트로피 오차는 2.3
	- 즉 결과(오차 값)이 더 작은 첫 번째 추정이 정답일 가능성이 높다고 판단한다.

## 미니배치 학습

1. 기계학습 문제는 훈련 데이터를 사용해 학습한다.
2. 훈련 데이터에 대한 손실 함수의 값을 구하고 그 값을 최대한 줄여주는 매개변수를 찾아낸다.
3. 이렇게 하려면 모든 훈련 데이터를 대상으로 손실 함수 값을 구해야 한다.
4. 즉 훈련 데이터가 100개가 있으면 그로부터 계산한 100개의 손실 함수 값들의 합을 지표로 삼는다.

![](https://i.imgur.com/7fIW25k.png)
1. 데이터가 N개라면 t_nk는 n번째 데이터의 k번쨰 값을 의미한다.
   (y_nk는 신경망의 출력, t_nk는 정답 레이블이다.)
2. N으로 나눔으로써 평균 손실 함수르 구한다.
3. 평균을 구해 사용하면 훈련 데이터 개수와 상관없이 언제든 통일된 지표를 얻을 수 있다.
4. 많은 데이터를 대상으로 일일이 손실함 수를 계산 하는 것이 아닌 훈련 데이터로부터 일부만 골라 학습을 수행한다.
5. 이 일부를 ***미니배치***라고 한다.
6. 예를 들어 60,000 장의 훈련 데이터중에 100장을 무작위로 뽑아 100장만 사용하여 학습하는 학습을 ***미니배치 학습***이라고 한다.

### MNIST + 미니배치

```python
import sys, os
sys.path.append(os.pardir)
import numpy as np
from dataset.mnist import load_mnist

(x_train, t_train), (x_test, t_test) = \
    load_mnist(normalize=True, one_hot_label=True)
print(x_train.shape) # (60000, 784)
print(t_train.shape) # (60000z 10)

train_size = x_train.shape[0]
batch_size = 10
batch_mask = np.random.choice(train_size, batch_size)
# 이전 배치는 스텝이였는데 이번 배치는 10개를 무작위로 뽑는거임

x_batch = x_train[batch_mask]
t_batch = t_train[batch_mask]
```

![](https://i.imgur.com/22kp66x.png)
> - 넘파이 random.choice로 train_size에 있는 데이터 무작위 10개를 뽑아온다.
> - 손실 함수도 미니배치로 계산한다.

### (배치용) 교차 엔트로피 오차 구현하기
```python
import sys, os
sys.path.append(os.pardir)
import numpy as np
from dataset.mnist import load_mnist

# 배치용 교차 엔트로피 오차 구현하기
# 1번 원 핫코딩일때
def cross_entropy_error(y, t):
    if y.ndim == 1:
        # ndim 은 y 의 차원의 수, 배열의 축 수
        # t는 정답테이블
        # y는 신경망 출력
        t = t.reshape(1, t.size)
        y = y.reshape(1, y.size)
	    batch_size = y.shape[0]
	    return -np.sum(t * np.log(y + 1e-7)) / batch_size

# 1e-7 = 0.0000001
# 2번
def cross_entropy_error(y, t):
    if y.ndim == 1:
        t = t.reshape(1, t.size)
        y = y.reshape(1, y.size)
    batch_size = y.shape[0]
    return -np.sum(np.log(y[np.arange(batch_size), t] + 1e-7)) / batch_size


'''
reshape는 배열 변환 ->
a = [1,2,3,4,5,6,7,8]
b = np.reshape(a,(2,4))
c = np.reshape(a,(4,2))
[[1 2 3 4]
 [5 6 7 8]]
 [[1 2]
 [3 4]
 [5 6]
 [7 8]]
print(b.shape[0])
print(b.shape[1])
2
4
'''
```
> y = 신경망의 출력
> t = 정답 테이블
> t 가 0일 경우 교차 엔트로피 오차도 0, 그 계산은 무시함



### 왜 손실 함수를 설정하는가?
- 신경망 학습에서는 최적의 매개변수(가중치와 편향)을 탐색할 때 손실 함수의 값을 가능한 작게 만들어주는 매개변수 값을 찾는다.
- 이때 매개변수의 미분(정확히는 기울기)를 계산하고 그 미분 값을 단서로 매개변수의 값을 서서히 갱신하는 과정을 반복한다.
- 예를 들어 미분 갑이 음수면 가중치 매개변수를 양의 방햔으로 변화시켜 손실 함수의 값을 줄이고 반대로 미분 값이 양수면 가중치 매개변수를 음의 방향으로 변화시켜 손실 함수의 값을 줄일 수 있다.
- 하지만 미분 값이 0이면 가중치 매개벼수를 어느 쪽으로 움직여도 손실 함수의 값은 줄어들지 않는다.
- 이 때 가중치 매개변수의 갱신은 멈춘다.
>- 정확도를 지표로 삼으면 안되는 이유는 미분 값이 대부분의 장소에서 0이 되어 매개변수를 갱신할 수 없기 때문이다.

#### 정확도를 지표로 삼으면 매개변수의 미분이 대대분의 장소에서 0이 되는 이유는 무엇일까?
- 예시
	- 한 신경망이 100장의 훈련데이터 중 32장을 올바로 인식한다면 정확도는 32%다.
	- 만약 정확도가 지표였다면 가중치 매개변수의 값을 조금씩 바꾼다고 해도 정확도는 그대로 32%다.
	- 즉 매개변수를 약간만 조정해서는 정확도가 개선되지 않고 일정하게 유지된다.
	  
- 손실 함수를 지표로 삼는다면?
	- 현재 손실 함수 값이 0.925313 같은 수치라면 매개변수의 값이 조금 변화하면 손실함수의 값도 0.93434 처럼 연속적으로 변화한다.
	- 정확도는 매개변수의 미소한 변화에는 거의 반응을 보이지 않고 반응이 있더라도 그 값이 불연속적으로 갑자기 변화한다.
	- 계단 함수를 활성화 함수로 사용하지 않는 이유와도 들어맞는다.
	- 만약 활성화 함수로 계단 함수를 사용한다면 지금까지와의 이유로 신경망 학습이 잘 이뤄지지 않는다.
	- 계단 함수의 미분은 다음 그림과 같이 대부분의 장소에서(0이 외의 곳) 에서 0이다.
	- 그 결과 계단 함수를 이용하면 손실 함수를 지표로 삼는 게 아무 의미가 없다.
	- 매개변수의 작은 변화가 주는 파장을 계단 함수가 말살하여 손실 함수의 값에는 아무런 변화가 나타나지 않기 때문이다.
![](https://i.imgur.com/YQ0rMdO.png)

- 계단 함수는 한순간만 변화를 일으키지만 시그모이드 함수의 미분(접선)은 그림과 같이 출력이 연속적으로 변하고 곡선의 기울기도 연속적으로 변한다.
- 즉 시그모이드 함수의 미분은 어느장소라도 0이 되지 않는다.
- 이는 신경망 학습에서 중요한 성질로, 기울기가 0이 되지 않는 덕분에 신경망이 올바르게 학습할 수 있는 점이다.

## 수치 미분
경사법에서는 기울기(경사) 값을 기준으로 나아갈 방향을 정한다. 

### 미분
미분이란 한 순간의 변화량을 표시한 것
![](https://i.imgur.com/oWzbI44.png)

#### 파이썬으로 구현
```python
def numerical_diff(f, x):
    h = 1e-4 # 0.0001
    return (f(x+h) - f(x-h)) / (2*h)
```
>- 파이썬에서는 반올림 오차 문제가 있다. 1e-50을 float32형으로 나타내면 0.0이 된다.
>- 이 미세값을 1e-4 정도로 사용하면 문제 없다.
>- h를 무한히 0으로 좁히는 것은 불가능하다.
>- 이러한 오차를 줄이기 위해 (x+h) 와 (x-h)일때 함수의 차분을 계산해서 쓴다.
>- 이를 중심차분 또는 중앙차분이라하며
>- (x+h) 와 x의 차분은 전방차분이라고 한다.
>- 요약하자면 우리가 알고 있는 미분은 ***해석적 미분*** 이고 ***수치미분은 근사치로 계산*** 하는 거라고 생각하면 된다.

![](https://i.imgur.com/SXCqk5C.png)

<iframe width="1567" height="586" src="https://www.youtube.com/embed/rqX27ZKgjcQ" title="인하대물리1 03A차분과 미분" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

### 수치 미분의 예

![](https://i.imgur.com/BORdRoh.png)

#### 파이썬 구현

![](https://i.imgur.com/K5aGkbA.png)

#### 함수 그리기

![](https://i.imgur.com/nA5LK1k.png)

![](https://i.imgur.com/3jjjvns.png)

- x 가 5일 때와 10일 때의 함수 미분을 계산해보자
![](https://i.imgur.com/HJXPYmI.png)

>- 진정한 미분은 0.2 와 0.3 이지만 수치미분과 비교 했을 때 오차가 매우 작다.
>- x =5 , x = 10에서의 접선은 아래와 같다.

![](https://i.imgur.com/cGyBX7T.png)

### 편미분
- 앞과 달리 변수가 2개다
- ![](https://i.imgur.com/5dxjBIf.png)
- ![](https://i.imgur.com/u3dBHSh.png)

- 그래프
- ![](https://i.imgur.com/UCQgrM4.png)
- ![](https://i.imgur.com/cvzgc1O.png)
- ![](https://i.imgur.com/x70YNOU.png)
>- 문제 1에서는 x_1 = 4로 고정된 새로운 함수를 정의하고, 변수가 x_0 에 대해 수치 미분 함수를 적용했다.
>- 이렇게 구한 문제 1의 결과는 6.0000...
>- 문제 2의 결과는 7.99999... 
>- 편미분은 목표 변수를 제외한 나머지를 특정 값에 고정

## 기울기
```python
import numpy as np
# 기울기
# x[0] x[1] 동시에 계산하고 싶다면?
def numerical_gradient(f, x):
    h = 1e-4 # 0.0001
    grad = np.zeros_like(x) # x와 형상이 같은 배열을 생성
    # print(grad) array [0. 0.]
    # print(x.size) -> 2
    for idx in range(x.size):
        tmp_val = x[idx]
        # f(x+h) 계산
        x[idx] = tmp_val + h
        fxh1 = f(x)
        # f(x-h) 계산
        x[idx] = tmp_val - h
        fxh2 = f(x)
        grad[idx] = (fxh1 - fxh2) / (2*h)
        x[idx] = tmp_val # 값 복원
    return grad

def function_2(x):
    return x[0]**2 + x[1]**2

```
>- x = np.array[3.0, 4.0] 이라고 가정한다면 x.size 값은 2
>- range 로 인해 idx 는 0 , 1
>- x[0] 값은 3.0, x[1] 값은 4.0
>- x[0], x[1] 값 갱신
>- 파라미터 F 는 함수를 받아옴
>- f(x) = function_2(x) 갱신된 값 fxh1, fxh2 각각 대입

![](https://i.imgur.com/CUtJtMR.png)
> - 세 점 (3,4) , (0,2), (3,0) 에서의 기울기는 위와 같다.
> - 이 기울기를 백터로 그리면 아래와 같다.

![](https://i.imgur.com/wed5whJ.png)
>- 각 기울기는 각 지점에서 낮아지는 방향을 가리킨다.
>- 기울기가 가리키는 쪽은 각 장소에서 함수의 출력 값을 가장 크게 줄이는 방향이다.

### 경사법(경사 하강법)
신경망에서 최적의 매개변수 (가중치와 편향)을 학습 시에 찾아야 한다. 여기에서 최적이란 손실 함수가 최솟값이 될 때의 매개변수 값이다.
>- 일반적으로 매개변수 공간이 광대하여 어디가 최솟값이 되는 곳인지 짐작하기 어렵다.
>- 이런 상황에서 기울기를 잘 이용해 함수의 최솟값(또는 가능한 작은 값)을 찾으려는 것이 경사법이다.
>- 주의할 점은 각 지점에서 기울기가 가리키는 곳이 정말 함수의 최솟값이 있는지, 그 쪽으로 나아갈 방향인지는 보장할 수 없다.
>- 실제로 복잡한 함수에서 기울기가 가리키는 방향에 최솟값이 없는 경우가 대부분이다.

- 경사법은 현 위치에서 기울어진 방향으로 일정 거리만큼 이동한다.
- 그런 다음 이동한 곳에서도 마찬가지로 기울기를 구하고, 또 그 기울어진 방향으로 나아가기를 반복한다.
- 이런 방식으로 함수의 값을 점차 줄이는 것을 ***경사법***이라 한다.
- 특히 신경망 학습에서 경사법을 많이 사용한다.
![](https://i.imgur.com/BgE7RvC.png)
- 경사법의 수식은 다음과 같다
- ![](https://i.imgur.com/QYjPwGc.png)
>- 기호 에타 N 은 갱신하는 양을 나타낸다.
>- 이를 신경망 학습에서는 학습률이라고 한다.
>- 한 번의 학습으로 얼마만큼 학습해야할지, 즉 매개변수 값을 얼마나 갱신하느냐를 정하는 것이 학습률이다.
>- 위 식에서는 1회에 해당하는 갱신이고 이 단계를 반복한다.
>- 이 단계를 여러번 반복해서 서서히 함수의 값을 줄인다.
>	- 학습률 값은 0.01 이나 0.001등 미리 특정 값으로 정한다
>	- 일반적으로 값이 너무 크거나 작으면 좋은 장소를 찾아갈 수 없다.
>	- 보통 이 학습률 값을 변경하면서 올바르게 학습하고 있는지를 확인하면서 진행한다.

#### 경사 하강법 구현

```python
import numpy as np
from Chap_4_4_0_기울기 import numerical_gradient
# 경사하강법

def gradient_descent(f, init_x, lr=0.01, step_num=100):
    x = init_x
    for i in range(step_num):
        grad = numerical_gradient(f, x)
        x -= lr * grad
    return x
```
>- f 는 최적화 하려는 함수
>- init_x 는 초깃값
>- lr 은 learning late를 의미하는 학습률
>- step_num 경사법에 따른 반복 횟수를 뜻함

```python
# 경사하강법으로 f(x[0],f[1]) = x[0]^2 + x[1]^2 의 최솟값을 구하여라
def function_2(x):
    return x[0]**2 + x[1]**2
    
init_x = np.array([-3.0, 4.0])

a = gradient_descent(function_2, init_x=init_x, lr=0.1, step_num=100)

print(a)
# array([ -6.11110793e-10, 8.14814391e-10])
```
>- 초기값을 (-3.0, 4.0) 으로 설정
>- lr = 0.1 x grad(기울기) =  (array ([-6.0, 8.0]))
>- 첫 return x 값은 [-2.4 3.2]
>	- (-3.0 , 4.0) - (-0.6 , 0.8)
>- [-2.4, 3.2] 의 기울기로 다시 시작
>- 반복문이 끝날 때까지 계속 차감하면서 값을 업데이트

- ![](https://i.imgur.com/oafQbW5.png)
- ![](https://i.imgur.com/yOyPShr.png)
##### 학습률에 따른 차이
![](https://i.imgur.com/QbckfX9.png)


### 신경망에서의 기울기

![](https://i.imgur.com/FXoZxkX.png)
- ![](https://i.imgur.com/NkXsf2j.png) 의 각 원소는 각각의 원소에 관한 편미분이다.
- 예를 들어 1행 1번째 원소인 ![](https://i.imgur.com/3IeNrfk.png) 은 w_11 을 조금 변경했을 때 손실함수 L이 얼마나 변화했느냐를 나타낸다.

```python
class simpleNet:
    def __init__(self):
        self.W = np.random.randn(2,3) # 정규분포로 초기화
        self.Z = 1
    def predict(self,x):
        return np.dot(x, self.W)

    # 행렬의 곱
    def loss(self, x, t):
        z = self.predict(x)
        y = softmax(z)
        loss = cross_entropy_error(y, t)
        return loss
```
>- W는 random 으로 2행 3열의 행렬 생성


- ![](https://i.imgur.com/C16nIYf.png)
- ![](https://i.imgur.com/P02YhNz.png)
-  x= 1x2, W = 2x3
- np.argmax() 배열에서 가장 높은 값의 인덱스 반환
- t= 임의의 정답 레이블
- 위에서는 1.1328074가 최대값이라 np.argmax(p) 값은 2
- loss의 z값은 p값과 동일
- p 값은 1차원
- 오버플로 대비해서 p의 max 값 제거해줌
	- x값 = [1.05414809 0.63071653 1.1328074 ]
	- np.max값 = 1.1328074
	- x - np.max(x) 값 = [-0.07865931 -0.50209087  0.        ]
- soft max 값은 np.exp(x) / np.sum(np.exp(x))
- 위 식에서 `y = [0.36541271 0.23927078 0.39531651]`
- y 값이 1 차원이므로
	- t = t.reshape(1, t.size)
		- t 값은 [2]
	- y = y.reshape(1, y.size)
		- y 값은 [[0.36541271 0.23927078 0.39531651]]
	- batch_size = y.shape[0]
	- return -np.sum(np.log(y[np.arange(batch_size), t] + 1e-7)) / batch_size
- 0.92806853663411326


![](https://i.imgur.com/dlHkapx.png)
![](https://i.imgur.com/UZuFQIi.png)


출처 : 밑바닥부터 시작하는 딥러닝