---
layout: post
title: "코인 가격 예측 스터디노트 - 1. Google Colab"
category: "machine_learning"
tag:
  - "python"
  - "data science"
  - "machine learning"
  - "deep learning"
---
_스터디파이에서 '딥러닝으로 주식/코인 가격 예측하기'를 참여하며 공부한 내용을 정리한 것입니다.._

학습 자료 :  
[1. Introduction to Deep Learning - Deep Learning basics with Python, TensorFlow and Keras p.1](https://pythonprogramming.net/introduction-deep-learning-python-tensorflow-keras/)  
[2. [Colab] 텐서플로우 백엔드를 사용해서 케라스 상에서 무료 GPU를 사용하는 방법을 알아봅니다.](https://youtu.be/UKujX90xLHo)

#### **Google Colab 사용하기**

Google Colab을 추가하고, 구글 드라이브에서 노트북을 연다.
Runtime -> Change runtime Type 에서 Python 3, GPU를 선택.

```python
!cat /proc/meminfo
```
```python
!cat /proc/cpuinfo
```
위의 코드로 성능을 확인하면 아래와 같다.

```cmd
MemTotal:       13335204 kB
model name	: Intel(R) Xeon(R) CPU @ 2.30GHz
```

#### **Google Colab에서 GPU를 사용한 MNIST**

```python
import tensorflow as tf
import matplotlib.pyplot as plt
mnist = tf.keras.datasets.mnist

%matplotlib inline
```

```python
(x_train, y_train),(x_test, y_test) = mnist.load_data()

x_train = tf.keras.utils.normalize(x_train, axis = 1)
x_test = tf.keras.utils.normalize(x_test, axis = 1)

### GPU 사용
with tf.device('/gpu:0'):

  model = tf.keras.models.Sequential()
  model.add(tf.keras.layers.Flatten())

  # Flatten : Flattens the input. Does not affect the batch size.
  model.add(tf.keras.layers.Dense(128, activation=tf.nn.relu))
  model.add(tf.keras.layers.Dense(128, activation=tf.nn.relu))
  model.add(tf.keras.layers.Dense(10, activation=tf.nn.softmax))

  model.compile(optimizer='adam',
               loss='sparse_categorical_crossentropy',
                metrics = ['accuracy']              
               )

  model.fit(x_train, y_train, epochs=3)
```

```python
val_loss, val_acc = model.evaluate(x_test, y_test)
print(val_loss, val_acc)
```
```cmd
Epoch 1/3
60000/60000 [==============================] - 10s 173us/step - loss: 0.2626 - acc: 0.9228
Epoch 2/3
60000/60000 [==============================] - 10s 170us/step - loss: 0.1075 - acc: 0.9667
Epoch 3/3
60000/60000 [==============================] - 10s 169us/step - loss: 0.0742 - acc: 0.9766
```python
10000/10000 [==============================] - 1s 76us/step
0.09344221491254866 0.9716
```

```python
predictions = model.predict(x_test)

import numpy as np
print(np.argmax(predictions[0]))
```
```cmd
7
```


```python
plt.imshow(x_test[0],cmap=plt.cm.binary)
plt.show()
```
![studypie_1_1]({{ site.url }}\assets\study_notes\studypie_1\1.png)

```python
predictions[0]
```

```cmd
array([1.7975790e-10, 2.3375832e-09, 3.0333268e-08, 3.2107891e-07,
       2.4037560e-11, 8.0090983e-09, 4.9225248e-15, 9.9999917e-01,
       1.3270974e-08, 4.5924011e-07], dtype=float32)
```
prediction 값을 직접 살펴보면 7이 제일 확률이 높다.(softmax 값)  




#### **categorical_crossentropy와 sparse_categorical_crossentropy 의 차이**




1) categorical_crossentropy  
 -> 원-핫-인코딩된 레이블로 cross entropy를 계산 (target과 output의 shape가 같아야 함)

```python
def categorical_crossentropy(target, output, from_logits=False, axis=-1):
    """Categorical crossentropy between an output tensor and a target tensor.
    # Arguments
        target: A tensor of the same shape as `output`.
        output: A tensor resulting from a softmax
            (unless `from_logits` is True, in which
            case `output` is expected to be the logits).
        from_logits: Boolean, whether `output` is the
            result of a softmax, or is a tensor of logits.
        axis: Int specifying the channels axis. `axis=-1`
            corresponds to data format `channels_last`,
            and `axis=1` corresponds to data format
            `channels_first`.
    # Returns
        Output tensor.
    # Raises
        ValueError: if `axis` is neither -1 nor one of
            the axes of `output`.
    """
```

2) sparse_categorical_crossentropy  
 -> 한개의 있는 정답레이블을 가지고 있음  
 -> MNIST의 경우 : 정답 레이블이 0 ~ 9로, 하나의 열에 정답 레이블을 정수로 표시되어 있으므로 sparse_categorical_crossentropy

```python
def sparse_categorical_crossentropy(target, output, from_logits=False, axis=-1):
    """Categorical crossentropy with integer targets.
    # Arguments
        target: An integer tensor.
        output: A tensor resulting from a softmax
            (unless `from_logits` is True, in which
            case `output` is expected to be the logits).
        from_logits: Boolean, whether `output` is the
            result of a softmax, or is a tensor of logits.
        axis: Int specifying the channels axis. `axis=-1`
            corresponds to data format `channels_last`,
            and `axis=1` corresponds to data format
            `channels_first`.
    # Returns
        Output tensor.
    # Raises
        ValueError: if `axis` is neither -1 nor one of
            the axes of `output`.
    """
```

Tensorflow 백엔드 소스 :  
https://github.com/keras-team/keras/blob/master/keras/backend/tensorflow_backend.py
