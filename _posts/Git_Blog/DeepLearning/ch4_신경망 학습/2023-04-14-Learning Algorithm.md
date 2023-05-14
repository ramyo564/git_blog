---
layout: single
title: "학습 알고리즘 구현하기!"
categories: ML_DL
tag: [Python,"[밑딥] 학습 알고리즘 구현하기!"]
toc: true
toc_sticky: true
author_profile: false

---
# 학습 알고리즘 구현하기

- 전제 
	- 신경망에는 적응 가능한 가중치와 편향이 있고, 이 가중치와 편향을 훈련 데이터에 적응하도록 조정하는 과정 을 ‘학습’이라고함 
	- 신경망 학습은 다음과 같이 4단계로 수행
	- 1단계-미니배치 
		- 훈련 데이터 중 일부를 무작위로 가져옴. 이렇게 선별한 데이터를 미니배치라 하며, 그 미니배치의 손실 함수 값을 줄이는 것이 목표
	- 2단계-기울기 
		- 산출 미니배치의 손실 함수 값을 줄이기 위해 각 가중치 매개변수의 기울기를 구함. 기울기는 손실 함수의 값 을 가장 작게 하는 방향을 제시
	- 3단계 - 매개변수 갱신 
		- 가중치 매개변수를 기울기 방향으로 아주 조금 갱신
	- 4단계 - 반복
		- 1~3단계를 반복

>- 위는 신경망 학습이 이루어지는 순서다.
>- 이는 경사 하강법으로 매개변수를 갱신하는 방법 이며, 이때 데이터를 미니배치로 무작위로 선정하기 때문에 확률적 경사 하강법 stochastic gradient descent. SGD이라고 부른다. 
>- ‘확률적으로 무작위로 골라낸 데이터’에 대해 수행하는 경사 하강법이라는 의미 
>- 대부분의 딥러닝 프레임워크는 확률적 경사 하강법의 영어 머리글자를 딴 SGD라는 함수로 이 기능을 구현


## 손글씨 숫자를 학습하는 신경망 구현

>- 여기에서는 2층 신경망（은닉층 이 1 개인 네트워크）을 대상으로 MNIST 데이터셋을 사용하여 학습을 수행

```python
# coding: utf-8
import sys, os
sys.path.append(os.pardir)  # 부모 디렉터리의 파일을 가져올 수 있도록 설정
from common.functions import *
from common.gradient import numerical_gradient

# 2층 신경망 클래스 구현하기

class TwoLayerNet:
    def __init__(self, input_size, hidden_size, output_size, weight_init_std=0.01):

        # 가중치 초기화
        self.params = {}
        self.params['W1'] = weight_init_std * np.random.randn(input_size, hidden_size)
        self.params['b1'] = np.zeros(hidden_size)
        self.params['W2'] = weight_init_std * np.random.randn(hidden_size, output_size)
        self.params['b2'] = np.zeros(output_size)

    def predict(self, x):
        W1, W2 = self.params['W1'], self.params['W2']
        b1, b2 = self.params['b1'], self.params['b2']
        a1 = np.dot(x, W1) + b1
        z1 = sigmoid(a1)
        a2 = np.dot(z1, W2) + b2
        y = softmax(a2)
        return y

    # x : 입력 데이터, t : 정답 레이블
    def loss(self, x, t):
        y = self.predict(x)
        return cross_entropy_error(y, t)
        
    def accuracy(self, x, t):
        y = self.predict(x)
        y = np.argmax(y, axis=1)
        t = np.argmax(t, axis=1)
        accuracy = np.sum(y == t) / float(x.shape[0])
        return accuracy

    # x : 입력 데이터, t : 정답 레이블
    def numerical_gradient(self, x, t):
        loss_W = lambda W: self.loss(x, t)
        
        grads = {}
        grads['W1'] = numerical_gradient(loss_W, self.params['W1'])
        grads['b1'] = numerical_gradient(loss_W, self.params['b1'])
        grads['W2'] = numerical_gradient(loss_W, self.params['W2'])
        grads['b2'] = numerical_gradient(loss_W, self.params['b2'])
        return grads
        
       # 오차역전법 -> 다음 장에서 설명
    def gradient(self, x, t):
        W1, W2 = self.params['W1'], self.params['W2']
        b1, b2 = self.params['b1'], self.params['b2']
        grads = {}
        batch_num = x.shape[0]

        # forward
        a1 = np.dot(x, W1) + b1
        z1 = sigmoid(a1)
        a2 = np.dot(z1, W2) + b2
        y = softmax(a2)

        # backward
        dy = (y - t) / batch_num
        grads['W2'] = np.dot(z1.T, dy)
        grads['b2'] = np.sum(dy, axis=0)
        da1 = np.dot(dy, W2.T)
        dz1 = sigmoid_grad(a1) * da1
        grads['W1'] = np.dot(x.T, dz1)
        grads['b1'] = np.sum(dz1, axis=0)

        return grads

```


