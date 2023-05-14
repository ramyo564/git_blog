---
layout: single
title: " Batch Normalization "
categories: ML_DL
tag: [Python,"[BIG][밑딥] 배치정규화"]
toc: true
toc_sticky: true
author_profile: false

---

# 배치 정규화

- 가중치의 초깃값을 적절히 설정하면 각층의 활성화 값 분포가 적당히 퍼지면서 학습이 원활하게 수행된다.
- 이러한 각 층의 활성화를 적당히 퍼뜨리도록 강제하는걸 **배치 정규화**라고 한다.

## 배치 정규화 알고리즘

>- 학습을 빨리 진행할 수 있다(학습 속도 개선)
>- 초깃값에 크게 의존하지 않는다 (골치 아픈 초깃값 선택 장애를 할 필요가 없음)
>- 오버피팅을 억제한다. (드롭아웃 등의 필요성 감소)


- 배치 정규화의 기본 아이디어는 각 층에서 활성화 값이 적당이 분포되도록 조정하는 것이다.

![](https://i.imgur.com/zr7j3mv.png)

- 학습을 할 때 미니배치 단위로 정규화 하며 구체적으로는 데이터 분포가 평균이 0, 분산이 1이 되도록 정규화 한다.
- <iframe width="1280" height="720" src="https://www.youtube.com/embed/3rSecBOH_EQ" title="분산의 정의 알아보기" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
- ![](https://i.imgur.com/U1hatRV.png)
- 여기에서 미니배치 B = `{x1, x2, ...xm}` 이라는 m개의 입력 데이터의 집합에 대해 평균 ![](https://i.imgur.com/1cDo7f3.png) 과 분산 ![](https://i.imgur.com/4LhuEEM.png) 을 구한다.
- 그리고 입력 데이터를 평균이 0, 분산이 1이 되게 (적절한 분포가 되게) 정규화한다.
- 그리고 위에 식에서 ![](https://i.imgur.com/KzZMrlq.png) (엘십론)는 작은값 (10e-7) 으로 , 0으로 나누는 사태를 예방하는 역할이다.
- 위 식은 미니배치 입력 데이터 {x1,x2,.... xm} 을 평균 0, 분산 1인 데이터 `{X1,X2, ...Xm }` 으로 변환하는 일을 한다.
- 이 처리를 활성화 함수의 앞 (혹은 뒤) 에 삽입함으로써 데이터 분포가 덜 치우치게 할 수 있다.
- 또 배치 정규화 계층마다 이 정규화된 데이터에 고유한 확대와 이동 변환을 수행한다.

수식은 아래와 같다.

![](https://i.imgur.com/QQW5QFa.png)

이 식에서 ![](https://i.imgur.com/CVYJJ4W.png) 가 확대를 ![](https://i.imgur.com/Tj7JrJw.png) 가 이동을 담당한다.
두 값은 처음에는 ![](https://i.imgur.com/CVYJJ4W.png) = 1 ![](https://i.imgur.com/Tj7JrJw.png) = 0 부터 시작하고 학습하면서 적합한 값으로 조정된다.
이 알고리즘은 신경망에서 순전파 때 적용된다.
계산 그래프로 나타내면 아래와 같다.

![](https://i.imgur.com/LpWWeYd.png)

### 역전파 유도
[역전파 유도 포스트](https://kratzert.github.io/2016/02/12/understanding-the-gradient-flow-through-the-batch-normalization-layer.html)

## 배치 정규화의 효과
- MNIST 데이터셋으로 배치 정규화 계층을 사용할 떄와 사용하지 않을 떄의 학습 진도가 어떻게 달라지는지 알 수 있다.

![](https://i.imgur.com/Srb4WAE.png)

> - 노란점선 -> 배치정규화 x 
> - 파란선 -> 배치정규화 o

>- 배치정규화를 사용하지 않았을 경우 초깃값 분포가 잘 되지 않아 학습이 전혀 진행되지 않는 모습이 많이 있다는걸 확인할 수 있다.
>- 정규화를 사용하면 가중치 초깃값에 크게 의존하지 않아도 된다.

### 코드 구현
```python
class BatchNormalization:

    """
    http://arxiv.org/abs/1502.03167
    """

    def __init__(self, gamma, beta, momentum=0.9, running_mean=None, running_var=None):
        self.gamma = gamma
        self.beta = beta
        self.momentum = momentum
        self.input_shape = None # 합성곱 계층은 4차원, 완전연결 계층은 2차원  

        # 시험할 때 사용할 평균과 분산
        self.running_mean = running_mean
        self.running_var = running_var  

        # backward 시에 사용할 중간 데이터
        self.batch_size = None
        self.xc = None
        self.std = None
        self.dgamma = None
        self.dbeta = None

    def forward(self, x, train_flg=True):
        self.input_shape = x.shape
        if x.ndim != 2:
            N, C, H, W = x.shape
            x = x.reshape(N, -1)
        out = self.__forward(x, train_flg)
        
        return out.reshape(*self.input_shape)

    def __forward(self, x, train_flg):
        if self.running_mean is None:
            N, D = x.shape
            self.running_mean = np.zeros(D)
            self.running_var = np.zeros(D)

        if train_flg:
            mu = x.mean(axis=0)
            xc = x - mu
            var = np.mean(xc**2, axis=0)
            std = np.sqrt(var + 10e-7)
            xn = xc / std
            self.batch_size = x.shape[0]
            self.xc = xc
            self.xn = xn
            self.std = std
            self.running_mean = self.momentum * self.running_mean + (1-self.momentum) * mu
            self.running_var = self.momentum * self.running_var + (1-self.momentum) * var            

        else:
            xc = x - self.running_mean
            xn = xc / ((np.sqrt(self.running_var + 10e-7)))
        out = self.gamma * xn + self.beta
        return out

    def backward(self, dout):
        if dout.ndim != 2:
            N, C, H, W = dout.shape
            dout = dout.reshape(N, -1)
        dx = self.__backward(dout)
        dx = dx.reshape(*self.input_shape)

        return dx

    def __backward(self, dout):
        dbeta = dout.sum(axis=0)
        dgamma = np.sum(self.xn * dout, axis=0)
        dxn = self.gamma * dout
        dxc = dxn / self.std
        dstd = -np.sum((dxn * self.xc) / (self.std * self.std), axis=0)
        dvar = 0.5 * dstd / self.std
        dxc += (2.0 / self.batch_size) * self.xc * dvar
        dmu = np.sum(dxc, axis=0)
        dx = dxc - dmu / self.batch_size
        self.dgamma = dgamma
        self.dbeta = dbeta

        return dx
        
```





출처 : 밑바닥부터 시작하는 딥러닝
