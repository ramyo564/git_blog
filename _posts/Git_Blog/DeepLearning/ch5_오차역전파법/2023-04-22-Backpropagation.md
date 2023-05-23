---
layout: single
title: " [딥러닝 기초] 오차역전파법!"
categories: ML_DL
tag: [Python,"[밑딥] 오차역전파법!"]
toc: true
toc_sticky: true
author_profile: false

---
# 오차역전파법

- 앞에서는 신경망의 가중치 매개변수의 기울기는 수치 미분을 사용해 구했다.
	- 수치 미분에 대한 장점
		- 단순하고 구현하기 쉬움
	- 수치 미분에 대한 단점
		- 계산 시간이 오래걸림

>- 이러한 이유로 가중치 매개변수의 기울기를 효율적으로 계산하는 방법으로 ***오차역전파법*** 이 있다.
>- 오차역전파법은 단계별로 나눠 미분을 통해 기울기 값을 구한다고 이해하면 된다.

## Why backpropagation is faster than Numerical differentiation?

- Backpropagation is faster than numerical differentiation for computing gradients in neural networks because it utilizes the ***chain rule*** of differentiation to propagate errors backward from the output layer to the input layer, instead of estimating the gradients numerically using finite differences.

- ***Computing gradients numerically involves calculating the function at least two times for each input dimension***, one for a small positive perturbation and one for a small negative perturbation, and then computing the difference between them divided by the perturbation size. ***This approach is computationally expensive because it requires a large number of function evaluations, especially when dealing with high-dimensional inputs, such as images or text.***

- In contrast, backpropagation calculates gradients in a much more efficient way ***by reusing the computations done during the forward pass of the neural network to calculate the gradients of the loss function with respect to each parameter in the network.*** By exploiting the structure of the network, backpropagation can efficiently compute gradients for all the parameters in a single pass, which makes it much faster than numerical differentiation, especially for deep neural networks with many layers and parameters.

>In summary, backpropagation is faster than numerical differentiation because **it leverages the structure of the neural network and the chain rule of differentiation to efficiently compute gradients with respect to all the parameters in the network in a single pass.**


## 연쇄법칙 Chain rule

