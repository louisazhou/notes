# HashTable

## Dictionary 使用场景

TF-IDF \(Term Frequency- Inverse Document Frequency\) 统计词频时，可以用list来统计频率，但是使用list的缺点是每次更新频率的时间复杂度都是O\(n\), 只能遍历一遍、找到词、给词频+1； 随着单词变多，这个过程就变得低效了。

在Python中，可以使用dictionary的结构来实现高效的“更新词频“的需求。

```python
def words_to_frequencies (words):
    myDict={}
    for word in words:
        if word in myDict:
            myDict[word]+=1
        else:
            myDict[word]=1
    return myDict
```

Time Complexity: O\(n\)

Space Complexity: O\(1\) 如果我们不算return值的话，那没有额外空间的消耗



### list vs. dictionary 

![](https://cdn.mathpix.com/snip/images/y9O3fSu8KAqCz-ToOpNBDbA38tZIL1SJ-Ci2qFFeSVY.original.fullsize.png)

![](https://cdn.mathpix.com/snip/images/3azTsxI4SoO1qqsyI08qpJ-39AS2_DOFSPIFLplyEgw.original.fullsize.png)



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
2. Key必须是immutable的类型 所以X={\[1\]:0}不对，list unhashable

像Y={\(1,2\):10}就行，str也行

## Dictionary Implementation

字典，hash的实现里，不需要去查找每个元素了，而是有一系列的buckets。每次有一个key，先算hashfunction（key），通过这个值，来得到bucket的序号。hashfunc可以接收任何变量，最后它能计算出一个integer，下面的code中，array\_size是一个常数，也就是bucket size. 用算好的hash来%array\_size, 得到index，意味着最后要把它放到index的桶里去找。

```python
hash = hashfunc(key)
index = hash % array_size
```

### Hash Collision 

假设两个key，x和y，本身x!=y, 但是hash\(x\)==hash\(y\), 就有了collision，因为它们被映射到了同一个bucket里，所以在第二行一定一样。

此外，因为array\_size是一个定值，即使1不一样了，到2还是有可能一样，那还是会collision。

 如果有perfect hashing, array\_size又是无穷大（这在实际情况中是不存在的），就不会有冲突。

那么 现实中如何解决hash collision？

策略一： open addressing 

![](https://cdn.mathpix.com/snip/images/pUAj2XwLpyYfoIPIcnZD3vFEnzo_1c3bZEwDIRy7QiU.original.fullsize.png)

如果算出来的数值被占了，发生了hash collision，就占用它最近的没有被占用的单元。

策略二：separate chaining 

![](https://cdn.mathpix.com/snip/images/k3-3lDVqKUvdqMWLZKpb6wVyjXe5-suCvXR3ZPL23c4.original.fullsize.png)

在每一个bucket里，就做成链表，把下一个人直接占在它后面 worst case就是，如果bucket=1\(概率极低\), 那么就变成了singly linked list，那么查找的时间复杂度又是O\(n\)了 不过，on average 还是可以O\(1\)



Time Complexity of different operations on dictionary 

\|Operation \| Avg \| Worst \| 

Search O\(1\) O\(n\) 

Add O\(1\) O\(n\) 

Delete O\(1\) O\(n\) 

Update O\(1\) O\(n\)





