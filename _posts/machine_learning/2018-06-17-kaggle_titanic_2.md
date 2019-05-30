---
layout: post
title: "Kaggle - 타이타닉 생존자 예측하기 (2)"
category: "machine_learning"
tag:
  - "kaggle"
  - "data science"
  - "machine learning"
---



- [Kaggle - 타이타닉 생존자 예측하기 EDA / Feature Engineering (1)]({{ "/machine_learning/2018/06/16/kaggle_titanic_1.html" | absolute_url }})  
- [Kaggle - 타이타닉 생존자 예측하기 EDA / Feature Engineering (2)]({{ "/machine_learning/2018/06/17/kaggle_titanic_2.html" | absolute_url }})


___


### **EDA / Feataure Engineering (2)**

#### 5. Fare
Fare는 운임을 나타냅니다. 단위는 따로 표시되어있지 않은데, 아마 파운드로 생각이 됩니다. 트레이닝 데이터셋에는 Missing value가 없고 test 데이터셋에만 한 개의 missing value가 있습니다. 분포도를 한번 살펴보도록 하겠습니다.

```python
import seaborn as sns
sns.distplot(train['Fare'])
```
![distplot]({{ site.url }}\assets\ML\titanic\titanic_1.png)

한 개의 Missing Value는 중위값으로 채우도록 하겠습니다.

```python
for dataset in full_data:
    dataset['Fare'] = dataset['Fare'].fillna(train['Fare'].median())
```



#### 6. Age  
Age는 탑승자의 나이를 나타냅니다. 트레이닝 데이터셋에는 177개의 나이 데이터가 누락되어 있습니다. 이 빠진 데이터들을 다른 레퍼런스에서처럼 평균값이나 평균에서 표준편차 범위내의 난수 등으로 집어넣지않고, 이미 살펴본 Pclass와 나이의 관계를 먼저 살펴본 후 적절해보이는 방법으로 빠진 값을 채우도록 하겠습니다.  

먼저 seaborn으로 티켓 클래스에 따라 나이가 어떤 통계값을 가지는지 boxplot으로 살펴보겠습니다.

```python
import matplotlib.pyplot as plt

plt.figure(figsize=(12, 7))
sns.boxplot(x='Pclass',y='Age',data=train)
```
![boxplot]({{ site.url }}\assets\ML\titanic\titanic_2.png)

위 boxplot에서 티켓 클래스가 높을수록 평균 나이가 높다는 사실을 알 수 있습니다. 티켓 클래스별 평균 나이는 아래와 같습니다.

```python
train[['Pclass','Age']].groupby(['Pclass']).mean()
```
```cmd
Pclass	Age
0	1	38.233441
1	2	29.877630
2	3	25.140620
```

빠진 나이 값은 티켓 클래스에 따라 평균을 집어넣도록 하겠습니다. 여기서는 함수를 만들어 적용하도록 하겠습니다.
```python
def impute_age(cols):
    Age = cols[0]
    Pclass = cols[1]

    if pd.isnull(Age):
        if Pclass == 1:
            return 38
        elif Pclass == 2:
            return 30
        else:
            return 25
    else:
        return Age
```
```python
for dataset in full_data:
    dataset['Age'] = dataset[['Age','Pclass']].apply(impute_age,axis=1)
```
Age의 모든 빠진 값들을 티켓 클래스에 따른 나이의 평균으로 채웠습니다.




#### 7. Name  
Name 행에는 Mr, Miss와 같은 타이틀이 이름 안에 포함되어 있습니다. 따라서 Name에서 타이틀을 추출해내는 작업이 필요합니다. 여기서는 정규표현식을 사용하는데요, 타이틀을 추출해내는 함수는 아래와 같습니다

```python
#먼저 정규표현식 사용을 위한 re (Regular Expression) 모듈을 임포트합니다.
import re as re

# get_title`함수를 정의하고 name을 파라미터로 받습니다.  
def get_title(name):
  # re.search 모듈은 두번째 파라미터인 name에서
  # 첫번째 파라미터인 ' ([A-Za-z]+)\.'를 찾아보고 리턴합니다. (MatchObject)
	title_search = re.search(' ([A-Za-z]+)\.', name)

  # 만약 title_search이 있으면 group(1)을 리턴합니다.
	if title_search:
		return title_search.group(1)
	return ""

  # ' ([A-Za-z]+)\.'은 "공백 + 1개 이상의 알파벳 + 마침표" 의 형태를 나타냅니다.

  # [A-Za-z]는 A부터 z까지의 임의의 알파벳, 그 뒤의 '+'는 알파벳이 한개 이상 붙어 있는 것을 의미하고
  # 이들을 감싸고 있는 소괄호는 그룹화를 하기 위한 것입니다.
  # 소괄호 앞의 공백은 진짜 공백을 의미하고, 괄호 뒤의 `\.`은 마침표`.`를 표현하기 위함이
```

위의 `get_title` 함수를 정의한 후 `apply` 메쏘드를 통해 데이터셋에 적용하여 Title이라는 새 열을 만듭니다.
```python
for dataset in full_data:
    dataset['Title'] = dataset['Name'].apply(get_title)

print(pd.crosstab(train['Title'], train['Sex']))
```

```cmd
Sex       female  male
Title                 
Capt           0     1
Col            0     2
Countess       1     0
Don            0     1
Dr             1     6
Jonkheer       0     1
Lady           1     0
Major          0     2
Master         0    40
Miss         182     0
Mlle           2     0
Mme            1     0
Mr             0   517
Mrs          125     0
Ms             1     0
Rev            0     6
Sir            0     1
```

통일성을 위해 Mlle(Mademoiselle)과 Ms(Mistress) 는 Miss로, Mme(Madam)은 Mrs로 바꾸고,
기타 다른 타이틀들은 Rare라는 항목을 만들어서 하나로 모읍니다.

```python
for dataset in full_data:
    dataset['Title'] = dataset['Title'].replace(['Lady', 'Countess','Capt', 'Col', 'Don', 'Dr', 'Major', 'Rev', 'Sir', 'Jonkheer', 'Dona'], 'Rare')
    dataset['Title'] = dataset['Title'].replace('Mlle', 'Miss')
    dataset['Title'] = dataset['Title'].replace('Ms', 'Miss')
    dataset['Title'] = dataset['Title'].replace('Mme', 'Mrs')

print(pd.crosstab(train['Title'], train['Sex']))
```

```cmd
Sex     female  male
Title               
Master       0    40
Miss       185     0
Mr           0   517
Mrs        126     0
Rare         3    20
```

이제 타이틀과 생존률과의 관계를 살펴보겠습니다.
```python
print (train[['Title', 'Survived']].groupby(['Title'], as_index=False).mean())
```
```cmd
Title  Survived
0  Master  0.575000
1    Miss  0.702703
2      Mr  0.156673
3     Mrs  0.793651
4    Rare  0.347826
```


#### 8. 기타

객실 번호를 보여주는 `Cabin` 열은 객실이 있고 없고로 구분하여 데이터를 사용해 볼 수도 있지만, 여기서는 따로 이용하지 않겠습니다.
