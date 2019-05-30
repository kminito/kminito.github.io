---
layout: post
title: "Natural Language Process(NLP) 연습"
category: "machine_learning"
tag:
  - "data science"
  - "machine learning"
---



자연어 처리 수업 내용 복습입니다.

캐글의 '[RecSys2013: Yelp Business Rating Prediction](https://www.kaggle.com/c/yelp-recsys-2013)'에 있는 데이터셋의 일부를 이용하여, 리뷰의 텍스트 내용을 바탕으로 실제 부여한 별점을 예측하고자 합니다. 수업에서는 연습 목적으로 별점 한 개와 다섯 개 짜리의 데이터만 사용했습니다.


#### **Import Libraries**

먼저 일반적으로 사용하는 라이브러리들을 임포트합니다.

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

sns.set_style('white')
%matplotlib inline
```


#### **Get the Data**
파일을 불러와서, 간단히 살펴보도록 하겠습니다.


```python
yelp = pd.read_csv('yelp.csv')
```

```python
yelp.head()
```
```cmd

business_id	date	review_id	stars	text	type	user_id	cool	useful	funny
0	9yKzy9PApeiPPOUJEtnvkg	2011-01-26	fWKvX83p0-ka4JS3dc6E5A	5	My wife took me here on my birthday for breakf...	review	rLtl8ZkDX5vH5nAx9C3q5Q	2	5	0
1	ZRJwVLyzEJq1VAihDhYiow	2011-07-27	IjZ33sJrzXqU-0X6U8NwyA	5	I have no idea why some people give bad review...	review	0a2KyEL0d3Yb1V6aivbIuQ	0	0	0
2	6oRAC4uyJCsJl1X0WZpVSA	2012-06-14	IESLBzqUCLdSzSqm0eCSxQ	4	love the gyro plate. Rice is so good and I als...	review	0hT2KtfLiobPvh6cDC8JQg	0	1	0
3	_1QQZuf4zZOyFCvXc0o6Vg	2010-05-27	G-WvGaISbqqaMHlNnByodA	5	Rosie, Dakota, and I LOVE Chaparral Dog Park!!...	review	uZetl9T0NcROGOyFfughhg	1	2	0
4	6ozycU1RpktNG2-1BroVtw	2012-01-05	1uJFq2r5QfJG_6ExMRCaGw	5	General Manager Scott Petello is a good egg!!!...	review	vYmM4KTsC8ZfQBg-j5MWkw	0	0

