---
layout: single
title: " [딥러닝 기초] 오버피팅 "
categories: ML_DL
tag: [Python,"[밑딥] 오버피팅"]
toc: true
toc_sticky: true
author_profile: false

---
# 오버피팅이란

- 오버피팅이란 신경망이 훈련 데이터에만 지나치게 적응되서 그 외에 데이터에는 제대로 대응하지 못하는 상태를 말한다.
- 기계학습은 범용 성능을 지향한다. -> 한 가지 데이터에만 국한 X
- 훈련 데이터에는 포함되지 않고, 아직 보지 못한 데이터가 주어져도 바르게 식별해내는 모델이 바람직하다.
- 복잡하고 표현력이 높은 모델을 만들 수 있지만 그만큼 오버피팅을 억제하는 기술이 중요하다.

## 오버피팅이 일어나는 경우
- 매개변수가 많고 표현력이 높은 모델
- 훈련 데이터가 적을 때

> 오버피팅을 인위적으로 만들기 위해 60,000개인 MNIST 데이터셋의 훈련 데이터중 300개만 사용하고, 7층 네트워크를 사용해서 네트워크의 복잡성을 높이고 각 층의 뉴런은 100개, 활성화 함수는 ReLU 를 사용한다.

## 코드구현

```python
# coding: utf-8
import os
import sys
sys.path.append(os.pardir) 
import numpy as np
import matplotlib.pyplot as plt
from dataset.mnist import load_mnist
from common.multi_layer_net import MultiLayerNet
from common.optimizer import SGD

(x_train, t_train), (x_test, t_test) = load_mnist(normalize=True)

# 오버피팅을 재현하기 위해 학습 데이터 수를 줄임
x_train = x_train[:300]
t_train = t_train[:300]

# weight decay（가중치 감쇠） 설정 =======================
#weight_decay_lambda = 0 # weight decay를 사용하지 않을 경우
weight_decay_lambda = 0.1

# ====================================================
network = MultiLayerNet(
						input_size=784, 
						hidden_size_list=[100, 100, 100, 100, 100, 100],
						output_size=10,
						weight_decay_lambda=weight_decay_lambda)
						
optimizer = SGD(lr=0.01) # 학습률이 0.01인 SGD로 매개변수 갱신
max_epochs = 201
train_size = x_train.shape[0]
batch_size = 100

train_loss_list = []
train_acc_list = []
test_acc_list = []

iter_per_epoch = max(train_size / batch_size, 1)
epoch_cnt = 0

for i in range(1000000000):
    batch_mask = np.random.choice(train_size, batch_size)
    x_batch = x_train[batch_mask]
    t_batch = t_train[batch_mask]
    grads = network.gradient(x_batch, t_batch)
    optimizer.update(network.params, grads)

    if i % iter_per_epoch == 0:
        train_acc = network.accuracy(x_train, t_train)
        test_acc = network.accuracy(x_test, t_test)
        train_acc_list.append(train_acc)
        test_acc_list.append(test_acc)

        print("epoch:" + str(epoch_cnt) + ", train acc:" + str(train_acc) + ", test acc:" + str(test_acc))

        epoch_cnt += 1
        if epoch_cnt >= max_epochs:
            break
```