```python
net = TwoLayerNet(input_size=784, hidden_size=100, output_size=10)

net.params['W1'].shape # (784, 100)
net.params['b1'].shape # (100,)
net.params['W2'].shape # (100, 10)
net.params['b2'].shape # (10,)
```

>- input_size 가 784 인 이유는 이미지 크기가 28 x 28
>- output_size 가 10인 이유는 출력이 10개 (0부터 9)
>- hidden_size 는 은닉층이고 적당한 값을 설정하면 됨
>	- 가중치 매개변수의 초깃값을 무엇으로 설정하느냐에 따라 신경망 학습의 성공을 좌우 한다고 함
>	- 편향은 0으로 초기화
>- predict() 은 정답 레이블을 바탕으로 교차 엔트로피 오차 구현
>- numerical_gradient(self, x, t) 는 각 매개변수의 기울기 계산 (계산 오래 걸림)
>- gradient(self, x, t)는 오차역전파법을 사용해 기울기를 계산
>	- ***numerical_gradient(self, x, t)는 수치 미분 방식으로 매개변수의 기울기를 계산***
>	- ***오차역 전파법***을 쓰면 수치 미분을 사용할 때와 ***거의 같은 결과를 훨씬 빠르게*** 얻을 수 있다.
>	- 신경망 학습은 시간이 오래 걸리니, 시간을 절약하려면 numerical_gradient(self, x, t) 대신 gradient(self, x. t)를 쓰는 것이 좋다. (연습할 때)
>	- 오차역전법에 대한 자세한 이야기는 다음장에서 ㄱ

### 예측처리

```python
x = np.random.rand(100, 784) # 더미 입력 데이터 (100장 분량)
y = net.predict(x)
```
>- x = 100행 784열

```python
x = np.random.rand(100, 784) # 더미 입력 데이터(100장 분량)
t = np.random.rand(100, 10) # 더이 정답 레이블(100장 분량)

grads = net.numerical_gradient(x, t) # 기울기 계산
grads['W1'].shape # (784, 100)
grads['b1'].shape # (100,)
grads['W2'].shape # (100, 10)
grads['b2'].shape # (10,)
```



## 미니배치 학습 구현하기

- 이전과 달리 훈련 데이터 중 일부를 무작위로 꺼내고
- 그 미니배치에 대해서 경사법으로 매개변수를 갱신

```python
# coding: utf-8

import sys, os
sys.path.append(os.pardir)  # 부모 디렉터리의 파일을 가져올 수 있도록 설정
from common.functions import *
from common.gradient import numerical_gradient
from dataset.mnist import load_mnist
from Chap_4_5_1_2층신경망클래스_구현하기 import TwoLayerNet
import pickle

# 미니배치 학습 구현하기

(x_train, t_train), (x_test, t_test) = \
load_mnist(normalize=True, one_hot_label=True)
train_loss_list = []

# 하이퍼파라미터

iters_num = 10000 # 반복 횟수
train_size = x_train.shape[0] # <- 훈련 이미지 60,000개
# shape[0] 은 행의 개수
# shape[1] 은 열의 개수

batch_size = 100 # 미니배치 크기
learning_rate = 0.1
network = TwoLayerNet(input_size=784, hidden_size=50, output_size=10)


for i in range(iters_num):
    # 미니배치 획득
    batch_mask = np.random.choice(train_size, batch_size) #<- 중복허용
    x_batch = x_train[batch_mask]
    t_batch = t_train[batch_mask]

    # 기울기 계산
    grad = network.numerical_gradient(x_batch, t_batch)
    # grad = network.gradient(x_batch, t_batch) # 성능 개선판!
    
    # 매개변수 갱신
    for key in ('W1', 'b1', 'W2', 'b2'):
        network.params[key] -= learning_rate * grad[key]

    # 학습 경과 기록
    loss = network.loss(x_batch, t_batch)
    train_loss_list.append(loss)
```

