---
layout: note_layout
title : pandas

---


**series**

{% highlight ruby %}

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

{% endhighlight %}

**Using an Index**

{% highlight ruby %}

ser1 = pd.Series([1,2,3,4],index = ['USA', 'Germany','USSR', 'Japan'])              
ser2 = pd.Series([1,2,5,4],index = ['USA', 'Germany','Italy', 'Japan'])   
ser1['USA']

ser1 + ser2

{% endhighlight %}

**DataFrames**

{% highlight ruby %}

import pandas as pd
import numpy as np

from numpy.random import randn
np.random.seed(101)

df = pd.DataFrame(randn(5,4),index='A B C D E'.split(),columns='W X Y Z'.split())


{% endhighlight %}


**Selection and Indexing**

{% highlight ruby %}


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

{% endhighlight %}

**Conditional Selection**

{% highlight ruby %}


df>0 #부울린 리턴


df[df>0] #true인 곳만 값 리턴 - false 는 NaN


df[df['W']>0] # W>0인 행만 리턴

df[df['W']>0]['Y'] # W>0인 행만 리턴한 것에서 Y열만 리턴

df[df['W']>0][['Y','X']]

# for two conditions
df[(df['W']>0) & (df['Y'] > 1)]

{% endhighlight %}


**More Index Details**


{% highlight ruby %}

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

df.xs('G1')

df.xs(['G1',1])

df.xs(1,level='Num')

{% endhighlight %}
