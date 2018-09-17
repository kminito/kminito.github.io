---
layout: post
title: "딥러닝 노트 - 3. 신경망 학습"
category: "machine_learning"
tag:
  - "python"
  - "data science"
  - "machine learning"
  - "deep learning"
---
_'밑바닥부터 시작하는 딥러닝'을 공부하며 정리한 내용입니다._




### **1. 손실 함수 (loss function)**

학습을 위해서 정확도가 아닌 오차를 사용하는 이유  

 **_신경망을 학습할 때 정확도를 지표로 삼아서는 안 된다.  
 정확도를 지표로 하면 매개변수의 미분이 대부분의 장소에서 0이 되기 때문이다_**

활성화 함수로 계단 함수를 사용하지 않는 이유도 비슷함. 계단 함수의 경우는 대부분의 장소에서 기울기가 0이므로, 매개변수의 변화에 따른 출력 변화를 보기가 어려움.

#### (1) 평균 제곱 오차

```python
def mean_squared_error(y, t):
    return 0.5 * np.sum((y-t)**2)
```

#### (2) 교차 엔트로피 오차 (원-핫 인코딩 레이블)

**데이터 1개용**

```python
def cross_entropy_error(y, t):
    delta = 1e-7
    return -np.sum(t * np.log(y + delta))
```

**배치용**

```python
def cross_entropy_error(y, t):
    # 1차원 리스트로 들어올 경우 shape 바꿈
    if y.ndim == 1:
        t = t.reshape(1, t.size)
        y = y.reshape(1, y.size)

    batch_size = y.shape[0]
    return -np.sum(t * np.log(y + 1e-7)) / batch_size
```


### **2. 매개변수 조정**

위의 손실 함수값을 바탕으로 구체적으로 어떻게 매개변수를 조정할지를 정한다.  

먼저 인풋 x에서의 함수값 기울기를 수치미분으로 표현하면 아래와 같다.  
고등학교 때 배운 대로 h를 매우 작은 값으로 두고, x-h, x+h 에서의 평균변화량을 계산한다.  

```python
#수치 미분
def numerical_gradient(f, x):
    h = 1e-4
    grad = np.zeros_like(x)

    for idx in range(x.size):
        tmp_val = x[idx]

        # f(x+h) 계산
        x[idx] = tmp_val + h
        fxh1 = f(x)
        # f(x-h) 계산
        x[idx] = tmp_val - h
        fxh2 = f(x)      

        grad[idx] = (fxh1 - fxh2) / (2*h)
        x[idx] = tmp_val

    return grad
```

#### (1) 경사 하강법

입력값에 따른 함수값의 변화 기울기를 확인하여, 기울기의 크기가 작아지는 방향으로 매개변수 값을 조정. 여기서 학습률(learning rate, lr) 은 한번에 매개변수를 얼마나 조정할지를 정하는 값.


```python
def gradient_descent(f, init_x, lr=0.01, step_num=100):
    x = init_x

    for i in range(step_num):
        grad = numerical_gradient(f, x)
        x -= lr * grad
    return x

```

#### (2) 신경망에서의 실제 기울기 계산



```python
import sys, os
sys.path.append(os.pardir)  # 부모 디렉터리의 파일을 가져올 수 있도록 설정
import numpy as np
# 그냥 참고용이므로 아래 함수들은 교재 첨부파일에서 임포트
from common.functions import softmax, cross_entropy_error
from common.gradient import numerical_gradient

class simpleNet:
    def __init__(self):
        self.W = np.random.randn(2,3)

    def predict(self, x):
        return np.dot(x, self.W)

    def loss(self, x, t):
        z = self.predict(x)
        y = softmax(z)
        loss = cross_entropy_error(y, t)

        return loss
```

```python
x = np.array([0.6, 0.9])
t = np.array([0, 0, 1])

net = simpleNet()

f = lambda w: net.loss(x, t)
dW = numerical_gradient(f, net.W)

print(dW)
```
```cmd
[[ 0.19982819  0.35555319 -0.55538138]
 [ 0.29974228  0.53332979 -0.83307207]]
```


### **3. 학습 알고리즘 구현하기**

먼저 2층 신경망을 하나의 클래스로 구현하고, 이후 위의 기울기를 이용하여 미니배치 학습을 구현.


#### (1) 2층 신경망 클래스 구현하기

이전에 학습한 내용들에 손실함수와 정확도, 기울기 계산이 추가됨.

