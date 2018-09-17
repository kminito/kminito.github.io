---
layout: post
title: "딥러닝 노트 - 4. 오차역전파법"
category: "machine_learning"
tag:
  - "python"
  - "data science"
  - "machine learning"
  - "deep learning"
---
_'밑바닥부터 시작하는 딥러닝'을 공부하며 정리한 내용입니다._

수치 미분으로 numerical gradient를 구해 가중치와 편향을 갱신하는 것은 정말로 느립니다. 앞의 게시물에서 마지막에 작성된 코드로
고작 1배치에 100개씩 100번 돌리는 데 한시간 가까이 걸렸습니다.  

여기서는 오차역전파법을 사용해서 기울기를 구하고, 신경망을 학습시키고자 합니다.



### **1. 활성화 함수 계층 구현하기**

#### (1) Relu

```python
class Relu:
    def __init__(self):
        self.mask = None

        #넘파이 배열에서 대괄호 안의 값이 True 이면 변수 할당
        #False 이면 변수가 할당되지 않음
    def forward(self, x):
        self.mask = (x<=0)
        out = x.copy()
        # Mask가 True 이면 out=0
        # Mask가 False 이면 out = x.copy()
        out[self.mask] = 0

        return out

    def backward(self, dout):
        # Mask가 True 이면 dout=0
        # Mask가 False 이면 dout=dout           
        dout[self.mask] = 0
        dx = dout

        return dx
```


#### (2) Sigmoid

Sigmoid는 y를 역전파하면 dL/dy * y(1-y)가 된다.

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


#### (3) Affine/Softmax

신경망의 순전파 때 수행하는 행렬의 곱을 기하학에서는 어파인 변환 (affine transformation)이라고 한다. -> 단순히 x.w + b 의 연산을 위한 층이라고 생각하면 됨. (activation 앞에 붙음)

```python
class Affine:
    def __init__(self, W, b):
        self.W = W
        self.b = b
        self.x = None
        self.dW = None
        self.db = None

    def forward(self, x):
        self.x = x
        out = np.dot(x, self.W) + self.b
        return out

    def backward(self, dout):
        dx = np.dot(dout, self.W.T)
        self.dW = np.dot(self.x.T, dout)
        self.db = np.sum(dout, axis=0)

        return dx
```

출력층에 사용될 Softmax의 경우  학습할 때에는 Loss가 계산이 되어야 하므로 Softmax + Cross Entorpy Error를 합쳐서 `Softmax-with-loss`로 사용.

```python
class SoftmaxWithLoss:
    def __init__(self):
        self.loss = None
        self.y = None
        self.t = None

    def forward(self, x, t):
        self.t = t
        self.y = softmax(x)
        self.loss = cross_entropy_error(self.y, self.t)
        return self.loss

    def backward(self, dout=1):
        batch_size = self.t.shape[0]
        dx = (self.y - self.t) / batch_size

        return dx
```


### **2. 오차역전파법을 적용한 신경망 구현하기**


#### (1) 오차역전파법으로 기울기 구하기

기존 TwoLayerNet에 오차역전파법으로 기울기를 구하는 코드를 추가하여, 수치미분과 오차역전파법 각각 사용하여 기울기를 구할 수 있도록 합니다.