```


```python
yelp.info()
```

```cmd
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 10000 entries, 0 to 9999
Data columns (total 10 columns):
business_id    10000 non-null object
date           10000 non-null object
review_id      10000 non-null object
stars          10000 non-null int64
text           10000 non-null object
type           10000 non-null object
user_id        10000 non-null object
cool           10000 non-null int64
useful         10000 non-null int64
funny          10000 non-null int64
dtypes: int64(4), object(6)
memory usage: 546.9+ KB
```


```python
yelp.describe()
```
```cmd
stars	cool	useful	funny
count	10000.000000	10000.000000	10000.000000	10000.000000
mean	3.777500	0.876800	1.409300	0.701300
std	1.214636	2.067861	2.336647	1.907942
min	1.000000	0.000000	0.000000	0.000000
25%	3.000000	0.000000	0.000000	0.000000
50%	4.000000	0.000000	1.000000	0.000000
75%	5.000000	1.000000	2.000000	1.000000
max	5.000000	77.000000	76.000000	57.000000
```


text의 값을 하나 살펴보겠습니다.

```python
yelp['text'][0]
```


'My wife took me here on my birthday for breakfast and it was excellent.  The weather was perfect which made sitting outside overlooking their grounds an absolute pleasure.  Our waitress was excellent and our food arrived quickly on the semi-busy Saturday morning.  It looked like the place fills up pretty quickly so the earlier you get here the better.\n\nDo yourself a favor and get their Bloody Mary.  It was phenomenal and simply the best I\'ve ever had.  I\'m pretty sure they only use ingredients from their garden and blend them fresh when you order it.  It was amazing.\n\nWhile EVERYTHING on the menu looks excellent, I had the white truffle scrambled eggs vegetable skillet and it was tasty and delicious.  It came with 2 pieces of their griddled bread with was amazing and it absolutely made the meal complete.  It was the best "toast" I\'ve ever had.\n\nAnyway, I can\'t wait to go back!'  





텍스트의 길이에 따른 별점을 살펴보기 위해 `text length` 라는 행을 추가합니다.
```python
yelp['text length'] = yelp['text'].apply(len)
```


#### **EDA**  

**별점에 따른 text length의 히스토그램**  

```python
g = sns.FacetGrid(yelp,col='stars')
g.map(plt.hist,'text length')
```
![nlp_1]({{ site.url }}\assets\ML\practice\nlp_1.png)



**별점에 따른 text length의 boxblot**  


```python
sns.boxplot(x='stars',y='text length',data=yelp,palette='rainbow')
```

![nlp_2]({{ site.url }}\assets\ML\practice\nlp_2.png)


**별점 수의 countplot**  


```python
sns.boxplot(x='stars',y='text length',data=yelp,palette='rainbow')
```

![nlp_3]({{ site.url }}\assets\ML\practice\nlp_3.png)


별점 수로 묶은 데이터들의 평균을 나타내는 DataFrame을 만듭니다.  


```python
stars = yelp.groupby('stars').mean()
stars
```

각 컬럼간의 상관관계를 확인합니다.  


```python
stars.corr()
```

```cmd
cool	useful	funny	text length
cool	1.000000	-0.743329	-0.944939	-0.857664
useful	-0.743329	1.000000	0.894506	0.699881
funny	-0.944939	0.894506	1.000000	0.843461
text length	-0.857664	0.699881	0.843461	1.000000
```

```python
sns.heatmap(stars.corr(),cmap='coolwarm',annot=True)
```

![nlp_4]({{ site.url }}\assets\ML\practice\nlp_4.png)  



#### **NLP Classification Task**  


별점이 1과 5인 데이터만 따로 분리해서 새로운 데이터프레임을 만듭니다.  

```python
yelp_class = yelp[(yelp.stars==1) | (yelp.stars==5)]
```

`yelp class` 데이터의 `text` 컬럼과 `stars` 컬럼을 각각 X, y로 정의합니다.  

```python
X = yelp_class['text']
y = yelp_class['stars']
```


CounterVectorizer를 임포트하고 객체를 생성합니다.  

```python
from sklearn.feature_extraction.text import CountVectorizer
cv = CountVectorizer()
```
`fit_transform` 메소드를 사용하여 `X`를 fitting합니다.  

```python
X = cv.fit_transform(X)
```  


**Train Test Split**    

```python
from sklearn.model_selection import train_test_split
```
```python
X_train, X_test, y_train, y_test = train_test_split(X, y,test_size=0.3)
```  


**Training a Model**  


다항분포 나이브 베이즈 모델을 사용하여 Classification을 하기 위해, MultinomialNB를 임포트하고 객체를 생성합니다.  

참고 : [위키피디아 - 나이브 베이즈 분류](https://ko.wikipedia.org/wiki/%EB%82%98%EC%9D%B4%EB%B8%8C_%EB%B2%A0%EC%9D%B4%EC%A6%88_%EB%B6%84%EB%A5%98)  



```python
from sklearn.naive_bayes import MultinomialNB
nb = MultinomialNB()
```

데이터를 Fitting합니다.
```python
nb.fit(X_train,y_train)
```
```cmd
MultinomialNB(alpha=1.0, class_prior=None, fit_prior=True)
```





#### **Predictions and Evaluations**

위에서 생성한 모델에 테스트 데이터를 돌립니다.
```python
predictions = nb.predict(X_test)
```

counfusion matrix와 classification_report를 이용하여 결과를 확인합니다.
```python
from sklearn.metrics import confusion_matrix,classification_report
```
```python
print(confusion_matrix(y_test,predictions))
print('\n')
print(classification_report(y_test,predictions))
```
```cmd
[[159  69]
 [ 22 976]]


             precision    recall  f1-score   support

          1       0.88      0.70      0.78       228
          5       0.93      0.98      0.96       998

avg / total       0.92      0.93      0.92      1226

