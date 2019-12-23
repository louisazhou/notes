---
description: Matplotlib and seaborn
---

# Data Visualization

## Tell a Story

* Engage audience with hierarchical summarized insights
* Process 1. organize initial story and insights 2. who is your audience and what are they interested in 3. the high-level insights 4. correlation between the insights 5. how will the insights change the audience's mind or help them make decision

## 可视化的基本原则

* story focus, supported by visualization
* data dimension in a chart: 2D
* attentive attributes: color, highlight
* less is more: remove distraction 

{% embed url="https://python-graph-gallery.com/" %}

## Tableau

{% embed url="https://public.tableau.com/en-us/gallery/?tab=viz-of-the-day&type=viz-of-the-day" %}

{% embed url="https://public.tableau.com/en-us/s/resources" %}



## Import Package

```python
import numpy as np, pandas as pd
from matplotlib import pylab
import matplotlib.pyplot as plt
import matplotlib as mpl
mpl.rcParams.update(mpl.rcParamsDefault) # 恢复matplotlib默认样式
import seaborn as sns

import scipy.stats as stats
import statsmodels as sm
from __future__ import division
# plt.style.use('ggplot')  #使用ggplot的样式
```

### Basic of Matplotlib

Stateful vs stateless \(OOP\) approach

* Stateful approach

线图点图：

```python
plt.plot(x=,y=, color=, marker=, linestyle=, linewidth= , markersize= )
plt.bar(x, y, align='center', alpha=0.5) #bar chart
```

优点：简单快捷  
缺点：缺乏灵活性

* Stateless \(OOP\) approach

```python
fig, ax = plt.subplots(nrow, ncol, figsize=(5, 3))
# fig: 给我一张画布; ax：画布上要有几个图
# 将ax看成object, 添加plot,legend, set_xlabel, set_ylabel
```

比如同样是画三条线，对比这两种approach

```python
x=np.linspace(0,10,50)
np.random.seed(10)

# stateful
plt.plot(x,np.sin(x)+x+np.random.randn(50))
plt.plot(x,np.sin(x)+0.5*x+np.random.randn(50))
plt.plot(x,np.sin(x)+2*x+np.random.rand(50))
plt.title("Three curves")
plt.show()

# stateless
fig, ax0=plt.subplots(nrows=1) # Create a figure and a set of subplots.if multiple plots, return an array to ax. 
ax0.plot(x,np.sin(x)+x+np.random.randn(50))
ax0.plot(x,np.sin(x)+0.5*x+np.random.randn(50))
ax0.plot(x,np.sin(x)+2*x+np.random.rand(50))
ax0.set_title("Three curves")
plt.show()

## Plot multiple charts on the same figure: stateless only
fig=plt.figure() # crate a new figure
ax0=fig.add_subplot(211) # figure layout: 2x1. We will plot the first chart on [0,0]
ax0.plot(x,np.sin(x)+x+np.random.randn(50))
ax0.plot(x,np.sin(x)+0.5*x+np.random.randn(50))
ax0.plot(x,np.sin(x)+2*x+np.random.rand(50))

ax1=fig.add_subplot(212)
ax1.plot(x,np.sin(x)+x+np.random.randn(50))
ax1.plot(x,np.sin(x)+0.5*x+np.random.randn(50))
ax1.plot(x,np.sin(x)+2*x+np.random.rand(50))
plt.axhline(y=10,color='purple',linestyle='--')
plt.show()
```

更灵活的做法stateless里，还可以

#### 调节coordinate objects

* adjust background color
* adjust gridline
* set x-axis label and y-axis label
* adjust x-axis and y-axis ticks

```python
x=np.linspace(0,10,50)
np.random.seed(10)
fig,ax0=plt.subplots(nrows=1)
ax0.plot(x,np.sin(x)+x+np.random.randn(50))
ax0.plot(x,np.sin(x)+0.5*x+np.random.randn(50))
ax0.plot(x,np.sin(x)+2*x+np.random.rand(50))
ax0.set_title("Three curves",fontsize=20)

# 1. adjust gridline type: dotted-line
ax0.grid(color='gray', alpha=0.5, linestyle='dotted') # alpha to adjust grid transparency
# or hide the grid: 
ax0.grid(True)
# 2. set x-axis label and y-axis label
ax0.set_xlabel('X')
ax0.set_ylabel('Randomization')
ax0.xaxis.label.set_size(20) # set xlabel size
ax0.yaxis.label.set_size(20) # set xlabel size
# # 3. adjust x-axis and y-axis data range
ax0.set_xticks(np.arange(min(x),max(x)+1,1)) #步长的ticks

plt.show() # used to suppress 
```

### 用for loop来subplot