![](https://i.imgur.com/t061YlL.png)


>- 훈련 데이터를 사용하여 측정한 정확도는 100에폭을 지나는 무렵부터 거의 100%다
>- 그러나 시험 데이터에 대해서는 큰 차이가 보인다.
>- 이처럼 정확도가 크게 벌어지는 것은 훈련 데이터에만 적응해버린 결과다.
>- 훈련 때 사용하지 않은 범용 데이터(시험 데이터)에는 제대로 대응하지 못하는 것을 그래프에서 확인할 수 있다.

## 가중치 감소

- 오버피팅 억제용으로 예로부터 많이 이용해온 방법 중 ***가중치 감소*** 라는 것이 있다.
- 이는 학습 과정에서 큰 가중치에 대해서는 그에 상응하는 큰 패널티를 부과해 오버피팅을 억제하는 방법이다.
- 오버피팅은 가중치 매개변수의 값이 커서 발생하는 경우가 많기 때문에 이 방법을 쓴다.
- 예를 들어 가중치의 제곱 노름(L2 노름)을 손실 함수에 더한다.
- 그러면 가중치가 커지는 것을 억제할 수 있다.
- 가중치를 W라 하면 L2 노름에 따른 가중치 감소는 ![](https://i.imgur.com/ZJrFqBr.png)
 이 되고, 이 ![](https://i.imgur.com/BUuCdUX.png)
 을 손실 함수에 더한다. 여기에서 ![](https://i.imgur.com/Bos0J61.png)
람다는 정규화의 세기를 조절하는 하이퍼파라미터다. 람다를 크게 설정할수록 큰 가중치에 대한 패널티가 커진다.
- 또 ![](https://i.imgur.com/pBSJRzw.png)
의 앞쪽 1/2 는 ![](https://i.imgur.com/l5RbZrj.png)
의 미분 결과인 ![](https://i.imgur.com/RYMOM31.png)
 를 조정하는 역할의 상수다.
- 가중치 감소는 모든 가중치 각각의 손실 함수에 ![](https://i.imgur.com/CiUNfh8.png)
 를 더한다.
- 따라서 가중치의 기울기를 구하느 계산에서 그동안의 오차역전파법에 따른 결과에 정규화 항을 미분한 ![](https://i.imgur.com/SbcdMUG.png) 를 더한다.

 > - L2 노름은 각 원소의 제곱들을 더한 것에 해당된다. 가중치 W = (w1, w2,.... wn) 이 있다면 L2 노름에서는 ![](https://i.imgur.com/zcKnz3J.png) 으로 계산할 수 있다.
 > - L2 노름 외에도 L1 노름과 상한 노름 도 있다.
 > - 상한 노름은 MAX 노름이라고도 하며, 각 원소의 절댓값 중 가장 큰 것에 해당된다.
 > - 정규화 항으로 L2 노름, L1 노름, 상한 노름 중 어떤 것도 사용 가능하다.
 > - 각자의 특징이 있는데 보통 일반적으로 L2 노름으로 구현한다.
 > - [노름](https://namu.wiki/w/%EB%85%B8%EB%A6%84(%EC%88%98%ED%95%99))

똑같은 코드에서 람다 를 0.1 로 가중치 감소를 적용한다면 밑에 그림과 같다.
 
![](https://i.imgur.com/7z0ox05.png)

> 이전과 정확도에는 여전히 차이가 있지만 가중치 감소를 이용하지 않은 이전과 비교하면 그 차이가 줄었다.
> 오버피팅이 억제 됬다는 의미다.
> 그리고 앞서와 달리 훈련 데이터에 대한 정확도가 100% (1.0)에= 도달하지 못한 점도 주목해야한다.

## 드롭아웃

앞 절에서 오버피팅을 억제하는 방식으로 손실 함수에 가중치의 L2 노름을 더한 가중치 감소 방법을 설명했따.
가중치 감소는 간단하게 구현할 수 있고 어느 정도 지나친 학습을 억제할 수 있다.
그러나 신경망 모델이 복잡해지면 가중치 감소만으로는 대응하기 어려워 진다.
이럴 때 흔히 ***드롭아웃*** 이라는 기법을 이용한다.

- 드롭아웃은 뉴런을 임의로 삭제하면서 학습하는 방법이다.
- 훈련 떄 은닉층의 뉴런을 무작위로 골라 삭제한다.
- 삭제된 뉴런은 (b) 와 같이 신호를 전달하지 못한다.
- 훈련 때는 데이터를 흘릴 때마다 삭제할 뉴런을 무작위로 선택하고, 시험 떄는 모든 뉴런에 신호를 전달한다.
- 단 시험 때는 각 뉴런의 출력에 훈련 때 삭제 안 한 비율을 곱해 출력한다.

![](https://i.imgur.com/RXCITMA.png)

### 드롭아웃 코드 구현
- 책에서 이해가 되기 쉽도록 코드로 구현되어있다.
- 순전파를 담당하는 forward 메서드에서는 훈련 때 (train_flg = True 일 때) 만 잘 계산해두면 시험 때는 단순히 데이터를 흘리기만 하면 된다.
- 삭제 안한 비율은 곱하지 않아도 된다.
- 실제 딥러닝 프레임워크들도 비율을 곱하지 않는다고 한다.
- 더 효율적인 구현은  [체이너 프레임워크](https://chainer.org/) 의 드롭아웃 구현을 참고

```python
class Dropout: 
	def __init__(self, dropout_ratio=0.5):
		self.dropout_ratio = dropout_ratio 
		self.mask = None
	def forward(self, x, train_flg=True):
		if train_flg:
			self.mask = np.random.rand(*x.shape) > self.dropout_ratio
			return x * self.mask
		else:
			return x * (1.0 - self.dropout_ratio)
	def backward(self, dout):
		return dout * self.mask

```

>- 훈련시 순전파 때마다 self.mask에 삭제할 뉴런을 False로 표시한다는 점이다.
>- self.mask는 x와 형상이 같은 배열을 무작위로 생성하고, 그 값이 dropout_ratio 보다 큰 원소만 True 로 설정한다.
>- 역전파 때의 동작은 ReLU와 같다.
>- 즉, 순전파 때 신호를 통과시키는 뉴런은 역전파 때도 신호를 그대로 통과시키고, 순전파 때 통과시키지 않은 뉴런은 역전파 때도 신호를 차단한다.

#### 드롭아웃이 없을 때
![](https://i.imgur.com/Iz1ljsR.png)


#### 드롭아웃을 적용 했을 때 (dropout_ratio = 0.2)

![](https://i.imgur.com/OHX5ubd.png)

> 기계학습에서는 **앙상블 학습** 을 애용한다.
> 앙상블 학습은 개별적으로 학습시킨 여러모델의 출력을 평균 내어 추론하는 방식이다.
> 신경망의 맥락에서 얘기하면, 가령 같은(혹은 비슷한) 구조의 네트워크를 5개 준비하여 따로따로 학습시키고, 시험 떄는 그 5개의 출력을 평균 내어 답한다.
> 앙상블 학습을 수행하면 신경망의 정확도가 몇 % 정도 개선된다는 것이 실험적으로 알려져 있다.


> 앙상블 학습은 드롭아웃과 밀접하다.
> 드롭아웃이 학습 때 뉴런을 무작위로 삭제하는 행위를 매번 다른 모델을 학습시키는 것으로 해석할 수도 있기 떄문이다.
> 그리고 추론 때는 뉴런의 출력에 삭제한 비율 (이를테면 0.5 등) 을 곱함으로써 앙상블 학습에서 여러 모델의 평균을 내는 것과 같은 효과를 얻는다.
> 즉 드롭아웃은 앙상블 학습과 같은 효과를 (대략) 하나의 네트워크로 구현했다고 생각할 수 있다.

