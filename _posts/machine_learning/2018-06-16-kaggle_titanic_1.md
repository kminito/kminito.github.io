---
layout: post
title: "Kaggle - 타이타닉 생존자 예측하기 (1)"
category: "machine_learning"
tag:
  - "kaggle"
  - "data science"
  - "machine learning"
---

- [Kaggle - 타이타닉 생존자 예측하기 EDA / Feature Engineering (1)]({{ "/machine_learning/2018/06/16/kaggle_titanic_1.html" | absolute_url }})  
- [Kaggle - 타이타닉 생존자 예측하기 EDA / Feature Engineering (2)]({{ "/machine_learning/2018/06/17/kaggle_titanic_2.html" | absolute_url }})


___


### **개요**  
 타이타닉 침몰 사건은 1912년 4월 10일 영국의 사우샘프턴을 떠나 미국 뉴욕으로 향하던 'RMS 타이타닉'이라는 여객선이 빙산과 충돌하여 침몰한 사건으로, 총 탑승자 2,223명 중 1,514이 사망했습니다. [(위키피디아)](https://ko.wikipedia.org/wiki/RMS_%ED%83%80%EC%9D%B4%ED%83%80%EB%8B%89)  

 당시 타이타닉 탑승자의 정보를 바탕으로 생존자를 예측하는 문제를 캐글에서 입문용으로 제공하고 있어서, 저도 연습 겸 배운 내용 복습 겸 시작하게 되었습니다. 캐글 홈페이지에 있는 다른 분들이 쓰신 글들을 바탕으로 따라하면서 제가 따로 궁금한 것들도 해보도록 하겠습니다.


문제 및 데이터셋 : [Titanic: Machine Learning from Disaster](https://www.kaggle.com/c/titanic)   
참고한 글 : [Titanic Data Science Solutions](https://www.kaggle.com/startupsci/titanic-data-science-solutions), [Titanic best working Classifier](https://www.kaggle.com/sinakhorami/titanic-best-working-classifier)
, [Introduction to Ensembling/Stacking in Python](https://www.kaggle.com/arthurtok/introduction-to-ensembling-stacking-in-python)  




### **데이터 불러오기**  


```python
import numpy as np
import pandas as pd
%matplotlib inline

train = pd.read_csv('train.csv')
test = pd.read_csv('test.csv')
full_data = [train, test]
```

두 파일 모두 불러온 후 데이터를 살펴보았습니다.
```python
print(train.info())
print('\n')
print(test.info())
```

```cmd
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 891 entries, 0 to 890
Data columns (total 12 columns):
PassengerId    891 non-null int64
Survived       891 non-null int64
Pclass         891 non-null int64
Name           891 non-null object
Sex            891 non-null object
Age            714 non-null float64
SibSp          891 non-null int64
Parch          891 non-null int64
Ticket         891 non-null object
Fare           891 non-null float64
Cabin          204 non-null object
Embarked       889 non-null object
dtypes: float64(2), int64(5), object(5)
memory usage: 66.2+ KB
None


<class 'pandas.core.frame.DataFrame'>
RangeIndex: 418 entries, 0 to 417
Data columns (total 11 columns):
PassengerId    418 non-null int64
Pclass         418 non-null int64
Name           418 non-null object
Sex            418 non-null object
Age            332 non-null float64
SibSp          418 non-null int64
Parch          418 non-null int64
Ticket         418 non-null object
Fare           417 non-null float64
Cabin          91 non-null object
Embarked       418 non-null object
dtypes: float64(2), int64(4), object(5)
memory usage: 27.8+ KB
None
```

train는 891개, test는 418개의 데이터로 이루어져 있고, Age나 Cabin 등은 missing value가 있는 것을 알 수 있습니다. 하나씩 살펴보도록 하겠습니다.

### **EDA / Feataure Engineering (1)**
#### 1. Survived
Survived는 생존 여부를 표시한 것입니다. 값이 1이면 생존을 의미합니다.

```python
train['Survived'].value_counts()
```
```cmd
0    549
1    342
Name: Survived, dtype: int64
```
891명 중 342명이 살아남은 것을 알 수 있습니다.

#### 2. Pclass  
Pclass는 티켓 클래스로, 1은 1등석, 2는 2등석, 3은 3등석을 의미합니다. 문제에 따르면, 티켓 클래스로 사회경제적 지위를 알 수 있다고 합니다.  
빠진 데이터가 없고, 이미 정수형이므로 바로 어떤 관계가 있는지 살펴보겠습니다.


```python
train[['Pclass', 'Survived']].groupby(['Pclass']).mean()
```

```cmd
Pclass    Survived   
1         0.629630
2         0.472826
3         0.242363
```

티켓 클래스가 높을수록 생존할 확률이 높은 것을 알 수 있습니다.  

평균을 바로 구하지 않고, 아래와 같이 각 티켓 클래스별로 살아남은 사람 수를 구한 후 각 티켓 별 총 인원으로 나누어 주어도 같은 값을 확인할 수 있습니다.
```python
train.groupby(['Pclass']).count()['Survived']
```
```python
train['Pclass'].value_counts()
```

#### 3. Sex  
성별도 같은 위와 같은 방식으로 확인해 보겠습니다.

```python
train[["Sex", "Survived"]].groupby(['Sex'], as_index=False).mean()
# as_index = False 는 결과의 인덱스가 두 줄로 나오는 것이 불편해서 추가함
````
```cmd
Sex	Survived
0	female	0.742038
1	male	0.188908
```
여성의 생존확률이 월등히 높습니다.

#### 4. SibSp and Parch
SibSp는 함께 탑승한 형, 누나, 동생 등 형제자매(Siblings)의 숫자와 아내, 남편 등의 배우자 (Spouse)의 숫자를 합친 것이고, Parch는 함께 탑승한 아버지, 어머니, 아들, 딸 등 직계가족(Parents + Children)의 숫자를 합친 것입니다.

먼저 데이터 안에 어떤 값들이 있는지 알아보겠습니다.
```python
print(train['SibSp'].unique())
print(train['Parch'].unique())
```
```cmd
[1 0 3 4 2 5 8]
[0 1 2 5 3 4 6]
```  


위의 티켓클래스, 성별과 같은 방식으로 한번 생존자의 비율을 구해보면  


```python
print(train[["SibSp", "Survived"]].groupby(['SibSp']).mean())
```
```cmd
SibSp	Survived
0	0	0.345395
1	1	0.535885
2	2	0.464286
3	3	0.250000
4	4	0.166667
5	5	0.000000
6	8	0.000000
```
위의 경우들과는 달리 뚜렷한 관계가 보이지 않습니다. Parch도 마찬가지입니다.


그래서 이번에는 SibSp와 Parch를 합쳐 함께 탄 가족들의 숫자를 보여주는 열을 새로 만들어보도록 하겠습니다. 열의 이름은 ```FamilySize```이고, 가족 없이 혼자 탄 경우에는 1이 되도록 하기 위해 ```Sibsp```과 ```Parch```의 값을 합친 후 1이 되도록 할 것입니다.


```python
for dataset in full_data:
    dataset['FamilySize'] = dataset['SibSp'] + dataset['Parch'] + 1
```
```python
print (train[['FamilySize', 'Survived']].groupby(['FamilySize'], as_index=False).mean())
```

```cmd
FamilySize	Survived
0	1	0.303538
1	2	0.552795
2	3	0.578431
3	4	0.724138
4	5	0.200000
5	6	0.136364
6	7	0.333333
7	8	0.000000
8	11	0.000000
```

아까와 달라진 것이 있다면, 혼자 올 때 보다 다른 가족과 함께 왔을 때 네명까지는 생존 확률이 증가한다는 것입니다.   

조금 더 나아가, 혼자 탔는지 아닌지를 보여주는 열을 하나 더 추가하도록 하겠습니다.

```python
for dataset in full_data:
    dataset['IsAlone'] = 0
    dataset.loc[dataset['FamilySize'] == 1, 'IsAlone'] = 1
```
```python
train[['IsAlone', 'Survived']].groupby(['IsAlone'], as_index=False).mean()
```

```cmd
IsAlone	Survived
0	0	0.505650
1	1	0.303538
```


#### 5. Embarked  
Embared는 탑승한 항구를 나타냅니다. C는 Cherbourg, Q는 Queenstown, S는 Southampton 입니다. 탑승 항구같은 경우에는 빠진 값(Missing value)이 2개 있습니다. 먼저 어느 항구에서 가장 많이 탑승했는지 확인한 후, 빠진 값을 최빈값으로 채우도록 하겠습니다.

```python
train['Embarked'].value_counts()
```
```cmd
S    644
C    168
Q     77
Name: Embarked, dtype: int64
```

S인  싸우쓰햄튼이 가장 많으므로, 없는 값 두개를 S로 채우도록 하겠습니다.
```python
for dataset in full_data:
  dataset['Embarked'] = dataset['Embarked'].fillna('S')
```

이제 다시 항구별로 생존률을 확인해보겠습니다.
```python
train[['Embarked', 'Survived']].groupby(['Embarked'], as_index=False).mean()
```

```cmd
Embarked	Survived
0	C	0.553571
1	Q	0.389610
2	S	0.339009
```
항구별로 차이가 있긴 한데, 사실 직관적으로는 어떤 차이가 있어서 그런 건지 잘 모르겠습니다. 탑승하는 항구에 따라 배 안에서의 좌석 위치가 다른가 싶기도 합니다.
