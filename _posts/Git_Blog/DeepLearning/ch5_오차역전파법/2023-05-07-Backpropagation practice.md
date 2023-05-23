---
layout: single
title: " [딥러닝 기초] 오차역전파법 구현하기!"
categories: ML_DL
tag: [Python,"[밑딥] 오차역전파법 구현하기"]
toc: true
toc_sticky: true
author_profile: false

---

# 오차역전파법 구현하기!


## 신경망 학습의 전체 그림
- 전제 
	- 신경망에는 적응 가능한 가중치와 편향이 있고, 이 가중치와 편향을 훈련 데이터에 적응하도록 조정하는 과정 을 ‘학습’이라 한다. 
	- 신경망 학습은 다음과 같이 4단계로 수행
- 1 단계- 미니배치 
	- 훈련 데이터 중 일부를 무작위로 가져온다. 
	- 이렇게 선별한 데이터를 미니배치라 하며, 그 미니배치의 손실 함수 값을 줄이는 것이 목표
- 2단계-기울기 산출 
	- 미니배치의 손실 함수 값을 줄이기 위해 각 가중치 매개변수의 기울기를 구한다. 
	- 기울기는 손실 함수의 값을 가장 작게 하는 방향을 제시한다. 
- 3단계-매개변수 갱신 
	- 가중치 매개변수를 기울기 방향으로 아주 조금 갱신 
- 4단계 - 반복 
	- 1 ~3단계를 반복

>- 지금까지는 오차역전파법에서 두 번째인 `기울기 산출` 이다.
>- 앞에서는 이 기울기를 구하기 위해 수치 미분을 사용했다.
>- 그런데 수치 미분은 구현하기는 쉽지만 계산이 오래 걸렸다.
>- 오차 역전파법을 이용하면 느릴 수치 미분과 달리 기울기를 효율적이고 빠르게 구현 가능하다.


## 오차역전파법을 적용한 신경망 구현하기
- 앞에서 한 것과 크게 다른 부분은 계층을 사용한다는 점이다.
- 계층을 사용함으로써 인식 결과를 얻는 처리 (predict()) 와 기울기를 구하는 처리(gradient()) 계층의 전파만으로 동작이 이루어진다는 점이다.

```python
import sys, os
sys.path.append(os.pardir)
import numpy as np
from common.layers import *
from common.gradient import numerical_gradient
from collections import OrderedDict

class Relu:
    def __init__(self):
        self.mask = None
  
    def forward(self, x):
        self.mask = (x <= 0)
        out = x.copy()
        out[self.mask] = 0
        return out

    def backward(self, dout):
        dout[self.mask] = 0
        dx = dout
        return dx

class Affine:
    def __init__(self, W, b):
        self.W = W
        self.b = b
        self.x = None
        self.original_x_shape = None

        # 가중치와 편향 매개변수의 미분
        self.dW = None
        self.db = None
        
    def forward(self, x):
        # 텐서 대응
        self.original_x_shape = x.shape
        x = x.reshape(x.shape[0], -1)
        self.x = x
        out = np.dot(self.x, self.W) + self.b
        return out

    def backward(self, dout):
        dx = np.dot(dout, self.W.T)
        self.dW = np.dot(self.x.T, dout)
        self.db = np.sum(dout, axis=0)
        dx = dx.reshape(*self.original_x_shape)  # 입력 데이터 모양 변경(텐서 대응)
        return dx

class SoftmaxWithLoss:
    def __init__(self):
        self.loss = None # 손실함수
        self.y = None    # softmax의 출력
        self.t = None    # 정답 레이블(원-핫 인코딩 형태)

    def forward(self, x, t):
        self.t = t
        self.y = softmax(x)
        self.loss = cross_entropy_error(self.y, self.t)
        return self.loss
        
    def backward(self, dout=1):
        batch_size = self.t.shape[0]
        if self.t.size == self.y.size: # 정답 레이블이 원-핫 인코딩 형태일 때
            dx = (self.y - self.t) / batch_size
        else:
            dx = self.y.copy()
            dx[np.arange(batch_size), self.t] -= 1
            dx = dx / batch_size
        return dx
        
class TwoLayerNet:
    def __init__(self, input_size, hidden_size, output_size, weight_init_std = 0.01):
        # 가중치 초기화
        self.params = {}
        self.params['W1'] = weight_init_std * np.random.randn(input_size, hidden_size)
        self.params['b1'] = np.zeros(hidden_size)
        self.params['W2'] = weight_init_std * np.random.randn(hidden_size, output_size)
        self.params['b2'] = np.zeros(output_size)

        # 계층 생성
        self.layers = OrderedDict()
        self.layers['Affine1'] = Affine(self.params['W1'], self.params['b1'])
        self.layers['Relu1'] = Relu()
        self.layers['Affine2'] = Affine(self.params['W2'], self.params['b2'])
        self.lastLayer = SoftmaxWithLoss()

    def predict(self, x):
        for layer in self.layers.values():
            x = layer.forward(x)
        return x

    # x : 입력 데이터, t : 정답 레이블
    def loss(self, x, t):
        y = self.predict(x)
        return self.lastLayer.forward(y, t)

    def accuracy(self, x, t):
        y = self.predict(x)
        y = np.argmax(y, axis=1)
        if t.ndim != 1 : t = np.argmax(t, axis=1)
        
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

    def gradient(self, x, t):
        # forward
        self.loss(x, t)
  
        # backward
        dout = 1
        dout = self.lastLayer.backward(dout)
        layers = list(self.layers.values())
        layers.reverse()

        for layer in layers:
            dout = layer.backward(dout)
            
        # 결과 저장
        grads = {}
        grads['W1'], grads['b1'] = self.layers['Affine1'].dW, self.layers['Affine1'].db
        grads['W2'], grads['b2'] = self.layers['Affine2'].dW, self.layers['Affine2'].db

        return grads
```

