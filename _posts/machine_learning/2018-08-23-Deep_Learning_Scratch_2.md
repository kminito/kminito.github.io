---
layout: post
title: "딥러닝 노트 - 2. 간단한 신경망 구현"
category: "machine_learning"
tag:
  - "python"
  - "data science"
  - "machine learning"
  - "deep learning"
---
_'밑바닥부터 시작하는 딥러닝'을 공부하며 정리한 내용입니다._




### **1. 활성화 함수**

#### (1) Step Function

```python
def step_function(x):
    if x > 0:
        return 1
    else:
        return 0
```
위와 같은 기본적인 형태에서, 인수 x에 배열이 들어갈 수 있도록 수정하면 아래와 같다.

```python
def step_function(x):
    y = x > 0
    return y.astype(np.int)

    # y는 부울린 타입을 리턴하므로 astype으로 0,1로 바꾸어 리턴하도록함
```

그래프를 그려보면 아래와 같다.

```python
import numpy as np
import matplotlib.pylab as plt

x = np.arange(-5.0, 5.0, 0.1)
y = step_function(x)
plt.plot(x, y)
plt.ylim(-0.1, 1.1)
plt.show()
```
![scratch_2_1]({{ site.url }}\assets\ML\scratch\2_1.png)



#### (2) Sigmoid

```python
def sigmoid(x):
    return 1/ (1+np.exp(-x))
```

Sigmoid는 위의 Step Fuction과 달리 모든 정의역에서 미분가능하다.

```python
x = np.arange(-5.0, 5.0, 0.1)
y = sigmoid(x)
plt.plot(x,y)
plt.ylim(-0.1, 1.1)
plt.show
```
![scratch_2_2]({{ site.url }}\assets\ML\scratch\2_2.png)


#### (3) ReLu (Rectified Linear Unit))

```python
def relu(x):
    return np.maximum(0,x)
```


```python
x = np.arange(-5.0, 5.0, 0.1)
y = relu(x)
plt.plot(x,y)
plt.ylim(-1, 5.5)
plt.show
```

![scratch_2_3]({{ site.url }}\assets\ML\scratch\2_3.png)



### **2. 3층 신경망 만들기**

2(입력) - 3 - 2 - 2 (출력)형태의 3층 신경망을 만들어보자.

![scratch_2_4]({{ site.url }}\assets\ML\scratch\2_4.png)
그림출처 : http://kolikim.tistory.com/16


#### (1) 가중치와 편향 초기화

신경망의 가중치와 편향을 초기화하는 `init_network`함수를 만듭니다.

```python
def init_network():
    network = {}
    network['W1'] = np.array([[0.1, 0.3, 0.5],[0.2, 0.4, 0.6]])
    network['b1'] = np.array([0.1, 0.2, 0.3])

    network['W2'] = np.array([[0.1, 0.4],[0.2, 0.5],[0.3,0.6]])
    network['b2'] = np.array([0.1, 0.2])       

    network['W3'] = np.array([[0.1, 0.3],[0.2, 0.4]])
    network['b3'] = np.array([0.1, 0.2])      

    return network
```
`init_network` 함수는 정해진 값으로 가중치와 편향을 초기화하는 딕셔너리를 리턴합니다. 각 가중치의 shape를 보면 행렬의 dot product가 가능한 것을 알 수 있습니다.


#### (2) 신경망 순전파

신경망의 가중치와 편향 값이 들어있는 딕셔너리와, 신경망의 입력값 `x` 를 받아 순전파하는 함수 `forard`를 만듭니다.

```python
def forward(network, x):
    # 가중치와 편향 변수에 값을 할당
    W1, W2, W3 = network['W1'],network['W2'],network['W3']
    b1, b2, b3 = network['b1'],network['b2'],network['b3']

    # 은닉층을 통과할 때의 연산. 활성화 함수는 sigmoid
    a1 = np.dot(x, W1) + b1
    z1 = sigmoid(a1)
    a2 = np.dot(z1, W2) + b2
    z2 = sigmoid(a2)

    # 출력층은 활성화함수 없이 그냥 identity_function 이용
    a3 = np.dot(z2, W3) + b3
    y = identity_function(a3)

    return y

    # 출력층에 사용된 항등 함수
def identity_function(x):
    return x
```

#### (3) 신경망 실행해보기
위의 완성된 신경망을 x = [1.0, 0.5]를 입력하여 돌려보면 아래와 같은 결과를 받을 수 있다.
```python
network = init_network()
x = np.array([1.0, 0.5])
y = forward(network, x)
print(y)
```
```cmd
[0.31682708 0.69627909]
```


#### (4) 출력층 설계

위 신경망의 경우는 regression을 위해 출력층의 활성화 함수에 항등 함수를 적용했지만, Classification을 위해서는 항등 함수대신 softmax 함수를 사용하면 된다.

```python
def softmax(a):
    exp_a = np.exp(a)
    sum_exp_a = np.sum(exp_a)
    y = exp_a / sum_exp_a

    return y
```
하지만 위 함수의 경우에는 입력값이 매우 클 경우 나눗셈 과정에서 오버플로가 일어날 수 있다. 따라서 아래와 같이 `c = np.max(a)`로 두고 모든 `a`에서 `c`를 뺀 후 계산해서 오버플로우를 방지한다.

```python
def softmax(a):
    c = np.max(a)
    exp_a = np.exp(a-c)
    sum_exp_a = np.sum(exp_a)
    y = exp_a / sum_exp_a

    return b
```
