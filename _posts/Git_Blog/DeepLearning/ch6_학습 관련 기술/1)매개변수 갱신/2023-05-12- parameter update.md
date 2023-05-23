---
layout: single
title: " [딥러닝 기초] 메개변수 갱신 "
categories: ML_DL
tag: [Python,"[밑딥] 매개변수 갱신"]
toc: true
toc_sticky: true
author_profile: false

---
# 매개변수 갱신

- 신경망 학습의 목적은 손실 함수의 값을 가능한 낮추는 매개변수를 찾는 것 -> 매개변수의 최적값을 찾는 문제 -> 이러한 문제를 푸는 것을 ***최적화*** 라고 한다.
- 지금까지 최적의 매개변수 값을 찾는 단서로 매개변수의 기울기(미분)을 이용했다.
- 매개변수의 기울기를 구해, 기울어진 방향으로 배개변수 값을 갱신하는 일을 몇 번이고 반복해서 점점 최적의 값에 다가갔다.
- 이것이 ***확률적 경사 하강법(SGD)*** 이란 단순한 방법이다.

## 확률적 경사 하강법(SGD)

![](https://i.imgur.com/mWU4X8V.png)

- W는 갱신할 가중치 매개변수고 ![](https://i.imgur.com/RNayWCT.png) 은 W에 대한 손실 함수의 기울기다.
- ![](https://i.imgur.com/oV2xmiq.png) 은 학습률을 의미하는데 실제로 0.01 이나 0.001 과 같은 값을 미리 정해서 사용한다.
- 또, <- 는 우변의 값으로 좌변의 값을 갱신한다는 뜻이다.
	- 기울어진 방향으로 일정 거리만 가겠다는 단순한 방법

### 코드 구현
```python
class SGD: 
	def __init__(self, lr=0.01):
		self,lr = Ir 
	def update(self, params, grads):
		for key in params.keys():
			params[key] -= self.lr * grads[key]
```

- 초기화 때 받는 인수인 lr은 learning rate （학습률）를 뜻한다. 
- 이 학습률을 인스턴스 변수 로 유지한다. 
- update （params, grads） 메서드는 SGD 과정에서 반복해서 호출된다. 인수인 params와 grads는 （지금까지의 신경망 구현과 마찬가지로） 딕셔너리 변수다. 
- params`['Wl']`, grads`['Wl']` 등과 같이 각각 가중치 매개변수와 기울기를 저장하고 있다. 
- SGD 클래스를 사용하면 신경망 매개변수의 진행을 다음과 같이 수행할 수 있다.

### 책에 나온 예시코드
```python
network = TwoLayerNet(...) 
optimizer = SGD() 
for i in range(10000): 
	x_batdiz t_batch = get_mini_batch(...) # 미니배치
	grads = network.gradient(x_batch, t_batch) 
	params = network.params optimizer.update(params, grads)
```

- 최적화를 담당하는 캘래스를 분리해 구현하면 기능을 모듈화 하기 좋다.
- optimizer = SGD() -> optimizer = Momentum() 으로 변경하는 식

>- 대부분의 딥러닝 프레임 워크는 다영한 최적화 기법을 구현해 제공하며 원하는 기법으로 쉽게 바꿀 수 있는 구조로 되어 있다.


### SGD의 단점

![](https://i.imgur.com/qCw1my9.png)

- 해당 함수의 최솟값을 구하는 문제에서 이 함수를 그래프와 등고선으로 나타내면 아래와 같다.

![](https://i.imgur.com/S0PV1UF.png)

![](https://i.imgur.com/3iyl6T9.png)

- 함수의 기울기를 그리면 아래와 같다.
- 기울기는 Y축 방향은 크고 X축 방향은 작다는 것이 특징이다.
- Y 축 방향은 가파른데 X 축 방향은 완만하다.
- 최솟값이 되는 장소는 (x,y) = (0,0) 하지만 밑의 그림에서 보여지는 기울기 대부분은 (0, 0) 방향을 가리키지 않는다.

![](https://i.imgur.com/AdNQtEj.png)

밑에서 까만 선은 함수의 등고선을 표현한 것이고 빨간 점은 최솟값을 찾아 나가는 경로를 나타낸 것

![](https://i.imgur.com/zYU7FPY.png)

- 이 그래프는 50번 갱신했는데 그 이하로는 최솟값 (0,0)에 제대로 다가가지 못한다.

>- SGD의 단점은 비등상성 함수 (방향에 따라 성질, 즉 여기에서는 기울기가 달라지는 함수) 에서 탐색 경로가 비료율적이다.
>- 이럴 떄는 SGD 같이 무작정 기울어진 방향으로 진행하는 단순한 방식보다는 더 좋은 방법이 있다.
>	- 모멘텀, AdaGrad, Adam


## 모멘텀

![](https://i.imgur.com/OzQH1tb.png)

- W 는 갱신할 가중치 매개변수, ![](https://i.imgur.com/5diqfos.png) 는 W에 대한 손실 함수의 기울기 ![](https://i.imgur.com/oV2xmiq.png) 는 학습률이다.
- V라는 변수가 새로 나오는데 속도에 해당된다.
	- 기울기 방향으로 힘을 받아 물체가 가속된다는 개념
- ![](https://i.imgur.com/zl8AAN7.png) 항은 물체가 아무런 힘을 받지 않을 때 서서히 하강시키는 역할을 한다. (0.9 등의 값으로 설정한다고 함)
- 물리로 치면 공기나 지면 마찰에 해당.

### 코드 구현
```python
class Momentum: 
	def __init__(self, lr=0.01z momentum=0.9):
		self.lr = Ir 
		self.momentum = momentum 
		self.v = None 
	def update(self, params, grads): 
		if self.v is None: 
			self.v = {} 
			for key, val in params.items(): 
				self,v[key] = np.zeros_like(val) 
				
			for key in params.keys(): 
				self.v[key] = self.momentum*self.v[key] - self.lr*grads[key] 
				params[key] += self.v[key]
```

- 인스턴스 변수 V는 물체의 속도다.
- V는 초기화 때는 아무 값도 담지 않고, 대신 update()가 처음 호출될 때 매개변수와 같은 구조의 데이터를 딕셔너리 변수로 저장한다.
- 나머지는 위와 동일하다.

![](https://i.imgur.com/6sk0k2s.png)

>- SGD와 비교하면 지그재그 정도가 덜하다.
>- 이는 x 축의 힘은 아주 작지만 방향은 변하지 않아서 한 방향으로 일정하게 가속하기 때문이다.
>- 거꾸로 y축의 힘은 크지만 위아래로 번갈아 받아서 상충하여 y축의 방향의 속도는 안정적이지 않다.
>- 전체적으로 SGD 보다 x 축 방향으로 빠르게 다가가 지그재그 움직임이 줄어든다.

## AdaGrad

- 신경망 학습에서는 학습률 ![](https://i.imgur.com/oV2xmiq.png) 값이 중요하다. 이 값이 너무 작으면 학습 시간이 너무 길어지고, 반대로 너무 크면 발산하여 학습이 제대로 이뤄지지 않는다.
- 이 학습률을 정하는 효과적 기술로 ***학습률 감소*** 가 있다.
- 이는 학습을 진행하면서 학습률을 점차 줄여가는 방법이다.
- 처음에는 크게 학습하다가 조금씩 작게 학습한다는 얘기로, 실제 신경망 학습에서 자주 쓰인다.
- 학습률을 서서히 낮추는 가장  간단한 방법은 매개변수 '전체'의 학습률 값을 일괄적으로 낮추는 것이다.
- 이를 더욱 발전시킨 것이 AdaGrad 
- AdaGrad 는 각각의 매개변수에 맞춤형 값을 만들어준다.
	- 적응적으로 학습률을 조정하면서 학습을 진행한다.

![](https://i.imgur.com/0r79iQU.png)

- 마찬가지로 W는 갱신할 가중치 매개변수
- ![](https://i.imgur.com/5diqfos.png) 는 W에 대한 손실함수의 기울기
- ![](https://i.imgur.com/oV2xmiq.png) 는 학습률을 뜻 한다.
- 여기서 h 라는 변수는 기존 기울기 값을 제곱하여 계속 더해준다.
	- ![](https://i.imgur.com/OFhhTxe.png) 는 행렬의 원소별 곱셈을 의미
- 그리고 매개변수를 갱신할 때 ![](https://i.imgur.com/O7B3a6m.png) 을 곱해 학습률을 조정한다.
- 매개변수의 원소 중에서 많이 움직인 (크게 갱신된) 원소는 학습률이 낮아진다는 뜻인데,
- 다시 말해 학습률 감소가 매개변수의 원소마다 다르게 적용됨을 뜻한다.

>- AdaGrad는 과거의 기울기를 제곱하여 계속 더해간다. 그래서 학습을 진행할수록 갱신 강도가 약해진다 
>- 실제로 무한히 계속 학습한다면 어느 순간 갱신량이 0이 되어 전혀 갱신되지 않는다. 
>- 이 문제 를 개선한 기법으로서 RMSPropn이라는 방법이 있다. 
>- RMSProp은 과거의 모든 기울기를 균일하게 더해가는 것이 아니라, 먼 과거의 기울기는 서서히 잊고 새로운 기울기 정보를 크게 반영한다. 이를 **지수이동평균 (Exponential Moving Average. EMA** )이라 하여, 과거 기울기의 반영 규모를 기하급수적으로 감소시킨다.

### 코드구현

```python
class AdaGrad: 
	def __init__(self, lr=0.01): 
		self.lr = Ir 
		self .h = None 
		
	def update(self, params, grads): 
		if self,h is None: 
			self.h = {}
			for key, val in params.items(): 
				self,h[key] = np.zeros_like(val) 
				
		for key in params.keys(): 
			self,h[key] += grads[key] * grads[key] 
			params[key] -= self.lr * grads[key] / (np.sqrt(self.h[key]) + 1e-7)
```
- 마지막 줄에1e-7 (0.000001) 은 `self.h[key]` 에 0이 담겨 있더라도 0으로 나누는 사태를 사전에 방지해준다.
- 대부분 딥러닝 프레임 워크에서도 이 값도 인수로 설정할 수 있다고 한다.

![](https://i.imgur.com/SxHRDuk.png)

>- 그림에서 보면 최솟값을 향해 효율적으로 움직이는 것을 알 수 있다.
>- y 축 방향은 기울기가 커서 처음에는 크게 움직이지만, 그 큰 움직임에 비례해 갱신 정도도 큰 폭으로 작아지도록 조정된다.
>- 그래서 y축 방향으로 갱신 강도가 빠르게 약해지고, 지그재그 움직임이 줄어든다.

### Adam

- 위에서 소개한 모멘텀과 AdaGrad를 융합한 기법이다.
- 매개변수를 효율적으로 탐색하면서 하이퍼파라미터의 '편향 보정'이 진행된다는 점도 Adam의 특징이다.

```python
class Adam:
    """Adam (http://arxiv.org/abs/1412.6980v8)"""
    def __init__(self, lr=0.001, beta1=0.9, beta2=0.999):
        self.lr = lr
        self.beta1 = beta1
        self.beta2 = beta2
        self.iter = 0
        self.m = None
        self.v = None

    def update(self, params, grads):
        if self.m is None:
            self.m, self.v = {}, {}
            for key, val in params.items():
                self.m[key] = np.zeros_like(val)
                self.v[key] = np.zeros_like(val)

        self.iter += 1
        lr_t  = self.lr * np.sqrt(1.0 - self.beta2**self.iter) / (1.0 - self.beta1**self.iter)        
        for key in params.keys():

            #self.m[key] = self.beta1*self.m[key] + (1-self.beta1)*grads[key]
            #self.v[key] = self.beta2*self.v[key] + (1-self.beta2)*(grads[key]**2)
            self.m[key] += (1 - self.beta1) * (grads[key] - self.m[key])
            self.v[key] += (1 - self.beta2) * (grads[key]**2 - self.v[key])
            params[key] -= lr_t * self.m[key] / (np.sqrt(self.v[key]) + 1e-7)

            #unbias_m += (1 - self.beta1) * (grads[key] - self.m[key]) # correct bias
            #unbisa_b += (1 - self.beta2) * (grads[key]*grads[key] - self.v[key]) # correct bias
            #params[key] += self.lr * unbias_m / (np.sqrt(unbisa_b) + 1e-7)
```

논문 링크 : [논문링크](https://arxiv.org/pdf/1412.6980.pdf)

수학기호 : [수학기호](https://ko.wikipedia.org/wiki/%EC%88%98%ED%95%99_%EA%B8%B0%ED%98%B8)
E 는 무엇?

![](https://i.imgur.com/DjGbAbv.png)

>- 모멘텀과 비슷하지만 모멘텀 때보다 공의 좌우 흔들림이 적다.
>- 이는 학습의 갱신 강도를 적응적으로 조정해서 얻는 혜택이다.
>- Adam 은 하이퍼 파라미터로 3개를 설정한다.
>	- 하나는 지금까지의 학습률 (논문에서는 ![](https://i.imgur.com/nDzp6WR.png) 로 나옴)
>	- 나머지 두개는 일차 모멘텀용 계수 ![](https://i.imgur.com/lW8TMxn.png) , 이차 모멘텀용 계수 ![](https://i.imgur.com/8CrjSSM.png).
>	- 논문에 따르면 기본 설정 값은 ![](https://i.imgur.com/lW8TMxn.png) -> 0.9
>	-  ![](https://i.imgur.com/8CrjSSM.png) -> 0.999 
>	- 이 값이면 대부분의 경우에서 좋은 결과를 얻을 수 있다고 한다.

## 어느 갱신 방법을 이용해야 될까?

![](https://i.imgur.com/UwqkDsj.png)

>- 풀어야 할 문제가 무엇이냐에 따라 달라진다.
>- (학습률 등의) 하이퍼 파라미터를 어떻게 설정하느냐에 따라서도 결과가 바뀐다.
>- 각자의 장단이 있어 잘 푸는 문제와 그렇지 않은 문제가 있다.

## 데이터셋으로 본 갱신 방법 비교

![](https://i.imgur.com/wDI65Pm.png)

>- loss 는 손실함수의 값 iterations는 학습의 반복 횟수
>- 각 층이 100개인 뉴런으로 구성된 5층 신경망에서 ReLU를 활성화 함수로 사용해 측정한 결과다.
>- 학습 결과에서는 SGD의 학습 진도가 가장 느리다.
>- 여기서 주의할 점은 하이퍼파라미터인 학습률과 신경망의 구조 (층 깊이 등)에 따라 결과가 달라진다는 점이다.
>- 다만 일반적으로 SGD 보다 다른 세 기법이 빠르게 학습하고, 때로는 최종 정확도도 높게 나타난다.

### 손실함수 개념 복습

>- 딥러닝에서 모델을 학습할 때 손실 함수를 사용해서 모델의 성능을 평가한다.
>- 손실 함수는 모델의 출력과 실제 값 사이의 차이를 계산하는 함수며 이 차이가 작을수록 모델의 예측이 실제 값과 일치한다는 것을 의미한다.
>- 따라서 손실 함수의 값이 낮을수록 모델의 성능이 높아진다고 할 수 있다. 
>- 이는 모델이 더 정확하게 예측하고 있기 때문 
>- 예를 들어, 분류 문제에서는 정확도가 높을수록 모델이 데이터를 더 정확하게 분류한다는 것을 의미
>- 회귀 문제에서는 평균 제곱 오차(MSE)와 같은 손실 함수의 값이 작을수록 모델이 실제 값과 더 가까워진다는 것을 의미
>- 따라서 딥러닝에서는 손실 함수의 값을 최소화하는 방향으로 모델을 학습시키는 것이 중요하다. 
>- 최적화 알고리즘을 사용하여 모델의 파라미터를 업데이트하고 손실 함수의 값을 줄이는 방향으로 모델을 학습해야한다.

>- 하이퍼 파라미터와 문제에 따라 결국 결과 값이 달라지므로 사람의 손을 타긴 탄다.
>	- 머신러닝보다 좀 덜 탈뿐이다.


출처 : 밑바닥부터 시작하는 딥러닝