```python
import sys, os
sys.path.append(os.pardir)
from common.functions import *
from common.gradient import numerical_gradient

class TwoLayerNet:
    def __init__(self, input_size, hidden_size, output_size,
                weight_init_std=0.01):

        # 가중치 초기화
        self.params = {}
        self.params['W1'] = weight_init_std * np.random.randn(input_size, hidden_size)
        self.params['b1'] = np.zeros(hidden_size)

        self.params['W2'] = weight_init_std * np.random.randn(hidden_size, output_size)
        self.params['b2'] = np.zeros(output_size)

    # 현재 가중치와 편향으로 출력값 예측
    def predict(self, x):
        W1, W2 = self.params['W1'], self.params['W2']
        b1, b2 = self.params['b1'], self.params['b2']

        a1 = np.dot(x, W1) + b1
        z1 = sigmoid(a1)
        a2 = np.dot(z1, W2) + b2
        y = softmax(a2)

        return y

    # 교차 엔트로피 오차 계산
    # x : 입력 데이터, t : 정답 레이블
    def loss(self, x, t):
        y = self.predict(x)

        return cross_entropy_error(y, t)

    # 정확도 계산
    def accuracy(self, x, t):
        y = self.predict(x)
        y = np.argmax(y, axis = 1)
        t = np.argmax(t, axis = 1)

        accuracy = np.sum(y == t) / float(x.shape[0])
        return accuracy

    # 이미 정의된 함수 numerical_gradent를 이용하여 입력값 x에서의 손실함수의 기울기 계산
    def numerical_gradient(self, x, t):
        loss_W = lambda W: self.loss(x, t)

        grads = {}
        grads['W1'] = numerical_gradient(loss_W, self.params['W1'])
        grads['b1'] = numerical_gradient(loss_W, self.params['b1'])
        grads['W2'] = numerical_gradient(loss_W, self.params['W2'])
        grads['b2'] = numerical_gradient(loss_W, self.params['b2'])

        return grads
```

#### (2) 미니배치 학습 구현하기
위의 신경망에 학습이 가능하도록 함. 집에서는 시간이 너무 오래 걸려서 반복횟수와 배치 크기를 줄여 간단히 테스트만 해봄.

```python
import numpy as np
from dataset.mnist import load_mnist

(x_train, t_train), (x_test, t_test) = load_mnist(normalize=True, one_hot_label=True)

train_loss_list = []

# 하이퍼파라미터
iters_num = 1000 # 반복 횟수
train_size = x_train.shape[0]
batch_size = 100 # 미니배치 크기
learning_rate = 0.1

network = TwoLayerNet(input_size = 784, hidden_size = 50, output_size = 10)
print("시작")
for i in range(iters_num):
    # 미니배치 획득
    batch_mask = np.random.choice(train_size, batch_size)
    x_batch = x_train[batch_mask]
    t_batch = t_train[batch_mask]

    # 기울기 계산
    grad = network.numerical_gradient(x_batch, t_batch)

    # 매개변수 갱신
    for key in ('W1', 'b1', 'W2', 'b2'):
        network.params[key] -= learning_rate * grad[key]
        print('갱신')
    loss = network.loss(x_batch, t_batch)
    train_loss_list.append(loss)

```
#### (3) epoch 추가 및 테스트 데이터로 정확도

1 epoch : 학습에서 훈련 데이터를 모두 소진했을 때의 횟수  
ex) 훈련 데이터 10,000개를 100개의 미니배치로 학습할 경우, 확률적 경사 하강법을 100회 반복하면 1 epoch이 됨.

에폭을 반복할수록 훈련 데이터에 대한 정확도가 높아지나, 특정 시점을 지나면 오버피팅이 일어날 수 있음. 나중에 배울 내용이지만, 어느 순간부터 시험 데이터에 대한 정확도가 떨어지면 그 때에 학습을 준단하여 오버피팅을 예방할 수 있음.  

**epoch을 추가한 신경망**
```python
import numpy as np
from dataset.mnist import load_mnist

(x_train, t_train), (x_test, t_test) = load_mnist(normalize=True, one_hot_label=True)

network = TwoLayerNet(input_size = 784, hidden_size = 50, output_size = 10)

# 하이퍼파라미터
iters_num = 1000 # 반복 횟수
train_size = x_train.shape[0]
batch_size = 100 # 미니배치 크기
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
    grad = network.numerical_gradient(x_batch, t_batch)

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

        print("train acc, test acc :" + str(train_acc)+ ",", str(test_acc))
```

`iters_num` 을 100으로 줄이고 했는데도 한바퀴 도는 데 1시간이 걸렸다. 물론 학습 효과는 아주 조금 있었다.
```python
plt.plot(train_loss_list)
```
![scratch_3_1]({{ site.url }}\assets\ML\scratch\3_1.png)

위의 그래프를 보면 로스가 아주 조금은 줄어들고 있는 것으로 보인다..
