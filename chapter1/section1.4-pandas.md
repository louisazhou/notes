# Section1.4 Pandas

The two primary data structures of pandas, **Series \(1-dimensional\)** and **DataFrame \(2-dimensional\)**, handle the vast majority of typical use cases in finance, statistics, social science, and many areas of engineering. pandas is built on top of **NumPy** and is intended to integrate well within a scientific computing environment with many other 3rd party libraries.

## 在Colab怎么load数据

1. 直接用链接读 如果header是None，自动补1234

```python
iris = 'http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data'
df_iris = pd.read_csv(iris, sep=',', header=None)
```

2. data上传Google drive，share link，把ID=后面的复制出来，然后用下面的

```python
file_id='1k0ZYUdqqjPjVWCw3ElcaMMRFg6H3-Pjh'
link='https://drive.google.com/uc?export=download&id={FILE_ID}'
csv_url=link.format(FILE_ID=file_id)
df_uk_rain = pd.read_csv(csv_url)
```

3. 在GitHub读raw data

```python
import pandas as pd
import io
import requests
url="https://gist.githubusercontent.com/curran/a08a1080b88344b0c8a7/raw/d546eaee765268bf2f487608c537c05e22e4b221/iris.csv"
c=pd.read_csv(url)
```

4. 把Google drive和colab给mount过来

在本地读就很简单，pd.read\_csv\("../data\_folder/data.csv"\)就行



最后如果想从colab下载处理好的df，index=False就是不要第一列的那个idx

```python
df_uk_rain.to_csv('df.csv', index=False)
from google.colab import files
files.download('df.csv')
```

## 各种数据查看和预处理

一键改dataframe column的名字

```python
df_uk_rain.columns = ['water_year','rain_octsep', 'outflow_octsep',
              'rain_decfeb', 'outflow_decfeb', 'rain_junaug', 'outflow_junaug']
```

看头、看尾

```python
df_uk_rain.head(10)
df_uk_rain.tail()
```

看data type、有几行之类的基本信息

```text
df.info()
```

如果想看平均值、std、25%、50%之类的，用的

```text
df_iris.describe()
```

### Check Duplication: duplicated\(\)

duplicated\(\)找到重复的行（所有element都一模一样的），返回的其实是True/False

然后np.where 给出其中是True的index

```python
np.where(df_uk_rain.rain_octsep.duplicated())
```

如果只想check是不是某几个column里有一模一样的，那就可以指定一下

```python
df_uk_rain.iloc[np.where(df_uk_rain[['water_year', 'rain_octsep']].duplicated())]
```

### Remove Duplication: drop\_duplicated\(\)

```python
df_dedup=df_dup.drop_duplicates()
```

### Outlier 

Outlier Detection： Boxplot 

Interquartile Range\(IQR\): IQR= Upper Quantile\(Q3\) - Lower Quartile\(Q1\) 

Lower Limit = Q1 - 1.5 IQR 

Upper Limit = Q3 + 1.5 IQR

