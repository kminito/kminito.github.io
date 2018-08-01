---
layout: note_layout
title : visualization
---

캐글 문제 풀면서 EDA 한 것들 중 visualization 정리


```python
fig, axes = plt.subplots(2,2,figsize=(12,10))
sns.boxplot(data=train,y="count",ax=axes[0][0])
sns.boxplot(data=train,y="count",x="season",ax=axes[0][1])
sns.boxplot(data=train,y="count",x="hour",ax=axes[1][0])
sns.boxplot(data=train,y="count",x="weekday",ax=axes[1][1])
```

![vis_1]({{ site.url }}/assets/study_notes/vis_1.png)


```python
fig, axes = plt.subplots(2,2,figsize=(12,10))
sns.barplot(data=train,x='month',y="count",ax=axes[0][0])
sns.barplot(data=train,x='hour',y="count",ax=axes[0][1])
sns.barplot(data=train,x='weekday',y="count",ax=axes[1][0])
```
![vis_1]({{ site.url }}/assets/study_notes/vis_2.png)

```python
fig,(ax1,ax2, ax3)= plt.subplots(3,1)
fig.set_size_inches(10,12)

sns.pointplot(data=train, x='hour',y='count',hue='weekday',ax=ax1)
sns.pointplot(data=train, x='hour',y='count',hue='workingday',ax=ax2)
sns.pointplot(data=train, x='hour',y='count',hue='holiday',ax=ax3)
```
![vis_1]({{ site.url }}/assets/study_notes/vis_3.png)

```python
fig, ax = plt.subplots(figsize=(16,8))
sns.heatmap(corrMatt.corr(),cmap='coolwarm',annot=True, square=True)
```
![vis_1]({{ site.url }}/assets/study_notes/vis_4.png)


```python
sns.jointplot(x='temp',y='atemp',data=train,kind='hex')
```
![vis_1]({{ site.url }}/assets/study_notes/vis_5.png)




```python
sns.set_style('whitegrid')
sns.lmplot('Room.Board','Grad.Rate',data=df, hue='Private',
           palette='coolwarm',size=6,aspect=1,fit_reg=False)
```
![k_means_2]({{ site.url }}\assets\ML\practice\k_means_2.png)



```python
sns.set_style('darkgrid')
g = sns.FacetGrid(df,hue="Private",palette='coolwarm',size=6,aspect=2)
g = g.map(plt.hist,'Outstate',bins=20,alpha=0.7)
```
![k_means_4]({{ site.url }}\assets\ML\practice\k_means_4.png)




```python
g = sns.FacetGrid(yelp,col='stars')
g.map(plt.hist,'text length')
```
![nlp_1]({{ site.url }}\assets\ML\practice\nlp_1.png)


```python
sns.boxplot(x='stars',y='text length',data=yelp,palette='rainbow')
```

![nlp_2]({{ site.url }}\assets\ML\practice\nlp_2.png)


```python
sns.boxplot(x='stars',y='text length',data=yelp,palette='rainbow')
```

![nlp_3]({{ site.url }}\assets\ML\practice\nlp_3.png)
