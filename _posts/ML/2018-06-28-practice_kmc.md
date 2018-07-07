---
layout: post
title: "k-means Clustering 연습"
category: "machine_learning"
tag:
  - "data science"
  - "machine learning"
---

### **대학교 분류하기 (k-means clustering)**


unsupervised learning algorithm인 k-means clustering을 이용하여, 대학교들을 두가지 유형으로 분류하고자 합니다. 사용하는 데이터에서는 사립/공립이 표시되어 있지만, 여기서는 단지 모델링 후에 결과를 비교하고 참고하는 용도로만 사용할 것입니다.  

데이터는 수강중인 udemy 강의 내용에 포함된 것입니다.





#### **Import Libraries**

먼저 일반적으로 사용하는 라이브러리들을 임포트합니다.

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
%matplotlib inline
```


#### **Get the Data**
파일을 불러와서, 간단히 살펴보도록 하겠습니다.


```python
df = pd.read_csv('College_Data',index_col=0)
```

```python
df.head()
```
![k_means_1]({{ site.url }}\assets\ML\practice\k_means_1.jpg)


```python
df.info()
```
```cmd
<class 'pandas.core.frame.DataFrame'>
Index: 777 entries, Abilene Christian University to York College of Pennsylvania
Data columns (total 18 columns):
Private        777 non-null object
Apps           777 non-null int64
Accept         777 non-null int64
Enroll         777 non-null int64
Top10perc      777 non-null int64
Top25perc      777 non-null int64
F.Undergrad    777 non-null int64
P.Undergrad    777 non-null int64
Outstate       777 non-null int64
Room.Board     777 non-null int64
Books          777 non-null int64
Personal       777 non-null int64
PhD            777 non-null int64
Terminal       777 non-null int64
S.F.Ratio      777 non-null float64
perc.alumni    777 non-null int64
Expend         777 non-null int64
Grad.Rate      777 non-null int64
dtypes: float64(1), int64(16), object(1)
memory usage: 109.3+ KB
```



```python
df.describe()
```

#### **EDA**
데이터의 일부를 시각화 해 보겠습니다.


**(1) scatterplot of Grad.Rate versus Room.Board colored by the Private column**

Grad.Rate : Graduation rate  
Room.Board : Room and board costs  

```python
sns.set_style('whitegrid')
sns.lmplot('Room.Board','Grad.Rate',data=df, hue='Private',
           palette='coolwarm',size=6,aspect=1,fit_reg=False)
```
![k_means_2]({{ site.url }}\assets\ML\practice\k_means_2.png)


**(2) scatterplot of F.Undergrad versus Outstate colored by the Private column**  

F.Undergrad : Number of fulltime undergraduates   
Outstate : Out-of-state tuition  


```python
sns.set_style('whitegrid')
sns.lmplot('Outstate','F.Undergrad',data=df, hue='Private',
           palette='coolwarm',size=6,aspect=1,fit_reg=False)
```
![k_means_3]({{ site.url }}\assets\ML\practice\k_means_3.png)



**(3) stacked histogram showing Out of State Tuition based on the Private column**


```python
sns.set_style('darkgrid')
g = sns.FacetGrid(df,hue="Private",palette='coolwarm',size=6,aspect=2)
g = g.map(plt.hist,'Outstate',bins=20,alpha=0.7)
```
![k_means_4]({{ site.url }}\assets\ML\practice\k_means_4.png)

**(4) stacked histogram showing Graduation rate based on the Private column**

```python
sns.set_style('darkgrid')
g = sns.FacetGrid(df,hue="Private",palette='coolwarm',size=6,aspect=2)
g = g.map(plt.hist,'Grad.Rate',bins=20,alpha=0.7)

```
![k_means_5]({{ site.url }}\assets\ML\practice\k_means_5.png)
위의 히스토그램을 보시면 졸업률이 100%가 넘는 것이 있습니다.  
이 값을 100으로 바꾼 후 다시 그래프를 그리겠습니다.


```python
# 졸업률이 100% 이상인 열을 찾습니다.
df[df['Grad.Rate'] > 100]
```


```python
# Cazenovia College의 졸업률을 100으로 바꿉니다.
df['Grad.Rate']['Cazenovia College'] = 100
```

다시 히스토그램을 그려보면 졸업률의 최대 범위가 100인 것을 알 수 있습니다.
```python
sns.set_style('darkgrid')
g = sns.FacetGrid(df,hue="Private",palette='coolwarm',size=6,aspect=2)
g = g.map(plt.hist,'Grad.Rate',bins=20,alpha=0.7)
```
![k_means_6]({{ site.url }}\assets\ML\practice\k_means_6.png)



#### **K Means Cluster Creation**


```python
from sklearn.cluster import KMeans
```

클러스터를 2개 가진 인스턴스를 생성합니다.
```python
kmeans = KMeans(n_clusters=2)
```

데이터를 학습합니다.
```python
kmeans.fit(df.drop('Private',axis=1))
```

```cmd
KMeans(copy_x=True, init='k-means++', max_iter=300, n_clusters=2, n_init=10,
    n_jobs=1, precompute_distances='auto', random_state=None, tol=0.0001,
    verbose=0)
```

클러스터의 center vectors를 확인합니다.

```python
kmeans.cluster_centers_
```

```cmd
array([[  1.81323468e+03,   1.28716592e+03,   4.91044843e+02,
          2.53094170e+01,   5.34708520e+01,   2.18854858e+03,
          5.95458894e+02,   1.03957085e+04,   4.31136472e+03,
          5.41982063e+02,   1.28033632e+03,   7.04424514e+01,
          7.78251121e+01,   1.40997010e+01,   2.31748879e+01,
          8.93204634e+03,   6.51195815e+01],
       [  1.03631389e+04,   6.55089815e+03,   2.56972222e+03,
          4.14907407e+01,   7.02037037e+01,   1.30619352e+04,
          2.46486111e+03,   1.07191759e+04,   4.64347222e+03,
          5.95212963e+02,   1.71420370e+03,   8.63981481e+01,
          9.13333333e+01,   1.40277778e+01,   2.00740741e+01,
          1.41705000e+04,   6.75925926e+01]])
```

#### **Evaluation**
k means clustering은 unsupervised learning algorithm이므로 실제 상황에서 레이블이 없는 경우에는 평가 자체가 불가합니다. 이 프로젝트는 그냥 연습이므로, 그냥 평가해보도록 하겠습니다.  


데이터에 Cluster라는 열을 만들어 실제로 사립학교면 1, 공립이면 0을 입력하도록 하겠습니다.


```Python
def converter(cluster):
    if cluster=='Yes':
        return 0
    else:
        return 1

    # 원래 Yes일 경우 1이었으나,
    # 모델링 결과를 보니 공립이 1의 값을 가지는 것 같아 바꾸었음.
```
```python
df['Cluster'] = df['Private'].apply(converter)
```

이제 모델에서 나온 레이블과 실제 사립/공립 데이터를 비교해보겠습니다.

```python
from sklearn.metrics import confusion_matrix,classification_report
print(confusion_matrix(df['Cluster'],kmeans.labels_))
print(classification_report(df['Cluster'],kmeans.labels_))
```


```cmd
[[531  34]
 [138  74]]
             precision    recall  f1-score   support

          0       0.79      0.94      0.86       565
          1       0.69      0.35      0.46       212

avg / total       0.76      0.78      0.75       777
```