```


#### **Using Text Processing**

여기서부터는 모델링 전에 TF-IDF를 적용시켜서 다시 Classification을 합니다. pipeline을 이용합니다.  

참고 : [위키피디아 - tf-idf](https://ko.wikipedia.org/wiki/Tf-idf)


```python
from sklearn.feature_extraction.text import TfidfTransformer
from sklearn.pipeline import Pipeline
```

CountVectorizer(), TfidfTransformer(),MultinomialNB()을 사용하는 파이프라인을 생성합니다.

```python
pipeline = Pipeline([
    ('bow', CountVectorizer()),  # strings to token integer counts
    ('tfidf', TfidfTransformer()),  # integer counts to weighted TF-IDF scores
    ('classifier', MultinomialNB()),  # train on TF-IDF vectors w/ Naive Bayes classifier
])
```

**Train Test Split**

```python
X = yelp_class['text']
y = yelp_class['stars']
X_train, X_test, y_train, y_test = train_test_split(X, y,test_size=0.3)
```

파이프라인에 training data를 fitting합니다.
```python
pipeline.fit(X_train,y_train)
```
```cmd
Pipeline(steps=[('bow', CountVectorizer(analyzer='word', binary=False, decode_error='strict',
        dtype=<class 'numpy.int64'>, encoding='utf-8', input='content',
        lowercase=True, max_df=1.0, max_features=None, min_df=1,
        ngram_range=(1, 1), preprocessor=None, stop_words=None,
        strip_...f=False, use_idf=True)), ('classifier', MultinomialNB(alpha=1.0, class_prior=None, fit_prior=True))])
```


**Predictions and Evaluations**

```python
predictions = pipeline.predict(X_test)
```
```python
print(confusion_matrix(y_test,predictions))
print(classification_report(y_test,predictions))
```

```cmd
[[  0 228]
 [  0 998]]
             precision    recall  f1-score   support

          1       0.00      0.00      0.00       228
          5       0.81      1.00      0.90       998

avg / total       0.66      0.81      0.73      1226
```

TF-IDF를 적용하니 오차가 더 커졌습니다.



#### **참고 - CountVectorizer에 Analyzer** 사용

**문장 부호 삭제**
```python
import string

mess = 'Sample message! Notice: it has punctuation.'

# Check characters to see if they are in punctuation
nopunc = [char for char in mess if char not in string.punctuation]

# Join the characters again to form the string.
nopunc = ''.join(nopunc)
```

**stopwords를 살펴보자**

```python
from nltk.corpus import stopwords
stopwords.words('english')[0:10] # Show some stop words
```
```cmd
['i', 'me', 'my', 'myself', 'we', 'our', 'ours', 'ourselves', 'you', 'your']
```

nopunc에는 아래와 같은 단어들이 있는데,
```python
nopunc.split()
```
```cmd
['Sample', 'message', 'Notice', 'it', 'has', 'punctuation']
```

stopwords를 제거해보자
```python
clean_mess = [word for word in nopunc.split() if word.lower() not in stopwords.words('english')]
```
```python
clean_mess
```
```cmd
['Sample', 'message', 'Notice', 'punctuation']
```

위의 것들을 함수로 정의하면

```python
def text_process(mess):
    """
    Takes in a string of text, then performs the following:
    1. Remove all punctuation
    2. Remove all stopwords
    3. Returns a list of the cleaned text
    """
    # Check characters to see if they are in punctuation
    nopunc = [char for char in mess if char not in string.punctuation]

    # Join the characters again to form the string.
    nopunc = ''.join(nopunc)

    # Now just remove any stopwords
    return [word for word in nopunc.split() if word.lower() not in stopwords.words('english')]
```


데이터프레임에 적용하면 아래와 같이 된다 된다
```python
# Check to make sure its working
messages['message'].head(5).apply(text_process)
```
```cmd
0    [Go, jurong, point, crazy, Available, bugis, n...
1                       [Ok, lar, Joking, wif, u, oni]
2    [Free, entry, 2, wkly, comp, win, FA, Cup, fin...
3        [U, dun, say, early, hor, U, c, already, say]
4    [Nah, dont, think, goes, usf, lives, around, t...
Name: message, dtype: object
```

적용할 때는 이런 식으로 적용하면 된다.
```python
from sklearn.pipeline import Pipeline

pipeline = Pipeline([
    ('bow', CountVectorizer(analyzer=text_process)),  # strings to token integer counts
    ('tfidf', TfidfTransformer()),  # integer counts to weighted TF-IDF scores
    ('classifier', MultinomialNB()),  # train on TF-IDF vectors w/ Naive Bayes classifier
])
```