```python
## 用for loop to subplot
x=np.linspace(0,10)
np.random.seed(10)
# 生成数据
y1=np.sin(x)+x+np.random.randn(50)
y2=np.sin(x)+0.5*x+np.random.randn(50)
y3=np.sin(x)+2*x+np.random.randn(50)

df=pd.DataFrame({'serie1':y1,'serie2':y2,'serie3':y3})

fig=plt.figure()
fig.subplots_adjust(hspace=0.4)

i=1
for col in df.columns:
  plt.subplot(df.shape[1],1,i) 
  plt.plot(df.loc[:,col])
  plt.title(col,y=0.6,loc='right')
  i+=1
  
fig.show()
```

### 拿到一个新的数据 如何visualize

```python
# Import data using Pandas. csv file is in a Google Drive
file_id='13WIX0uQaA4ROvsfVjqPUwbmyW07XMc9S'
link='https://drive.google.com/uc?export=download&id={FILE_ID}'
csv_url=link.format(FILE_ID=file_id)
df = pd.read_csv(csv_url)
df.head()

# Data preprocessing:  rename columns, create age group
df.columns=['cust_id','first_name','last_name','gender','age','region','job','date_join',
           'balance']
df['age_group'] = pd.cut(df['age'], bins=[15, 30, 50, float('Inf')], labels=['15-30', '30-50', 'Above 50'])

# Balance vs region: Barchart
dt_region_mean_bal=df.groupby(['region'])['balance'].mean()
dt_region_mean_bal = dt_region_mean_bal.reset_index() # reset index才能保证出来的是一个dataframe，方便画图 不然index就成了region了
dt_region_mean_bal['balance']=np.rint(dt_region_mean_bal['balance']) #round to the nearest integer

fig,ax0=plt.subplots(nrows=1)
ax0.bar(dt_region_mean_bal['region'],dt_region_mean_bal['balance'])
# adjust: xlabel, ylabel,y-axis scale; adjust background color
# add title
ax0.set_title("Balance by region",fontsize=20)
ax0.set_xlabel('Region')
ax0.set_ylabel('Average balance')
ax0.xaxis.label.set_size(20) # set xlabel size
ax0.yaxis.label.set_size(20) # set xlabel size
ax0.set_ylim(top=70000)
ax0.grid(False)

# what about data label?
def autolabel(rects):
    """
    Attach a text label above each bar displaying its height
    """
    for rect in rects:
        height = rect.get_height()
        ax0.text(rect.get_x() + rect.get_width()/2., 1.05*height,
                '%d' % int(height),
                ha='center', va='bottom')

rect1=ax0.bar(dt_region_mean_bal['region'],dt_region_mean_bal['balance'],color='blue')
autolabel(rect1)
plt.show()
```

![](../.gitbook/assets/image%20%2813%29.png)

```python
# use OOP + pandas plot
fig,ax1=plt.subplots(nrows=1)
df.groupby(['region','gender'])['balance'].mean().unstack().plot(kind='bar',ax=ax1)
ax1.set_ylim(top=70000)
ax1.set_title("Balance by region and gender")
ax1.set_ylabel('Average Balance')
ax1.grid(False)
plt.show()
```

![](../.gitbook/assets/image%20%2852%29.png)

#### 双y轴

```python
# two y-axies: example, plot conversions and conversion rate on the same chart.
ts = pd.Series(np.random.randn(1000), index=pd.date_range('1/1/2000', periods=1000))
ts = ts.cumsum()
df = pd.DataFrame(np.random.randn(1000, 4), index=ts.index, columns=list('ABCD'))
df = df.cumsum()
df.plot(secondary_y=['A', 'B'], mark_right=False)
plt.show()
```

![](../.gitbook/assets/image%20%289%29.png)

## Seaborn

* 用seaborn作图
* 用matplotlib微调
* 加theme

语法上和R的ggplot很像

### 对比stateful vs stateless

```python
import seaborn as sns
sns.set_style('whitegrid')
#stateful
sns.boxplot(x='species', y='sepal_length', data=df_iris)
plt.xticks(rotation=-45) # adjust xticks
plt.title('Aris species sepal_length boxplox') # add title
plt.show()
```

![](../.gitbook/assets/image%20%2850%29.png)

```python
# boxplot UK bank client balance by age group, using seaborn and matplotlib
# stateless(OOP)
fig,ax2=plt.subplots(nrows=1)
sns.boxplot(x='age_group',y='balance',data=df,ax=ax2) # connect sns and matplotlib
ax2.grid(False)
ax2.set_title("Balance boxplot sliced by age_group")
plt.show()
```

![](../.gitbook/assets/image%20%2851%29.png)

```python
# Balance distribution by difference groups
# stateless(OOP)
fig = plt.figure()
ax3 = fig.add_subplot(1,1,1)

sns.distplot(df.loc[df['age_group']=='15-30','balance'],label='15-30', hist=False,ax=ax3)
sns.distplot(df.loc[df['age_group']=='30-50','balance'],label='30-50', hist=False,ax=ax3)
sns.distplot(df.loc[df['age_group']=='Above 50','balance'],label='Above 50', hist=False,ax=ax3)

ax3.grid(False)
ax3.set_title("Balance distribution by age_group")
ax3.legend()
plt.show()
```

