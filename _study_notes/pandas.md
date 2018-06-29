---
layout: note_layout
title : pandas

---


**series**

```python

import numpy as np
import pandas as pd

labels = ['a','b','c']
my_list = [10,20,30]
arr = np.array([10,20,30])
d = {'a':10,'b':20,'c':30}


pd.Series(data=my_list)
pd.Series(data=my_list,index=labels)
pd.Series(my_list,labels)


pd.Series(arr)
pd.Series(arr,labels)

pd.Series(d)

# Data in a Series
# A pandas Series can hold a variety of object types:
pd.Series(data=labels)
# Even functions (although unlikely that you will use this)
pd.Series([sum,print,len])

```

**Using an Index**

```python

ser1 = pd.Series([1,2,3,4],index = ['USA', 'Germany','USSR', 'Japan'])              
ser2 = pd.Series([1,2,5,4],index = ['USA', 'Germany','Italy', 'Japan'])   
ser1['USA']

ser1 + ser2

```

**DataFrames**

```python

import pandas as pd
import numpy as np

from numpy.random import randn
np.random.seed(101)

df = pd.DataFrame(randn(5,4),index='A B C D E'.split(),columns='W X Y Z'.split())


```


**Selection and Indexing**

```python


df['W']
# SQL Syntax (NOT RECOMMENDED!)
df.W
type(df['W'])


# Creating a new Column
df['new'] = df['W'] + df['Y']

# Removing Columns

df.drop('new',axis=1)
# Not inplace unless specified!

df.drop('new',axis=1,inplace=True)

df.drop('E',axis=0)




# Selecting Rows

df.loc['A']

df.iloc[2]

df.loc['B','Y']
df.loc[['A','B'],['W','Y']]

```

**Conditional Selection**

```python


df>0 #부울린 리턴


df[df>0] #true인 곳만 값 리턴 - false 는 NaN


df[df['W']>0] # W>0인 행만 리턴

df[df['W']>0]['Y'] # W>0인 행만 리턴한 것에서 Y열만 리턴

df[df['W']>0][['Y','X']]

# for two conditions
df[(df['W']>0) & (df['Y'] > 1)]

```


**More Index Details**


```python

# Reset to default 0,1...n index
df.reset_index()

newind = 'CA NY WY OR CO'.split()
df['States'] = newind

df.set_index('States')

df.set_index('States',inplace=True)



#Multi-Index and Index Hierarchy


# Index Levels
outside = ['G1','G1','G1','G2','G2','G2']
inside = [1,2,3,1,2,3]
hier_index = list(zip(outside,inside))
hier_index = pd.MultiIndex.from_tuples(hier_index)

hier_index

df = pd.DataFrame(np.random.randn(6,2),index=hier_index,columns=['A','B'])
df

df.loc['G1']
df.loc['G1'].loc[1]

df.index.names

df.index.names = ['Group','Num']


# cross section
df.xs('G1')
df.xs(['G1',1])
df.xs(1,level='Num')

```



**handling missing data**

```
df.dropna()
df.dropna(axis=1)
df.dropna(thresh=2)

df.fillna(value='FILL VALUE')
df['A'].fillna(value=df['A'].mean())
```


**Group by**
```
import pandas as pd
# Create dataframe
data = {'Company':['GOOG','GOOG','MSFT','MSFT','FB','FB'],
       'Person':['Sam','Charlie','Amy','Vanessa','Carl','Sarah'],
       'Sales':[200,120,340,124,243,350]}


df = pd.DataFrame(data)


df.groupby('Company')
by_comp = df.groupby("Company")
by_comp.mean()

df.groupby('Company').mean()

by_comp.std()
by_comp.max()
by_comp.count()

by_comp.describe()
by_comp.describe().transpose()

by_comp.describe().transpose()['GOOG']
```

```

#토탈페이베네핏에서 맥스값을 가진 행의 id를 리턴해서 loc에 넣음
df.loc[df['TotalPayBenefits'].idxmax()]
```

**Merging, Joining, and Concatenating**

```
Keep in mind that dimensions should match along the axis you are concatenating on.

pd.concat([df1,df2,df3])
pd.concat([df1,df2,df3],axis=1)
```



Merging
The merge function allows you to merge DataFrames together using a similar logic as merging SQL Tables together. For example:
```

left = pd.DataFrame({'key': ['K0', 'K1', 'K2', 'K3'],
                     'A': ['A0', 'A1', 'A2', 'A3'],
                     'B': ['B0', 'B1', 'B2', 'B3']})

right = pd.DataFrame({'key': ['K0', 'K1', 'K2', 'K3'],
                          'C': ['C0', 'C1', 'C2', 'C3'],
                          'D': ['D0', 'D1', 'D2', 'D3']})    


pd.merge(left,right,how='inner',on='key')

pd.merge(left, right, on=['key1', 'key2'])
pd.merge(left, right, how='outer', on=['key1', 'key2'])
pd.merge(left, right, how='right', on=['key1', 'key2'])
pd.merge(left, right, how='left', on=['key1', 'key2'])


```

Joining
Joining is a convenient method for combining the columns of two potentially differently-indexed DataFrames into a single result DataFrame.


```
left = pd.DataFrame({'A': ['A0', 'A1', 'A2'],
                     'B': ['B0', 'B1', 'B2']},
                      index=['K0', 'K1', 'K2'])

right = pd.DataFrame({'C': ['C0', 'C2', 'C3'],
                    'D': ['D0', 'D2', 'D3']},
                      index=['K0', 'K2', 'K3'])


left.join(right)



```



**Operations**
```


import pandas as pd
df = pd.DataFrame({'col1':[1,2,3,4],'col2':[444,555,666,444],'col3':['abc','def','ghi','xyz']})
df.head()

df['col2'].unique()
df['col2'].nunique()

# 내림차순
df['col2'].value_counts()



#Select from DataFrame using criteria from multiple columns
newdf = df[(df['col1']>2) & (df['col2']==444)]


def times2(x):
    return x*2

df['col1'].apply(times2)
df['col3'].apply(len)
df['col1'].sum()


# pandas.DataFrame.apply
# axis : {0 or ‘index’, 1 or ‘columns’}, default 0
# Axis along which the function is applied:
# 0 or ‘index’: apply function to each column.
# 1 or ‘columns’: apply function to each row.




del df['col1']



df.columns
df.index



df.sort_values(by='col2') #inplace=False by default

df.isnull()

# Drop rows with NaN Values
df.dropna()
```


```
df.fillna('FILL')


df.pivot_table(values='D',index=['A', 'B'],columns=['C'])

```


Data Input and Output

```
import numpy as np
import pandas as pd

df = pd.read_csv('example')
df.to_csv('example',index=False)

pd.read_excel('Excel_Sample.xlsx',sheetname='Sheet1')
df.to_excel('Excel_Sample.xlsx',sheet_name='Sheet1')

df = pd.read_html('http://www.fdic.gov/bank/individual/failed/banklist.html')


from sqlalchemy import create_engine
engine = create_engine('sqlite:///:memory:')
df.to_sql('data', engine)
sql_df = pd.read_sql('data',con=engine)
```


```
df.info()

How many Job Titles were represented by only one person in 2013? (e.g. Job Titles with only one occurence in 2013?)

sum(df[df['Year'] == 2013]['JobTitle'].value_counts() == 1)

```
