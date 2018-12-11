---
layout: post
title: "코인 가격 예측 스터디노트 - 3. RNN(LSTM) 모델링"
category: "machine_learning"
tag:
  - "python"
  - "data science"
  - "machine learning"
  - "deep learning"
---
_스터디파이에서 '딥러닝으로 주식/코인 가격 예측하기'를 참여하며 공부한 내용을 정리한 것입니다.._

학습 자료 : [Creating a Cryptocurrency-predicting finance recurrent neural network](https://pythonprogramming.net/cryptocurrency-recurrent-neural-network-deep-learning-python-tensorflow-keras/)  


큰 그림은 이렇습니다. 우리가 사용할 데이터에는 [비트코인, 라이트코인, 이더리움]의 [종가, 거래량]이 [1분 간격]으로 저장되어 있습니다. 이 데이터를 바탕으로 학습하여 세 코인 중 하나의 가격 등락을 예측하는 것입니다. 구체적으로 예를 들자면, 1월 1일 00시 00분의 비트코인 가격이 3분 후에 올라 있을지 떨어져 있을지를 예측하는 것입니다. 다른 코인의 정보 또한 feature로 사용이 됩니다.

자세한 내용은 위의 링크에 아주 길게 있습니다.  

## RNN으로 코인 가격 등락 예측 ##

데이터 파일은 총 세개("BTC-USD", "LTC-USD", "ETH-USD")인데, 일단 하나를 열어봅니다.

```py
import numpy as np
import pandas as pd


df = pd.read_csv("crypto_data/LTC-USD.csv", names = ['time', 'low', 'high', 'open', 'close', 'volume'])
print(df.head())
```
```cmd
time        low       high       open      close      volume
0  1528968660  96.580002  96.589996  96.589996  96.580002    9.647200
1  1528968720  96.449997  96.669998  96.589996  96.660004  314.387024
2  1528968780  96.470001  96.570000  96.570000  96.570000   77.129799
3  1528968840  96.449997  96.570000  96.570000  96.500000    7.216067
4  1528968900  96.279999  96.540001  96.500000  96.389999  524.539978
```
이렇게 생겼네요. row data는 저가, 고가, 시가, 종가, 거래량이 모두 포함되어 있습니다. 따라서 이후에 데이터를 처리하는 과정에서 필요 없는 컬럼은 제거됩니다.

#### **데이터 처리**

```py
# 데이터를 넣을 빈 데이터프레임을 만듦
main_df = pd.DataFrame()
```
```py
#파라미터를 변수로 설정
SEQ_LEN = 60 # 몇분짜리를 한 덩어리로 학습시킬건지 (ex 60분)
FUTURE_PERIOD_PREDICT = 3 #몇 분 후 가격을 기준으로 등락을 결정할 건지 (3분 후)
RATIO_TO_PREDICT = "LTC-USD" # 어느 코인 가격을 예측?
```


```python
ratios = ["BTC-USD", "LTC-USD", "ETH-USD"]
for ratio in ratios:
    dataset = f'training_datas/{ratio}.csv'
    df = pd.read_csv(dataset, names=['time','low','high','open','close','volume'])

    df.rename(columns={"close": f"{ratio}_close", "volume" : f"{ratio}_volume"}, inplace=True)


    # 일단 time을 index로 두고, 컬럼은 종가와 거래량만 남김
    df.set_index("time", inplace=True)
    df = df[[f"{ratio}_close", f"{ratio}_volume"]]
    # 그리고 이것들을 join한다 -> 여러 코인 데이터를 하나의 df에 join
    if len(main_df)==0:
        main_df = df
    else:
        main_df = main_df.join(df)

main_df.fillna(method="ffill",inplace=True)
main_df.dropna(inplace=True)
print(main_df.head())


## target값,즉 등락을 표시하기 위한 함수
def classify(current, future): #현재와 미래 가격을 인자로 받아서
    if float(future) > float(current):
        return 1 # 오르면 1
    else:
        return 0 # 떨어지면 0

#여기서 미래의 가격이란 3분 후의 가격
main_df['future'] = main_df[f'{RATIO_TO_PREDICT}_close'].shift(-FUTURE_PERIOD_PREDICT)
#그래서 위의 classfy 함수를 돌려 target value를 구한다
main_df['target'] = list(map(classify, main_df[f'{RATIO_TO_PREDICT}_close'], main_df['future']))


# test train split
#시간순으로 마지막 5%에 해당하는 데이터를 테스트 데이터로 쓴다
times = sorted(main_df.index.values) # get the times
last_5pct = sorted(main_df.index.values)[-int(0.05*len(times))]

# validation_main_df는 마지막 5% 이내 시간
validation_main_df = main_df[(main_df.index >= last_5pct)]
# main_df는 앞의 95% 시간에 해당하는 데이터가 들어감
main_df = main_df[(main_df.index < last_5pct)]
```
```cmd
BTC-USD_close  BTC-USD_volume  LTC-USD_close  LTC-USD_volume  \
time                                                                       
1528968720    6487.379883        7.706374      96.660004      314.387024   
1528968780    6479.410156        3.088252      96.570000       77.129799   
1528968840    6479.410156        1.404100      96.500000        7.216067   
1528968900    6479.979980        0.753000      96.389999      524.539978   
1528968960    6480.000000        1.490900      96.519997       16.991997   

ETH-USD_close  ETH-USD_volume  
time                                       
1528968720      486.01001       26.019083  
1528968780      486.00000        8.449400  
1528968840      485.75000       26.994646  
1528968900      486.00000       77.355759  
1528968960      486.00000        7.503300  
```
위의 결과는 세 코인의 데이터를 합쳤을 때 어떤지 보려고 프린트한 것.  


이제 합쳐진 데이터를 전처리하기 위한 함수를 정의합니다.
```py
from sklearn import preprocessing
from collections import deque
import random
```

```python
def preprocess_df(df):
    df = df.drop("future", axis=1) #필요없는 컬럼은 제거 (왜 위에서 안 지웠지?)

    #코인마다 가격이 닫르므로 스케일링을 해주어야 합니다.
    #여기서는 percent chnage를 하고 다시 sklearn.preprocessing.scale를 썼는디
    #구체적으로 어떤식으로 스케일링을 하는지는 찾아보지 않았습니다.
    for col in df.columns:
        if col != "target":
            df[col] = df[col].pct_change() #퍼센트 체인지
            df.dropna(inplace=True)
            df[col] = preprocessing.scale(df[col].values)

    # sentdex가 na는 잘 생긴다고 안전빵으로 dropna 한다고 했습니다.
    df.dropna(inplace=True)

    #자 이제 한 묶음으로 때려넣을 데이터를 묶습니다.
    sequential_data = []
    #deque는 배열에 새로운 데이터가 들어와도 length를 유지해주는 친구입니다.
    #배열이 꽉 찼는데 새로 뭐가 들어오면 맨 뒤에는 팅겨나가는 방식으로
    #크기를 유지합니다. 여기서는 SEQ_LEN, 즉 60개가 배열 크기가 됩니다.
    prev_days = deque(maxlen=SEQ_LEN)


    for i in  df.values:
        prev_days.append([n for n in i[:-1]]) #타겟 빼놓고 나머지를 pre_days에 어펜드
        if len(prev_days) == SEQ_LEN: #pre_days에 60개가 꽉차면
            sequential_data.append([np.array(prev_days),i[-1]]) #sequential_data에 target과 함께 어펜드
    random.shuffle(sequential_data)


    ## blancing
    # target이 1이면 사라, 0이면 팔아라,의 신호인데
    # 데이터 불균형이 있을 수 있으므로 그것을 맞추는 작업
    buys = []
    sells = []

    for seq, target in sequential_data:
        #타겟이 0이면 팔아라에 넣고
        if target == 0:
            sells.append([seq, target])
        #타겟이 1이면 사라에 넣는다
        elif target == 1:
            buys.append([seq, target])

    random.shuffle(buys)
    random.shuffle(sells)

    # 두 데이터의 숫자를 맞춰주려고 함(둘 중 숫자가 작은 것으로)
    lower = min(len(buys), len(sells))

    #위에서 미리 셔플해놨으므로 그냥 슬라이싱 하면 됨.
    buys = buys[:lower]
    sells = sells[:lower]

    # 둘을 합쳐서 학습 데이터에 넣고
    sequential_data = buys+sells
    random.shuffle(sequential_data)

    # test train split
    X = []
    y = []

    for seq, target in sequential_data:
        X.append(seq)
        y.append(target)

    return np.array(X), y

```

위에서 만든 함수로 데이터 전처리를 합니다.
```python
train_x, train_y = preprocess_df(main_df)
validation_x, validation_y = preprocess_df(validation_main_df)
```

#### **모델링**


하이퍼 파라미터를 설정합니다.
```python
import time
EPOCHS = 10
BATCH_SIZE=64

#요거는 로그를 저장할 때 쓸 이름입니다.
#하이퍼 파라미터를 이름에 들어가게 하는 건 좋은 방법인 것 같아요.
#시간도 집어넣어서 겹치는 일이 없도록 합니다.
NAME = f"{SEQ_LEN}-SEQ-{FUTURE_PERIOD_PREDICT}-PRED-{int(time.time())}"
```

```python
import tensorflow as tf
from keras.models import Sequential
from keras.layers import Dense, Dropout, LSTM, CuDNNLSTM, BatchNormalization
from keras.callbacks import TensorBoard
from keras.callbacks import ModelCheckpoint, ModelCheckpoint
from keras.optimizers import Adam
```
```cmd
Using TensorFlow backend.
```

신경망 구성
```python
model = Sequential()

model.add(CuDNNLSTM(128, input_shape=(train_x.shape[1:]), return_sequences=True))
model.add(Dropout(0.2)) #오버피팅을 막고자 일부를 버림
model.add(BatchNormalization())

model.add(CuDNNLSTM(128, return_sequences=True))
model.add(Dropout(0.1))
model.add(BatchNormalization())

model.add(CuDNNLSTM(128))
model.add(Dropout(0.2))
model.add(BatchNormalization())

model.add(Dense(32, activation = 'relu'))
model.add(Dropout(0.2))

model.add(Dense(2, activation='softmax'))

opt = Adam(lr=0.001, decay=1e-6)

# Compile model
model.compile(
    loss='sparse_categorical_crossentropy',
    optimizer=opt,
    metrics=['accuracy']
)
```
**tensorboard 설정**

```python
logs 폴더에 로그를 저장합니다.
tensorboard = TensorBoard(log_dir="logs/{}".format(NAME))
```  

```python

filepath = "RNN_Final-{epoch:02d}-{val_acc:.3f}"  
# val_acc가 가장 높을 때의 모델을 자동으로 저장하도록 합니다. (사실 자세히 모름)
checkpoint = ModelCheckpoint("models/{}.model".format(filepath, monitor='val_acc', verbose=1, save_best_only=True, mode='max')) # saves only the best ones
```  

텐서보드를 실행하기 위해 커맨드창에서 가상환경을 활성화하고, 주피터노트북 파일이 있는 곳으로 가서 아래 명령어를 입력합니다. `logdir`은 log가 저장될 곳에 따라서 바꾸면 됩니다.      


    tensorboard --logdir=logs/ --host localhost --port 8088

``TensorBoard 1.11.0 at http://localhost:8088 (Press CTRL+C to quit)``    
라고 뜨면 브라우저에 주소를 입력하고 들어갑니다. 아래와 같은 화면을 볼 수 있습니다.  
    ![tensor_board]({{ site.url }}\assets\study_notes\studypie_1\3_1.PNG)



#### **학습**

```python
# Train model
history = model.fit(
    train_x, train_y,
    batch_size=BATCH_SIZE,
    epochs=EPOCHS,
    validation_data=(validation_x, validation_y),
    callbacks=[tensorboard, checkpoint],
)
```

```cmd
Train on 77926 samples, validate on 3860 samples
Epoch 1/10
77926/77926 [==============================] - 40s 513us/step - loss: 0.7067 - acc: 0.5247 - val_loss: 0.6886 - val_acc: 0.5438
Epoch 2/10
77926/77926 [==============================] - 38s 483us/step - loss: 0.6866 - acc: 0.5482 - val_loss: 0.6806 - val_acc: 0.5661
Epoch 3/10
77926/77926 [==============================] - 38s 483us/step - loss: 0.6837 - acc: 0.5581 - val_loss: 0.6781 - val_acc: 0.5687
Epoch 4/10
77926/77926 [==============================] - 37s 481us/step - loss: 0.6819 - acc: 0.5606 - val_loss: 0.6809 - val_acc: 0.5622
Epoch 5/10
77926/77926 [==============================] - 37s 480us/step - loss: 0.6804 - acc: 0.5655 - val_loss: 0.6743 - val_acc: 0.5707
Epoch 6/10
77926/77926 [==============================] - 37s 473us/step - loss: 0.6781 - acc: 0.5728 - val_loss: 0.6736 - val_acc: 0.5790
Epoch 7/10
77926/77926 [==============================] - 38s 482us/step - loss: 0.6751 - acc: 0.5765 - val_loss: 0.6787 - val_acc: 0.5694
Epoch 8/10
77926/77926 [==============================] - 37s 472us/step - loss: 0.6723 - acc: 0.5812 - val_loss: 0.6734 - val_acc: 0.5754
Epoch 9/10
77926/77926 [==============================] - 47s 599us/step - loss: 0.6683 - acc: 0.5885 - val_loss: 0.6783 - val_acc: 0.5824
Epoch 10/10
77926/77926 [==============================] - 35s 451us/step - loss: 0.6640 - acc: 0.5967 - val_loss: 0.6775 - val_acc: 0.5705
```

```python
# Score model
score = model.evaluate(validation_x, validation_y, verbose=0)
print('Test loss:', score[0])
print('Test accuracy:', score[1])
# Save model
model.save("models/{}".format(NAME))
```
```python
Test loss: 0.6774762500753057
Test accuracy: 0.5704663213670562
```


텐서보드로 보면 이런식  

![tensor_board2]({{ site.url }}\assets\study_notes\studypie_1\3_2.PNG)


#### **총평**
이것저것 바꿔가면서 시도해봤는데 validation accruacy가 58%를 넘지 못함. 그리고 LSTM에 대해 좀 더 공부를 해야할 것 같다. 그리고 이거를 주식에 적용하려면 feature를 좀 더 확보해야 할 것 같다. 연습으로 좋았음.
