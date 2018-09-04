---
layout: post
title: "딥러닝 노트 - 1. 퍼셉트론"
category: "machine_learning"
tag:
  - "python"
  - "data science"
  - "machine learning"
  - "deep learning"
---
_'밑바닥부터 시작하는 딥러닝'을 공부하며 정리한 내용입니다._



**한 개의 퍼셉트론으로 AND, OR, NAND의 연산은 가능하나, XOR은 불가능하다.**

### **1. AND 게이트**

**AND 게이트의 진리표**
<table class="tftable" border="1">
<tr><th>x1</th><th>x2</th><th>y</th></tr>
<tr><td>0</td><td>0</td><td>0</td></tr>
<tr><td>1</td><td>0</td><td>0</td></tr>
<tr><td>0</td><td>1</td><td>0</td></tr>
<tr><td>1</td><td>1</td><td>1</td></tr>
</table>

위의 AND 게이트를 퍼셉트론으로 표현하면 아래와 같습니다. 여기서는 입력값 x1, x2에 각각 가중치 w1, w2를 곱한 값의 합이 theta보다 크면 1을 리턴하도록 되어있습니다.

```python
def AND(x1, x2):
    w1, w2, theta = 0.5, 0.5, 0.7
    tmp = x1*w1 + x2*w2
    if tmp <= theta:
        return 0
    elif tmp > theta:
        return 1
```
w1, w2, theta를 각각 0.5, 0.5, 0.7로 설정하면 위의 진리표와 동일한 입력 및 출력을 확인할 수 있습니다.

```python
print(AND(0,0))
print(AND(1,0))
print(AND(0,1))
print(AND(1,1))
```
```cmd
0
0
0
1
```

입력값과 가중치의 곱을 편리하게 하기 위해 데이터 타입을 넘파이 어레이로 바꿉니다. 또한 theta를 -b로 바꿈으로써 바이어스의 의미가 더욱 명확해지도록 합니다.

```python
# 위의 퍼셉트론에서 배열로 된 가중치, 바이어스 추가
# 밑 위의 식에서 theta를 -b 로 바꿈
def AND(x1, x2):
    x = np.array([x1, x2])
    w = np.array([0.5, 0.5])
    b = -0.7

    tmp = np.sum(w*x) + b

    if tmp <= 0:
        return 0
    else:
        return 1
```

### **2. NAND, OR 게이트**

위의 AND 게이트와 같은 방식으로 NAND 게이트와, OR 게이트도 만들 수 있습니다.

**NAND 게이트의 진리표**
<table class="tftable" border="1">
<tr><th>x1</th><th>x2</th><th>y</th></tr>
<tr><td>0</td><td>0</td><td>1</td></tr>
<tr><td>1</td><td>0</td><td>1</td></tr>
<tr><td>0</td><td>1</td><td>1</td></tr>
<tr><td>1</td><td>1</td><td>0</td></tr>
</table>




```python
def NAND(x1, x2):
    x = np.array([x1, x2])
    w = np.array([-0.5, -0.5])
    b = 0.7

    tmp = np.sum(w*x) + b

    if tmp <= 0:
        return 0
    else:
        return 1
```


**OR 게이트의 진리표**
<table class="tftable" border="1">
<tr><th>x1</th><th>x2</th><th>y</th></tr>
<tr><td>0</td><td>0</td><td>0</td></tr>
<tr><td>1</td><td>0</td><td>1</td></tr>
<tr><td>0</td><td>1</td><td>1</td></tr>
<tr><td>1</td><td>1</td><td>1</td></tr>
</table>

```python
def OR(x1, x2):
    x = np.array([x1, x2])
    w = np.array([0.5, 0.5])
    b = -0.2

    tmp = np.sum(w*x) + b

    if tmp <= 0:
        return 0
    else:
        return 1
```

### **3. XOR 게이트**

**XOR 게이트의 진리표**
<table class="tftable" border="1">
<tr><th>x1</th><th>x2</th><th>y</th></tr>
<tr><td>0</td><td>0</td><td>0</td></tr>
<tr><td>1</td><td>0</td><td>1</td></tr>
<tr><td>0</td><td>1</td><td>1
</td></tr>
<tr><td>1</td><td>1</td><td>0</td></tr>
</table>

한 개의 퍼셉트론으로는 XOR 게이트를 표현하는 것이 불가능합니다. 하지만 다층 퍼셉트론을 이용하면 XOR을 표현할 수 있습니다.    

입력 값(x1, x2)을 NAND와 XOR게이트에 넣고, 두 게이트의 출력 값(s1, s2)을 AND 게이트의 입력 값으로 넣으면 아래의 진리표와 같이 XOR 게이트를 표현할 수 있습니다.



<table class="tftable" border="1">
<tr><th>x1</th><th>x2</th><th>s1</th><th>s2</th><th>y</th></tr>
<tr><td>0</td><td>0</td><td>1</td><td>0</td><td>0</td></tr>
<tr><td>1</td><td>0</td><td>1</td><td>1</td><td>1</td></tr>
<tr><td>0</td><td>1</td><td>1</td><td>1</td><td>1</td></tr>
<tr><td>1</td><td>1</td><td>0</td><td>1</td><td>0</td></tr>
</table>

파이썬으로 구현하면 아래와 같습니다.

```python
# AND, NAND, OR 게이트 조합하여 XOR Gate 구하기
def XOR(x1, x2):
    s1 = NAND(x1, x2)
    s2 = OR(x1, x2)
    y = AND(s1, s2)
    return y
```

```python
print(XOR(0,0))
print(XOR(1,0))
print(XOR(0,1))
print(XOR(1,1))
```
```cmd
0
1
1
0
```