```python
import sys, os
sys.path.append(os.pardir)
import numpy as np
from common.functions import *
from common.gradient import numerical_gradient
from collections import OrderedDict

class TwoLayerNet:
    def __init__(self, input_size, hidden_size, output_size,
                weight_init_std=0.01):

        # 가중치 초기화
        self.params = {}
        self.params['W1'] = weight_init_std * np.random.randn(input_size, hidden_size)
        self.params['b1'] = np.zeros(hidden_size)
        self.params['W2'] = weight_init_std * np.random.randn(hidden_size, output_size)
        self.params['b2'] = np.zeros(output_size)

        # 계층 생성
        self.layers = OrderedDict()
        self.layers['Affine1'] = Affine(self.params['W1'],self.params['b1'])
        self.layers['Relu1'] = Relu()
        self.layers['Affine2'] = Affine(self.params['W2'],self.params['b2'])

        self.lastLayer = SoftmaxWithLoss()

    # 현재 가중치와 편향으로 출력값 예측
    def predict(self, x):
        for layer in self.layers.values():
            x = layer.forward(x)
        return x

    # x : 입력 데이터, t : 정답 레이블
    def loss(self, x, t):
        y = self.predict(x)
        return self.lastLayer.forward(y, t)

    # 정확도 계산
    def accuracy(self, x, t):
        y = self.predict(x)
        y = np.argmax(y, axis = 1)

        if t.ndim != 1 : t = np.argmax(t, axis=1)

        accuracy = np.sum(y == t) / float(x.shape[0])
        return accuracy

    # 이전에 사용한 수치 미분 방식
    def numerical_gradient(self, x, t):
        loss_W = lambda W: self.loss(x, t)

        grads = {}
        grads['W1'] = numerical_gradient(loss_W, self.params['W1'])
        grads['b1'] = numerical_gradient(loss_W, self.params['b1'])
        grads['W2'] = numerical_gradient(loss_W, self.params['W2'])
        grads['b2'] = numerical_gradient(loss_W, self.params['b2'])

        return grads

    # 오차역전파법!!
    def gradient(self, x, t):
        # 순전파
        self.loss(x, t)

        # 역전파
        dout = 1
        dout = self.lastLayer.backward(dout)

        layers = list(self.layers.values())
        layers.reverse()

        for layer in layers:
            dout = layer.backward(dout)

        # 결과 저장
        grads = {}
        grads['W1'] = self.layers['Affine1'].dW
        grads['b1'] = self.layers['Affine1'].db
        grads['W2'] = self.layers['Affine2'].dW
        grads['b2'] = self.layers['Affine2'].db

        return grads

```  

위의 (가짜) 신경망을 적용하여 수치 미분과 오차역전파법으로 각각 기울기를 구해 둘의 차이를 확인하도록 하겠습니다.
```python
import sys, os
sys.path.append(os.pardir)
import numpy as np
from dataset.mnist import load_mnist

# 데이터 읽기
(x_train, t_train), (x_test, t_test) = load_mnist(normalize=True, one_hot_label=True)

network = TwoLayerNet(input_size=784, hidden_size=50, output_size=10)

x_batch = x_train[:3]
t_batch = t_train[:3]

grad_numerical = network.numerical_gradient(x_batch, t_batch)
grad_backprop = network.gradient(x_batch, t_batch)

#각 가중치 사이의 절대값을 구한 후, 그 절댓값들의 평균을 낸다
for key in grad_numerical.keys():
    diff = np.average(np.abs(grad_backprop[key] - grad_numerical[key]))
    print(key + ":" + str(diff))
```

```cmd
W1:2.8740113033704586e-07
b1:2.9147065476906715e-06
W2:6.122116533333005e-09
b2:1.402556598917304e-07
```

책보다는 조금 크긴 해도, 여전히 오차가 매우 작은 것을 확인할 수 있습니다.


#### (2) 오차역전파법을 사용한 학습 구현하기
수치 미분으로 가중치와 편향을 학습시켰던 지난 게시물의 코드에서, 수치 미분을 오차역전파법으로 바꾸기만 하겠습니다.

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

print("시작")
for i in range(iters_num):
    # 미니배치 획득
    batch_mask = np.random.choice(train_size, batch_size)
    x_batch = x_train[batch_mask]
    t_batch = t_train[batch_mask]

    # 기울기 계산
    grad = network.gradient(x_batch, t_batch)
    # grad = network.numerical_gradient(x_batch, t_batch) 이 내용만 바꿈!

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
```cmd
시작
train acc, test acc :0.10525, 0.1034
train acc, test acc :0.9020833333333333, 0.906
```

1000번 반복하는 데, 10초가 안 걸렸씁니다. 지난 번 수치 미분에서는 100번 하는 데 한시간 가까이 걸렸는데 말이죠. 오차역전파법이 얼마나 효율적인지 몸소 느낄 수 있는 소중한 시간이었습니다.
