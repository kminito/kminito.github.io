---
layout: note_layout
title : LinearRegression
---


**seaborn**

먼저 기본적인 것들 임포트 하고


```Python

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
%matplotlib inline

```

상태가 어떤지 살펴본다. 빠진 것은 없는지, 데이터 형태는 어떤지 등

```python
USAhousing = pd.read_csv('USA_Housing.csv')
USAhousing.head()
USAhousing.info()
USAhousing.describe()

대충 어케 생겼는지도
```

```
디자인 변경
sns.set_palette("GnBu_d")
sns.set_style('whitegrid')

두가지 뽑아서 분포 비교
sns.jointplot(x='Time on Website',y='Yearly Amount Spent',data=customers)


sns.pairplot(customers)
sns.distplot(USAhousing['Price'])
sns.heatmap(USAhousing.corr())
```

컬럼을 뽑아서 X, y에 넣고
```
USAhousing.columns

X = USAhousing[['Avg. Area Income', 'Avg. Area House Age', 'Avg. Area Number of Rooms',
               'Avg. Area Number of Bedrooms', 'Area Population']]
y = USAhousing['Price']

```

데이터를 스플릿한다
```
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.4, random_state=101)

```

그다음 모델을 만들어서 돌리고 확인한다


```

from sklearn.linear_model import LinearRegression

lm = LinearRegression()
lm.fit(X_train,y_train)

coeff_df = pd.DataFrame(lm.coef_,X.columns,columns=['Coefficient'])
coeff_df

```

이제 모델을 적용해본다


```

predictions = lm.predict(X_test)
plt.scatter(y_test,predictions)


sns.distplot((y_test-predictions),bins=50);



from sklearn import metrics

print('MAE:', metrics.mean_absolute_error(y_test, predictions))
print('MSE:', metrics.mean_squared_error(y_test, predictions))
print('RMSE:', np.sqrt(metrics.mean_squared_error(y_test, predictions)))