![](https://i.stack.imgur.com/mpbTr.gif)

```python
def iqr_outlier_rm(dt_input):
  lq,uq=np.percentile(dt_input,[25,75])
  lower_l=lq - 1.5*(uq-lq)
  upper_l=uq + 1.5*(uq-lq)
  return dt_input[(dt_input >=lower_l)&(dt_input<=upper_l)]
  
dt_outlier_ws=iqr_outlier_rm(dt_outlier)
sns.boxplot(dt_outlier_ws,orient='v')
```

### Missing Value



## index指定

series是一个维度，一维的，所以没有n\*1或者1\*n之说。

```python
# Series from list
s = pd.Series([1,3,5,np.nan,6,8], index = ['a','b','c','d','e','f'])
```

## Indexing and Slicing 

注意下面的区别，只有一对\[\]取出来的是series，不是dataframe。

```python
df1 = df_iris['sepal_length']
print(type(df1))

df2 = df_iris[['sepal_length']]
print(type(df2))
```

在df里只能使用slicing的方式来indexing，而不能\[index\]

```python
df3 = df_iris[['sepal_length', 'petal_length']]
print(df3.head())
print(type(df3))

df4 = df_iris[1:3]
print(df4)
print(type(df4))
```

上面是合法的，但是下面的不合法

```python
df5 = df_iris[1]
```

iloc或loc可以选行也可以选列，但是如果弄不清，就坚持用loc吧！

因为iloc索引的是index，有时候随着df的变化index会变，所以看起来好像不那么robust。既然是index，那么iloc的索引\[beginpoint, endpoint\)

```python
# selection by position, use iloc, endpoint is excluded
# iloc既能取行又能取列
df1 = df_iris.iloc[3]
print(df1)
print(type(df1)) #series 如果[[3]]那就df

df2 = df_iris.iloc[3:5,0:2]
print(df2)
print(type(df2)) #df

df3 = df_iris.iloc[[1,2,4],[0,2]]
print(df3)
print(type(df3)) #df
```

loc和iloc的区别是它 \[beginpoint, endpoint\], endpoint是included，另外loc只能索引column name，然后slice的方式来做行  比如下面的可以

```python
# selection by lable, use loc , endpoint is included
df1 = df_iris.loc[:, 'sepal_length']
print(df1.head())
print(type(df1)) #注意这个是series 因为只有一个[] ！

df2 = df_iris.loc[1:3, ['sepal_length']]
print(df2.head())
print(type(df2))

df3 = df_iris.loc[[1, 3], ['sepal_length', 'petal_length']]
print(df3)
```

slicing 不要单独套括号，但是index都套上括号，这样就不用担心series的问题了

## Boolean Indexing

和np的差不多，pd也可以boolean indexing，注意这里的&是&而不是and，因为它是逻辑与，不需要位与。

```python
# boolean indexing
df1 = df_iris[(df_iris['sepal_length'] > 6.0) & (df_iris.petal_length < 5.0)]
print(df1)

df2 = df_iris.loc[(df_iris['sepal_length'] > 6.0) & (df_iris.petal_length < 5.0), ['sepal_width', 'petal_width']]
df2 = df2.reset_index()
print(df2)
df2.index
```

筛选过后，可以reset\_index\(\)一下～

## Useful Functions

### Apply

Apply只能对df里的一行或者一列操作

line 4的list\('abc'\)等价于\['a','b','c'\] 

```python
import numpy as np
import pandas as pd

df = pd.DataFrame(np.arange(12).reshape((4, 3)), columns=list('abc'), index=['Utah', 'Ohio', 'Texas', 'Oregon'])
print(df)

def func(col):
  return col.max() - col.min()

# add a new column using apply
df['range'] = df.apply(func, axis = 1)


# use lambda function
print('haluo')
print(df.apply(lambda x: x.mean(), axis='index'))

print('haluo2:')
print(df.apply(lambda x: x.mean(), axis='columns'))


# add a new column
df['range'] = df[['a','b','c']].apply(func, axis='columns')
print(df)

# for some columns
df[['a', 'b']].apply(sum, axis=0)

```

### Lambda

lambda function，也叫匿名函数，因为lambda不需要起名字，一次性的，用完就完了 lambda input: output, axis = 'columns'

axis=‘columns‘等价于axis=1

axis='index'等价于axis=0

```python
# use lambda function
print('haluo')
print(df.apply(lambda x: x.mean(), axis='index'))

print('haluo2:')
print(df.apply(lambda x: x.mean(), axis='columns'))
```

### Applymap

Applymap是element-wise的操作，所以针对的是每一个element的操作

```python
df = pd.DataFrame(np.arange(12).reshape((4, 3)), columns=list('abc'), index=['Utah', 'Ohio', 'Texas', 'Oregon'])
print(df.applymap(lambda x: x**2))
```

### DateTime

之后回来补吧，好像没什么好写的... 就是一个pd. to\_datetime\(\)的function

## Merging 

[参考链接](https://pandas.pydata.org/pandas-docs/stable/user_guide/merging.html)

```python
df_right=pd.DataFrame({'year':np.arange(1980,1990),'rain_cn':np.arange(800,810)})
print(df_right)
df_right2=pd.DataFrame({'year': [900], 'rain_cn': [1800]})
print(df_right2)
df_right=pd.concat([df_right, df_right2])
print(df_right)
```

### SQL-like inner join

```python
df_join=pd.merge(df_uk_rain, df_right, left_on='year',right_on='year',how='inner')
print(df_join)
```

### SQL-like left join

```python
df_left_join=pd.merge(df_uk_rain, df_right, left_on='year',right_on='year', how='left') # left_on, right_on
df_left_join.head()
```

### 在pandas里直接写SQL

locals\(\)指的就是这一个cell里的可见

globals\(\)指的是这个notebook里所有的可见

可以直接在pandas里写SQL query 

```python
from pandasql import sqldf

def pysqldf(q):
  return sqldf(q, globals())

q = '''
SELECT * 
FROM df_uk_rain a LEFT JOIN df_right b
ON a.year = b.year
LIMIT 5
'''

pysqldf(q)
```

## Grouping

[参考链接](https://pandas.pydata.org/pandas-docs/stable/user_guide/groupby.html)

### Groupby \(和SQL差不多\)

下面的code，取了每个decade的三个column的最大值。除了//10\*10之外，也可以用floor\(\)替代//.

```python
df_uk_rain.groupby(df_uk_rain.year // 10 *10)['rain_octsep','outflow_octsep','rain_decfeb'].apply(lambda x: np.max(x, axis=0)).reset_index()
```

如果想对多个column进行groupby，也可 as\_index=False意思是不用groupby的group类别作为index

```python
df_uk_rain.groupby([df_uk_rain.year // 10 * 10, df_uk_rain['rain_octsep'] // 1000 * 1000], as_index=False)[['outflow_octsep','outflow_decfeb', 'outflow_junaug'] ].mean()
```

还可以把上面的操作都用上 groupby+lambda+apply

下面的这行里面有一个骚操作是 np.max\(x, axis = 0\), 是按照行找到最大值，存成一个numpy array。这里其实输出的结果应该和上上个例子一样，不过index reset好了（从0开始）。

```python
df_uk_rain.groupby(df_uk_rain.year // 10 *10)['rain_octsep','outflow_octsep','rain_decfeb'].apply(lambda x: np.max(x, axis=0)).reset_index() 
```