![](../.gitbook/assets/image%20%2849%29.png)

### unstack

sns里面自己内置了很多groupby似的操作 帮着slice了数据 方便哭了

```python
# barplot
df_titanic=sns.load_dataset('titanic')
g=sns.barplot(x="sex", y="survived", hue="class", ci=None, data=df_titanic)
g.set_ylabel('survival rate')
vals=g.get_yticks()
g.set_yticklabels(['{:3.2f}%'.format(x*100) for x in vals]) # if you want to show percentage for yticklabels
plt.show()
```

![](../.gitbook/assets/image%20%2818%29.png)

```python
# Scatterplot
sns.pairplot(data=df_iris, hue="species")
plt.show()
```

![](../.gitbook/assets/image%20%2811%29.png)

```python
# multiple charts and chart overlap
# stateful
fig=plt.figure(figsize=(8,16)) # X: control width, Y: control length
plt.subplot(2,1,1) # or ax0=fig.add_subplot(2,1,1)
sns.boxplot(x='species',y='sepal_length',data=df)
plt.xticks(rotation=-45) # adjust xticks
plt.title('Aris species sepal_length boxplox') # add title

plt.subplot(2,1,2)
sns.distplot(df['sepal_length'])

plt.show()
```

![](../.gitbook/assets/image%20%2856%29.png)

### factor plot 

|  id | diet | pulse | time | kind |  |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | 1 | low fat | 85 | 1 min | rest |
| 1 | 1 | low fat | 85 | 15 min | rest |
| 2 | 1 | low fat | 88 | 30 min | rest |
| 3 | 2 | low fat | 90 | 1 min | rest |
| 4 | 2 | low fat | 92 | 15 min | rest |

```python
# g is a sns object
g=sns.factorplot(x='time', 
                   y='pulse', 
                   data=df, 
                   hue='diet',  # Color by diet
                   col='diet',  # Separate by diet
                   kind='box') # Swarmplot
 
# Rotate x-axis labels
g.set_xticklabels(rotation=-45)
```

![](../.gitbook/assets/image%20%2832%29.png)

### FacetGrid

FacetGrid 是一个绘制多个图表（以网格形式显示）的接口。

步骤：  
1、实例化对象  
2、map，映射到具体的 seaborn 图表类型  
3、添加图例

```text
# 在不同社会等级下，男性和女性在不同登陆港口下的数量对比
grid = sns.FacetGrid(data_all, col='Pclass', hue='Sex', palette='seismic', size=4)
# 'Embarked' 是  data_all （是一个 DataFrame） 中的字段
grid.map(sns.countplot, 'Embarked', alpha=.8)
# 在图表的右边会显示图例
grid.add_legend() 
```

![](../.gitbook/assets/image%20%2864%29.png)

row='Sex', col='Pclass' 是什么效果。 

```text
grid = sns.FacetGrid(data_all, row='Sex', col='Pclass',
                     hue='Survived', palette='seismic', size=4)
grid.map(sns.countplot, 'Embarked', alpha=0.8)
grid.add_legend()
```

![](../.gitbook/assets/image%20%2846%29.png)

### Regression Plot

```python
# regression plot
# Simulate data:
x = np.linspace(1,50,num=100)
epsilon = np.random.normal(0,3,size=100)
dt_lin = pd.DataFrame({'x':x, 'y':0.2+0.2*x + epsilon})

sns.set_style('whitegrid')
sns.regplot(x='x',y='y',data=dt_lin)

plt.show()
```

![](../.gitbook/assets/image%20%2848%29.png)

```python
# 微调， 把图形做得更简洁, 把不喜欢的gridline去掉, 加上title
fig=plt.figure(figsize=(8,4))
ax0=fig.add_subplot(121)
sns.regplot(x='x',y='y',data=dt_lin,ax=ax0)

ax1=fig.add_subplot(122)
sns.regplot(x='x',y='y',data=dt_lin,ax=ax1)
ax1.grid(False)
ax1.set_title('Clean linear regression')

plt.show()
```

![](../.gitbook/assets/image%20%2835%29.png)

```python
# seaborn fit a polynomial model
x = np.linspace(1,50, num = 100)
dt_poly=pd.DataFrame({'x':x,'y':0.2+0.3*np.power(x,2)})
sns.regplot(x='x', y='y',data=dt_poly,order=2, ci=None, scatter_kws={"s": 80});
plt.show()
```

![](../.gitbook/assets/image%20%2853%29.png)

### 更多例子

{% embed url="https://zhuanlan.zhihu.com/p/27593869" %}