>- 파이썬 3.6 이전에는 데이터가 삽입된 순서대로 데이터를 획득할 수 없었다.
>- 이 때문에 데이터가 무작위의 순서를 가졌지만 `OrderedDict` 클래스를 사용하면 데이터의 순서를 보장 받을 수 있다.
>	- 참고 : https://www.daleseo.com/python-collections-ordered-dict/
>- 이를 통해 신경망 계층에서 역전파를 구현할 때 계층 단계별 계산을 정확하게 할 수 있다.
>	- 이전에 numerical_gradient()를 이용할 때는 수치미분으로 구하기 때문에 계층 순서를 구분할 필요가 없었지만 gradient() 에서는 `layers['Affine1']`, `layers['Affine2']` 로 각 계층을 순서대로 유지 할 수 있다.
>	- 이에 따라 순전파 때는 추가한 순서대로 각 계층의 순전파 매서드 forward() 가 처리되면 역전파 때 계층을 반대 순서로 호출하면된다.
>	- 올바른 순서대로 연결하고 역순으로 호출해주면된다.
>		- 이에 따라 계층을 모듈화 할 수 있는데 층수가 많아질 수록 깊은 신경망이 된다
>- numerical_gradient() 를 통해 계산 실수가 없는지 검증한다.


## 오차역전파법으로 구한 기울기 검증하기

- 수치미분보다 오차역전파법을 이용하면 매개변수가 많아도 효율적으로 계산할 수 있다.
- 하지만 수치미분은 오차역전파법을 정확히 구현했는지 확인하기 위해서 필요하다.
>- 수치미분은 구현하기 쉬워 버그가 숨어있기 어렵지만 오차 역전파법은 구현하기 복잡해서 실수하기 쉽다
>- 오차역전파법으로 빠르게 구한뒤 마지막에 검사하는 용도로 쓰면 된다.

### 코드 구현
```python
import sys, os
sys.path.append(os.pardir)  # 부모 디렉터리의 파일을 가져올 수 있도록 설정
import numpy as np
from dataset.mnist import load_mnist
from Chap_5_7_2_오차역전파법을_적용한_신경망_구현하기 import TwoLayerNet

# 데이터 읽기

(x_train, t_train), (x_test, t_test) = load_mnist(normalize=True, one_hot_label=True)
network = TwoLayerNet(input_size=784, hidden_size=50, output_size=10)

x_batch = x_train[:3]
t_batch = t_train[:3]

grad_numerical = network.numerical_gradient(x_batch, t_batch)
grad_backprop = network.gradient(x_batch, t_batch)

# 각 가중치의 절대 오차의 평균을 구한다.

for key in grad_numerical.keys():
    diff = np.average( np.abs(grad_backprop[key] - grad_numerical[key]) )
    print(key + ":" + str(diff))
```