>- x_train.shape (60000, 784)
>	- 훈련이미지가 60,000 개 / 이미지 크기는 28 x 28 = 784
>- batch_mask = train_size (60,000) 수 에서 (batch_size)100개의 무작위 숫자(인덱스) 추출
>- x batch는 batch_mask 를 인덱스로 해서 원래 있던 60,000 개의 데이터중 무작위 100개 추출
>- 무작위의 데이터를 numerical_gradient에 대입
>	- 이전에 예측값 함수 predict에서 연속된 수(시그모이드) (입력이 작을 때 출력이 0, 입력이 클 때 출력이  1) 로 바꾼 후 소프트 맥스 함수를 이용해서 다시 확률로 계산
>	- 해당 확률을 크로스 엔트로피 오차를 이용해서 정답 테이블과 훈련 테이블의 오차 계산
>		- 정답에 가까울 수록 린턴 값은  0에 가까움
>	- 구해온 해당 오차 loss_W 와 이전에 초기화 값 self.params 와의 기울기 (미분 값)을 grads 딕셔너리에 저장
>	- 해당 grads의 키 값 과 이전에 저장해둔 learning_rate -0.01 값을 곱해 같은 키 값의 network.params의 값을 갱신 시켜줌
>- 해당 과정을 통해 손실 함수의 값이 서서히 줄어듬
>	- 손실 함수 값이 작아진다는 것은 신경망 학습이 잘 되고 있다는 뜻


## 시험 데이터로 평가하기

> - 1에폭은 학습에서 훈련 데이터를 모두 소진했을 때의 횟수에 해당
> 	- 훈련 데이터 10,000개를 100개의 미니배치로 학습할 경우
> 	- 확률적 경사 하강법을 100회 반복하면 모든 훈련 데이터를 소진하게 됨
> 		- 이 경우 100회가 1 에폭

```python
# coding: utf-8

import sys, os
sys.path.append(os.pardir)  # 부모 디렉터리의 파일을 가져올 수 있도록 설정
import numpy as np
import matplotlib.pyplot as plt
from dataset.mnist import load_mnist
from two_layer_net import TwoLayerNet

# 데이터 읽기
(x_train, t_train), (x_test, t_test) = load_mnist(normalize=True, one_hot_label=True)
network = TwoLayerNet(input_size=784, hidden_size=50, output_size=10)

# 하이퍼파라미터
iters_num = 10000  # 반복 횟수를 적절히 설정한다.
train_size = x_train.shape[0]
batch_size = 100   # 미니배치 크기
learning_rate = 0.1
train_loss_list = []
train_acc_list = []
test_acc_list = []

# 1에폭당 반복 수

iter_per_epoch = max(train_size / batch_size, 1)
for i in range(iters_num):

    # 미니배치 획득
    batch_mask = np.random.choice(train_size, batch_size)
    x_batch = x_train[batch_mask]
    t_batch = t_train[batch_mask]

    # 기울기 계산
    #grad = network.numerical_gradient(x_batch, t_batch)
    grad = network.gradient(x_batch, t_batch)
    
    # 매개변수 갱신
    for key in ('W1', 'b1', 'W2', 'b2'):
        network.params[key] -= learning_rate * grad[key]

    # 학습 경과 기록
    loss = network.loss(x_batch, t_batch)
    train_loss_list.append(loss)

    # 1에폭당 정확도 계산
    if i % iter_per_epoch == 0:
        train_acc = network.accuracy(x_train, t_train)
        test_acc = network.accuracy(x_test, t_test)
        train_acc_list.append(train_acc)
        test_acc_list.append(test_acc)
        print("train acc, test acc | " + str(train_acc) + ", " + str(test_acc))

# 그래프 그리기

markers = {'train': 'o', 'test': 's'}
x = np.arange(len(train_acc_list))
plt.plot(x, train_acc_list, label='train acc')
plt.plot(x, test_acc_list, label='test acc', linestyle='--')
plt.xlabel("epochs")
plt.ylabel("accuracy")
plt.ylim(0, 1.0)
plt.legend(loc='lower right')
plt.show()
```

>- accuracy 에서는 예를 들어 `[0.1, 0.5, 0.3, ... 0.04]` 같은 배열이 반환될 때 
>	- (이미지 숫자가 '0' 일 확률이 0.1, '1' 일 확률이 0.5 .... 식으로 해석)
>- np.argmax() 함수로 배열에서 가장 큰 값 (확률이 가장 높은) 원소의 인덱스를 구한다.
>- 이를 전체 이미지 숫자로 나눠 정확도를 측정

### 학습 결과 그래프

- ![](https://i.imgur.com/hav5e0O.png)
- ![](https://i.imgur.com/uh5ahls.png)




출처 : 밑바닥부터 시작하는 딥러닝