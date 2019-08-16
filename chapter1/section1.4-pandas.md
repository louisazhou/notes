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

## index指定

series是一个维度，一维的，所以没有n\*1或者1\*n之说。

```python
# Series from list
s = pd.Series([1,3,5,np.nan,6,8], index = ['a','b','c','d','e','f'])
```

## Indexing and Slicing 

注意下面的区别，第一种，只有一对\[\]取出来的是series，不是dataframe。

```text

```