>- 이런 결과가 나오는 이유는 우선  x_batch 는 2차원 array `[[1,784]]` 60,000 개중 3개만 뽑은 객체다
>	- ![](https://i.imgur.com/VNQrX1I.png)
>	- 이런식으로 28x28 이미지의 데이터를 1행 784열 array로 바꾼 값이다.
>	- `network.numerical_gradient(x_batch, t_batch)` 는 이전 포스팅에서 설명한 로직과 동일하게 돌아간다.
>	- `network.gradient(x_batch, t_batch)`는 우선 순전파의 결과를 마지막 레이어에 저장되는데  `SoftmaxWithLoss`의 계층이 이용된다.
>		- 여기서 y값은 softmax의 확률로 변환 되고 정답 레이블은 원 핫 인코딩이되어 이것을 손실함수  `cross_entropy_error` 를 거쳐 리턴한다.
>		- 이 때 정답 레이블이 원-핫 인코딩이므로 `(self.y - self.t) / batch_size` 로 리턴된 값을 객체 dout에 넣는다.
>		- 이 때 layer를 딕셔너리 키 값 Affine1, Relu1, Affine2 으로 저장해둔 벨류 값을 순서대로 리스트 형태로 변환한 뒤 reverse()를 통해 거꾸로 돌려준다.
>		- 거꾸로 돌려준 밸류 값들에 아까 구한값을 객체 dout에 넣어준다.
>		- 그렇게 구한 값을 다시 키값 w1,b1,w2,b2 에 차례대로 객체 grad_backprop 에 넣어준다.
>	- 이제 grad_numerical 값과 grad_backprop 값을 이용해서 평균을 구해주면 아래 값이 나온다.
>		- ![](https://i.imgur.com/ZYkIQRA.png)
>			- 1.96E-06 는 0.0000019551972239753 이며 이 결과는 수치 미분과 오차역전파법으로 구한 기울기의 차이가 매우 작은걸 알 수 있다.

- 수치 미분과 오차역전파법의 결과 오차가 0이 되는 일은 드물다. 이는 컴퓨터가 할 수 있는 계산의 정밀도가 유한하기 때문이다. (32비트 부동소수점)
- 이 오차의 값이 크면 잘못 구현했다고 보면 된다.

## 오차역전파법을 사용한 학습 구현하기

- 이전과 차이점은 기울기를 오차역전파법으로 구현한다는 점이다.

### 코드구현
```python
import sys, os
sys.path.append(os.pardir)
import numpy as np
from dataset.mnist import load_mnist
from Chap_5_7_2_오차역전파법을_적용한_신경망_구현하기 import TwoLayerNet

# 데이터 읽기
(x_train, t_train), (x_test, t_test) = load_mnist(normalize=True, one_hot_label=True)
network = TwoLayerNet(input_size=784, hidden_size=50, output_size=10)

iters_num = 10000
train_size = x_train.shape[0]
batch_size = 100
learning_rate = 0.1

train_loss_list = []
train_acc_list = []
test_acc_list = []

iter_per_epoch = max(train_size / batch_size, 1)

for i in range(iters_num):
    batch_mask = np.random.choice(train_size, batch_size)
    x_batch = x_train[batch_mask]
    t_batch = t_train[batch_mask]

    # 기울기 계산
    #grad = network.numerical_gradient(x_batch, t_batch) # 수치 미분 방식
    grad = network.gradient(x_batch, t_batch) # 오차역전파법 방식(훨씬 빠르다)

    # 갱신
    for key in ('W1', 'b1', 'W2', 'b2'):
        network.params[key] -= learning_rate * grad[key]
    loss = network.loss(x_batch, t_batch)
    train_loss_list.append(loss)
    
    if i % iter_per_epoch == 0:
        train_acc = network.accuracy(x_train, t_train)
        test_acc = network.accuracy(x_test, t_test)
        train_acc_list.append(train_acc)
        test_acc_list.append(test_acc)
        print(train_acc, test_acc)
```

![](https://i.imgur.com/RKQHyzd.png)

![](https://i.imgur.com/P284eqv.png)

>- 수치미분으로 구한 것과 비슷한 결과 값이지만 속도는 훨씬 빠르다.
>- 학습이 진행될수록 오차역전파법으로 진행한 훈련데이터와 시험데이터의 정확도 모두 좋아지고 있으며 두 정확도에는 차이가 거의 없다.
>	- 오버피팅이 일어나지 않은걸 알 수 있다.

## 정리

- 계산 그래 프를 이용하여 신경망의 동작과 오차역전파법을 설명하고, 그 처리 과정을 계층이라는 단위로 구현했다. 
- 예를 들어 ReLU 계층, Softmax-with-Loss 계층, Affine 계층, Softmax 계 층 등입니다. 모든 계층에서 forward와 backward라는 메서드를 구현했다. 
- 전자는 데이터 를 순방향으로 전파하고, 후자는 역방향으로 전파함으로써 가중치 매개변수의 기울기를 효율 적으로 구할 수 있었다. 
- 이처럼 동작을 계층으로 모듈화한 덕분에, 신경망의 계층을 자유롭 게 조합하여 원하는 신경망을 쉽게 만들 수 있다.

>- 계산 그래프를 이용하면 계산 과정을 시각적으로 파악할 수 있다.
>- 계산 그래프의 노드는 국소적 계산으로 구성된다. 국소적 계산을 조합해 전체 계산을 구성한다.
>- 계산 그래프의 순전파는 통상ㅈ의 계산을 수행한다. 한편 그래프의 역전파로는 각 노드의 미분을 구할 수 있다.
>- 신경망의 구성 요소를 계층으로 구현하여 기울기를 효율적으로 계산할 수 있다(오차역전파법)
>- 수치 미분과 오차역전파법의 결과를 비교하면 오차역전파법의 구현에 잘못이 없는지 확인할 수 있다. (기울기 확인)

출처 : 밑바닥부터 시작하는 딥러닝