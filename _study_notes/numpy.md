---
layout: note_layout
title : numpy

---


**Creating NumPy Arrays**

{% highlight ruby %}

import numpy as np

my_list = [1,2,3]
np.array(my_list)

# 5 10개짜리 배열
np.ones(10)*5

# Return evenly spaced values within a given interval.
np.arange(0,10)
np.arange(0,11,2)

# Generate arrays of zeros or ones
np.zeros(3)
np.zeros((5,5))
np.ones(3)
np.ones((3,3))


# Return evenly spaced numbers over a specified interval.
np.linspace(0,10,3)
np.linspace(0,10,50)


# Creates an identity matrix
np.eye(4)


# Create an array with random samples from a uniform distribution over [0, 1).
np.random.rand(2)
np.random.rand(5,5)


# Return a sample (or samples) from the "standard normal" distribution.
np.random.randn(2)
np.random.randn(5,5)


# Return random integers from low (inclusive) to high (exclusive).
np.random.randint(1,100)
np.random.randint(1,100,10)

{% endhighlight %}


**Array Attributes and Methods**

{% highlight ruby %}

arr = np.arange(25)
ranarr = np.random.randint(0,50,10)

arr.reshape(5,5)
ranarr.max()
ranarr.argmax()

ranarr.min()
ranarr.argmin()

arr.shape
arr.reshape(1,25)
arr.reshape(1,25).shape
arr.reshape(25,1)

arr.reshape(25,1).shape
arr.dtype

# 생성과 모양 변경을 동시에
mat = np.arange(1,26).reshape(5,5)

{% endhighlight %}

**Numpy Indexing and Selection**

{% highlight ruby %}

arr = np.arange(0,11)

arr[0:5]=100
arr = np.arange(0,11) #reset array

slice_of_arr = arr[0:6]
slice_of_arr[:]=99

# 오른쪽처럼 해야 2차원 배열로 들어옴!
mat[:3,1] != mat[:3,1:2]



# Changes occur in original arrays

-> 바꾸기 싫으면
arr_copy = arr.copy() 와 같이 카피


## Indexing a 2D array (matrices)

The general format is **arr_2d[row][col]** or **arr_2d[row,col]**. I recommend usually using the comma notation for clarity.


arr_2d = np.array(([5,10,15],[20,25,30],[35,40,45]))

arr_2d[1]
arr_2d[1][0]
arr_2d[1,0]

arr_2d[:2,1:]
arr_2d[2]
arr_2d[2,:]


 {% endhighlight %}

**Fancy Indexing**
 {% highlight ruby %}


#Set up matrix
arr2d = np.zeros((10,10))

#Length of array
arr_length = arr2d.shape[1]

#Set up array
for i in range(arr_length):
    arr2d[i] = i

# 부울린을 이용해서 해당되는 값을 리#
arr2d[[2,4,6,8]]
arr2d[[6,4,2,7]]


arr = np.arange(1,11)
arr > 4
bool_arr = arr>4
arr[bool_arr]

arr[arr>2]

x = 2
arr[arr>x]

{% endhighlight %}



**NumPy Operations**

{% highlight ruby %}


import numpy as np
arr = np.arange(0,10)

arr + arr
arr * arr
arr - arr
arr/arr # 0/0해도 에러 발생 안 하고 nan을 리턴
1/arr # 1/0해도 에러 발생 안 하고 inf를 리턴
arr*3

np.sqrt(arr)
np.exp(arr)
np.max(arr) #same as arr.max()
np.sin(arr)
np.log(arr) #에러 발생 안 하고 -inf 리턴


mat.sum(axis=0)
mat.sum(axis=1)

{% endhighlight %}
