---
layout: note_layout
title : seaborn
---


{% highlight ruby %}
import seaborn as sns
%matplotlib inline

tips = sns.load_dataset('tips')
{% endhighlight %}



{% highlight ruby %}
sns.distplot(tips['total_bill'])
sns.distplot(tips['total_bill'],kde=False,bins=30)
sns.jointplot(x='total_bill',y='tip',data=tips,kind='scatter')
sns.jointplot(x='total_bill',y='tip',data=tips,kind='hex')
sns.jointplot(x='total_bill',y='tip',data=tips,kind='reg')


sns.pairplot(tips)
sns.pairplot(tips,hue='sex',palette='coolwarm')
sns.rugplot(tips['total_bill'])



{% endhighlight %}




{% highlight ruby %}

sns.barplot(x='sex',y='total_bill',data=tips)
sns.barplot(x='sex',y='total_bill',data=tips,estimator=np.std)

sns.countplot(x='sex',data=tips)
sns.boxplot(x="day", y="total_bill", data=tips,palette='rainbow')

sns.boxplot(data=tips,palette='rainbow',orient='h')
sns.boxplot(x="day", y="total_bill", hue="smoker",data=tips, palette="coolwarm")

sns.violinplot(x="day", y="total_bill", data=tips,palette='rainbow')
sns.violinplot(x="day", y="total_bill", data=tips,hue='sex',palette='Set1')
sns.violinplot(x="day", y="total_bill", data=tips,hue='sex',split=True,palette='Set1')

sns.stripplot(x="day", y="total_bill", data=tips)
sns.stripplot(x="day", y="total_bill", data=tips,jitter=True)
sns.stripplot(x="day", y="total_bill", data=tips,jitter=True,hue='sex',palette='Set1')
sns.stripplot(x="day", y="total_bill", data=tips,jitter=True,hue='sex',palette='Set1',split=True)
sns.stripplot(x="day", y="total_bill", data=tips,jitter=True,hue='sex',palette='Set1',split=True)


sns.swarmplot(x="day", y="total_bill", data=tips)
sns.swarmplot(x="day", y="total_bill",hue='sex',data=tips, palette="Set1", split=True)


sns.violinplot(x="tip", y="day", data=tips,palette='rainbow')
sns.swarmplot(x="tip", y="day", data=tips,color='black',size=3)

sns.factorplot(x='sex',y='total_bill',data=tips,kind='bar')



{% endhighlight %}



{% highlight ruby %}

{% endhighlight %}




{% highlight ruby %}

{% endhighlight %}





{% highlight ruby %}

{% endhighlight %}
