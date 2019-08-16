# HashTable

## Dictionary 使用场景

TF-IDF 统计词频时，可以用list来统计频率，但是使用list的缺点是每次更新频率的时间复杂度都是O\(n\), 只能遍历一遍、找到词、给词频+1； 随着单词变多，这个过程就变得低效了。

在Python中，可以使用dictionary的结构来实现高效的“更新词频“的需求。

### list vs. dictionary 

![](https://cdn.mathpix.com/snip/images/y9O3fSu8KAqCz-ToOpNBDbA38tZIL1SJ-Ci2qFFeSVY.original.fullsize.png)

list存储的是单独的元素，而字典存储的是对应关系（mapping），或者叫它映射。我们叫它Key-value pair，键值-存储值。

对任何一个数据结构，都需要知道 创建、增、删、查、改



### 创建

```python
my_dict = {}
grades = {'Ana': 'B', 'John': 'A+', 'Denise': 'A', 'Katy': 'A'}
```

### 查找

字典没有顺序，只能按key查找，所以必须是存在在字典里的元素，不然直接run下行就会出现key error

```python
grades['John']
```

还有一个函数，get 如果找不到，可以给None 

```python
print (grades.get ('Bob'))
```

判断key在不在字典里，就可以用lookup的操作，

```python
'John' in grades  #return False
'Daniel' in grades #return True 
```

### 增改

加一个新的entry、改已有的syntax，都可以很简单的

```python
grades['John'] = 'A'
grades['Ana'] = 'B+'
```

### 删除

```python
del grades['Ana'] #从字典里取这个key，然后删了
grades.pop('Ana') 
```

### 拿到key的集合

python2 用grades.keys\(\) return的是\[\] （value的list）

python3 的return是 （value的view）

view都不会占额外的空间，就像是range和xrange的区别

当然也可以用list\(dict.keys\(\)\) ，占空间呗

### 拿到value的集合

类似👆

### requirements for keys

1. Key必须unique，不能重复；第二次的一样的值会把第一个覆盖掉
2. Key必须是immutable的类型