![](https://i.imgur.com/5Jk6m2y.png)
- 역전파는 순방향과는 반대 방향으로 국소적 미분을 곱한다.
- 연쇄법칙은 함성 함수의 미분에 대한 성질이며 함성 함수의 미분은 합성 함수를 구성하는 각 함수의 미분의 곱으로 나타낼 수 있다.
- ![](https://i.imgur.com/Kevglly.png)
- ![](https://i.imgur.com/SOeFXiu.png)
- ![](https://i.imgur.com/8CMJQUr.png)
- ![](https://i.imgur.com/DXS92uG.png)
- ![](https://i.imgur.com/Vn7L1xI.png)
- ![](https://i.imgur.com/8Hck1tI.png)
- ![](https://i.imgur.com/YFqaQtS.png)

## 그래프로 이해

![](https://i.imgur.com/I4Z2TV8.png)
- 사탕 가격의 미분은 5.5
- 사탕 개수의 미분은 220
- 소비세의 미분은 1000

>- 소비세와 사탕 가격이 같은 양만큼 오르면 최종 금액에는 소비세가 1000의 크기로 사탕 가격이 5.5 크기로 영향을 준다
>- 단 이 예에서 소비세와 사탕 가격은 단위가 다르니 주의 (소비세 1은 100%, 사과 가격 1은 1원)
>- 함성 함수 처럼 여러 변수가 주어졌을 때 구하고자 하는 변수를 제외한 나머지 변수를 고정
>	- 사탕 개수와 소비세의 값이 고정 되었을 때
>	- 사탕 가격의 변화량은 5.5
>		- 사탕가격인 500원으로 변화했다고 해본다면
>		- 500 x 5 x 1.1 = 2750
>		- 2750 * 5.5 미분값을 곱하면 원래 값 500원이 나온다.



## 단순한 계층 구현하기

- 위에 그림을 코드로 구현해보자

### 곱셉 계층
- forward() 는 순전파, backward() 역전파를 처리한다.

```python
class MulLayer:
    def __init__(self):
        self.x = None
        self.y = None

    def forward(self, x, y):
        self.x = x
        self.y = y                
        out = x * y

        return out
  
    def backward(self, dout):
        dx = dout * self.y  # x와 y를 바꾼다.
        dy = dout * self.x
  
        return dx, dy
```

#### 사탕그래프의 순전파 구현

```python
apple = 200
apple_num = 5
tax = 1.1

# 계층들
mul_apple_layer = MulLayer()
mul_tax_layer = MulLayer()

# 순전파
apple_price = mul_apple_layer.forward(apple, apple_num)
price = mul_tax_layer.forward(apple_price, tax)
print(price) # 1100

# 사탕그래프의 역전파 구현
dprice=1

dapple_price, dtax = mul_tax_layer.backward(dprice)
dapple, dapple_num = mul_apple_layer.backward(dapple_price)
print(dapple, dapple_num, dtax) # 5.5 220 1000
```

>- backward() 호출 순서는 forward() 때와는 반대로 하면 된다.

### 덧셈 계층

```python
class AddLayer:
    def __init__(self):
        pass

    def forward(self, x, y):
        out = x + y
        return out

    def backward(self, dout):
        dx = dout * 1
        dy = dout * 1
        return dx, dy

# dout 는 미분
```

## 활성화 함수 계층 구현하기

### ReLU 계층

- 수식
- ![](https://i.imgur.com/jVpxzcQ.png)
- X 에 대한 미분
- ![](https://i.imgur.com/ErJcumP.png)
- ![](https://i.imgur.com/OsDJBz3.png)

#### 코드 구현

```python
class Relu:
    def __init__(self):
        self.mask = None

    def forward(self, x):
        self.mask = (x <= 0) # mask에 true false로 저장됨
        out = x.copy() # out은 원래 값
        out[self.mask] = 0
        return out

    def backward(self, dout):
        dout[self.mask] = 0
        dx = dout
        return dx
```

```python
x = np.array( [[1.0, -0.5], [-2.0, 3.0]] )
print(x)
# [[ 1. -0.5]
# [-2. 3. ]]

# relu = Relu()
# print(relu.forward(x))

mask = (x <= 0)
print(mask)
# [[False True]
# [ True False]]
```

>- mask 는 True / False로 구성된 넘파이 배열
>- 순전파의 입력인 x의 원소 값이 0 이하인 인덱스는 True
>- 그 외 (0보다 큰 원소)는 False로 유지
>	- mask 변수는 -> True/False로 구성된 넘파이 배열을 유지한다.



### Sigmoid 계층

참고 : http://taewan.kim/post/sigmoid_diff/

- ![](https://i.imgur.com/9rKoigP.png)
- ![](https://i.imgur.com/cqiS1Op.png)
- Sigmoid 계층의 계산 그래프 (순전파)
	- ![](https://i.imgur.com/STr2ehB.png)
	- 위 식을 분석해보자

#### 1 단계
'/'노드 -> y= 1/x 를 미분하면 다음과 같은 식이 된다.
- x = 1+exp(-1) 를 치환했다고 보면 된다.
- ![](https://i.imgur.com/JOO3MIf.png)
- ![](https://i.imgur.com/U9frr9G.png)
- -y^2 가 되는 이유
- <iframe width="1040" height="596" src="https://www.youtube.com/embed/bluxeoECN7U" title="73. 몫의 미분법 - 개념정리" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
- 참고 : https://ok-lab.tistory.com/151


#### 2 단계
'+' 노드는 상류의 값을 여과 없이 하류로 보낸다.
- ![](https://i.imgur.com/jkHaKUy.png)

#### 3 단계
'exp' 노드는 y = exp(x) 연산을 수행한다.
- ![](https://i.imgur.com/Ygeinrh.png)
- ![](https://i.imgur.com/iJUXatr.png)

#### 4 단계
'x' 노드는 순전파 때의 값을 서로 바꿔 곱한다. 여기서는 -1을 곱한다.
- ![](https://i.imgur.com/xqxv4sy.png)

#### 간소화 버전
- ![](https://i.imgur.com/LO9ejwv.png)
- ![](https://i.imgur.com/eiLG2Ir.png)

#### 코드 구현
```python
class Sigmoid:
    def __init__(self):
        self.out = None

    def forward(self, x):
        out = 1 / (1 + np.exp(-x))
        self.out = out
        return out

    def backward(self, dout):
        dx = dout * (1.0 - self.out) * self.out
        return dx
```

>- 순전파의 출력을 인스턴스 변수 out에 보관했다가 역전파 계산 때 그 값을 사용한다.

## Affine/Softmax 계층 구현하기

### Affine 계층

![](https://i.imgur.com/xlBkW1b.png)

>- 신경망의 순전파 때 수행하는 행렬의 곱은 기하학에서는 *어파인 변환(Affine transformation)* 이라고 한다.
>- 이 책에서는 어파인 변환을 수행하는 처리를 Affine 계층이라는 이름으로 구현한다고 써있다.

```python
X = np.random.rand(2) # 입력 
W = np.random.rand(2,3) # 가중치 
B = np.random.rand(3) # 편향 
X.shape # (2,) 
W.shape # (2, 3) 
B.shape # (3,)
Y = np.dot(X, W)+B
```

![](https://i.imgur.com/pU05skg.png)
>- 이전에 나와있던 예들은 스칼라 값이였지만 현재는 행렬이다.
>- 역전파 또한 행렬의 원소마다 전개해보면 스칼라값을 사용한 지금까지의 계산 그래프와 같은 순서로 생각 가능하다.
>- ![](https://i.imgur.com/YjpamwZ.png)
>- T는 전치행렬을 뜻한다.
>- W의 형상이 (2,3) 이었다면 전치행렬은 (3,2)가 된다.
>- ![](https://i.imgur.com/WEu3Xdq.png)

#### Affine 계층의 역전파
- ![](https://i.imgur.com/o2ndyry.png)
- ![](https://i.imgur.com/NQ9AIFz.png)


### 배치용 Affine 계층

> 입력 데이터 X 하나만 고려한게 아닌 데이터 N개를 묶어 순전파하는 경우, 즉 배치용 Affine 계층을 알아보자
> 	묶은 데이터를 `배치`라고 부른다

![](https://i.imgur.com/0phvSg5.png)
>- 기존과 다른점은 입력인 X의 형상이 (N,2)가 된 점이다.
>- 그 뒤로는 지금까지와 같이 계산 그래프의 순서를 따라 차례대로 행렬의 형상에 주의하면 ![](https://i.imgur.com/gw5LiqW.png) 과 ![](https://i.imgur.com/ljzzDxf.png) 은 이전과 같이 도출할 수 있다.

#### 편향 더하기
```python
X_dot_W = np.array([[0, 0, 0], [10, 10, 10]])
B = np.array([1, 2, 3])
X_dot_W 
-> array([[ 0, 0, 0], [ 10, 10, 10]])

X_dot_W + B
-> array([[ 1, 2, 3], [11, 12, 13]])
```
>- 순전파 때의 편향 덧셈은 X W에 대한 편향이 각 데이터에 더해진다.
>- 예를 들어 N=2 (데이터가 2개)로 한 경우, 편향은 그 두 데이터 각각 계산 결과에 더해진다.
>- 순전파의 편향 덧셈은 각각의 데이터 (1번쨰 데이터, 2번째 데이터) 에 더해진다.
>- 그래서 역전파 떄는 각 데이터의 역전파 값이 편향의 원소에 모여야 한다.

```python
dY = np.array([[1, 2, 3], [4, 5, 6]])
dY 
-> array([[1, 2, 3], [4, 5, 6]])
dB = np.sum(dY, axis=0)
dB 
-> array([5Z 7, 9])
```

>- 이 예에서 데이터가 2개 (N=2) 라고 가정한다면 편향의 역전파는 그 두 데이터에 대한 미분을 데이터마다 더해서 구한다.
>- 그래서 np.sum() 에서 0번째 축(데이터를 단위로 한 축)에 대해서 (axis=0) 의 총합을 구한다


```python
class Affine: 
	def __init__(self, W, b): 
		self.W = W 
		self.b = b 
		self.x = None 
		self.dW = None 
		self.db = None 
		
	def forward(selfz x): 
		self.x = x 
		out = np.dot(x, self.W) + self.b 
		
		return out
		
	def backward(self, dout): 
		dx = np.dot(dout, self.W.T) 
		self.dW = np.dot(self.x.T, dout) 
		self.db = np.sum(dout, axis=0) 
		
		return dx
```
> 입력 데이터가 (4차원 데이터)인 경우도 고려한 거라 다음 구현과는 약간 차이가 있을 수 있다고 한다.

### Softmax-with-Loss 계층
- 소프트 맥스 함수는 입력 값을 정규화하여 출력한다.
- 손글씨 숫자 인식에서 Softmax 계층의 출력은 다음과 같다.

![](https://i.imgur.com/uamtKjl.png)
>- 입력 이미지가 Affine 계층과 ReLU 계층을 통과하여 변환되고, 마지막 Softmax 계층에 의해서 10개의 입력이 정규화된다.
>- 이 그림에서는 숫자 `0`의 점수는 5.3
>- Softmax 계층에 의해 0.008(0.8%)로 변환된다.
>- 또 '2'의 점수는 10.1에서 0.991(99.1%)로 변환된다.
>- 그림과 같이 Softnax 계층은 입력 값을 정규화(출력의 합이 1이 되도록 변형)하여 출력한다.
>- 또한, 손글씨 숫자는 가짓수가 10개(10클래스 분류) 이므로 Softmax 계층의 입력은 10개다.

- 신경망에서 수행하는 작업은 **학습**과 **추론** 두 가지다. 추론할 때는 일반적으로 Softmax 계층을 사용하지 않는다. 예컨대 위 그림의 신경망은 추론할 때는 마지막 Affine 계층의 출력을 인식 결과로 이용한다. 또한 신경망에서 정규화하지 않는 출력 결과(위 이미지) 에서는 Softmax 앞의 Affine 계층의 출력 을 **점수(score)** 라고한다.
- 즉 신경망 추론에서 답을 하나만 내는 경우는 가장 높은 점수만 알면 되니 Softmax 계층은 필요 없다는 뜻이다. 반면 신경망을 학습할 떄는 Softmax 계층이 필요하다.
- 좀 더 정리 필요

#### 소프트매스 계층의 구현
- 손실 함수인 교차 엔트로피 오차도 포함하여 `Softnax-with-Loss 계층` 이라는 이름을 구현
- 계산 그래프는 아래와 같다.
![](https://i.imgur.com/emSJoQD.png)
- 위의 계산 그래프를 간소화 하면 아래 그림과 같다.
- ![](https://i.imgur.com/UQihAnd.png)
- 여기에서 3클래스 분류를 가정하고 이전 계층에서 3개의 입력 (점수)를 받는다.
- 그림과 같이 Softmax 계층은 입력(a1,a2,a3)을 정규화하여 (y1,y2,y3)을 출력한다.
- Cross Entropy Error 계층은 Softmax의 출력 (y1,y2,y3) 그리고 정답 레이블 (t1,t2,t3) 를 받고 이 데이터들로부터 손실 L을 출력한다.
>- 이 그림에서 주목할 것은 역전파의 결과다.
>- Softmax 계층의 역전파는 ( y1-t1, y2-t2, y3- t3 ) 이라는 깔끔한 결과를 보여준다.
>- (y1,y2,y3) 는 Softmax 계층의 출력이고 (t1,t2,t3) 는 정답 레이블이므로 ( y1-t1, y2-t2, y3- t3 ) 는 Softmax 계층의 출력과 정답 레이블의 차분이다.
>- 신경망의 역전파에서는 이 차이인 오차가 앞 계층에 전해지는 것이다. ***(신경망 학습의 중요한 성질)***

- 그런데 신경망 학습의 목적은 신경망의 출력 (Softmax의 출력)이 정답 레이블의 오차를 효율적으로 앞 계층에 전달해야 한다.
- 앞의 ( y1-t1, y2-t2, y3- t3 )라는 결과는 바로 Softmax 계층의 출력과 정답 레이블의 차이로, 신경망의 현재 출력과 정답 레이블의 오차를 있는 그대로 드러낸다.

>- ‘소프트맥스 함수’의 손실 함수로 ‘교차 엔트로피 오차’를 사용하니 역전파가 ( y1-t1, y2-t2, y3- t3 ) 로 딱 떨어진다.
>- 사실 이런 말끔함은 우연이 아니라 교차 엔트로피 오차라는 함수가 그렇게 설계되 었기 때문이다.
>- 또 회귀의 출력층에서 사용하는 ‘항등 함수’의 손실 함수로 ‘오차제곱합’을 이용하는 이유도 이와 같다.
>- 즉, ‘항등 함수’의 손실 함수로 ‘오차제곱합’을 사용하면 역전파 의 결과가 ( y1-t1, y2-t2, y3- t3 ) 로 말끔히 떨어진다.

##### 구현해보기
- 정답 레이블이 (0,1,0)
- Softmax 계층이 (0.3, 0.2, 0.5)를 출력
- 정답 레이블을 보면 정답의 인덱스는 1
- 그런데 출력에서는 이때의 확률이 0.2 (20%)
> - 이 시점의 신경망은 제대로 인식하지 못하고 있다.
> - 이 경우 Softmax 계층의 역전파는 (0.3, -0.8, 0.5)라는 큰 오차를 전파
> - 결과적으로 Softmax 계층의 앞 계층들은 그 큰 오차로부터 결과를 얻는다.

- 정답 레이블이 똑같이 (0, 1, 0)
- Softmax 계층이 (0.01, 0.99, 0)을 출력
- 이 경우 Softmax 계층의 역전파가 보내는 오차는 비교적 작은 (0.01, -0.01, 0) 이다. 
- 이번에는 앞 계층으로 전달된 오차가 작으므로 학습하는 정도도 작아진다 (신경망이 정확하게 인식하고 있다)

##### Softmax-with-Loss 계층 코드 구현
```python
class SoftmaxWithLoss: 
	def __init__(self): 
		self.loss = None # 손실 
		self.y = None # softmax의 출력 
		self.t = None # 정답 레이블(원-핫 벡터)
		 
	def forward(self, xz t): 
		self.t = t 
		self.y = softmax(x) 
		self.loss = cross_entropy_error(self.y, self.t) 
		return self.loss 
	
	def backward(self, dout=1): 
		batch_size = self.t.shape[0] 
		dx = (self.y - self.t) / batch_size 
		return dx
```
>- 역전파 때는 전파하는 값을 배치의 수(batch_size)로 나눠서 데이터 1개당 오차를 앞 계층으로 전파한다.
>- 여기에 배치사이즈로 나눠주는 이유는 cross entropy의 정의가 각 데이터들의 cross entropy의 평균이다.
>- 다시 말해 각 데이터의 cross entropy를 모두 더한 후 데이터의 개수로 나눠준다 -> 이 데이터 개수가 batch_size다
>	- N행의 그레디언트 행렬의 모든 성분의 값이 N으로 나눠진다.











출처 : 밑바닥부터 시작하는 딥러닝