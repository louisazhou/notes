---
description: 'Dictionary, Set, 例题'
---

# HashTable

## 底层机制

set是简化版hashtable，而hashtable的本质其实是array（或者说Python里的list），因为list的本质就是array index和value的映射。所以关键问题是把key转换成index。一般分成3步：

1. 通过key和hash function计算它的hash\_number=hash\(key\) 这一步虽然可能有collision但是可能性比较低
2. index=hash\_number%N, N是array size。 这一步有很大概率有collision，取决于N的大小
3. 如果有hash collision，两种方法； 如果没有，list\[index\]=value

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

### Example: 查找高频

如果要找到频率最高的word，分两步1. 遍历，找到最大的值； 2. 找到list，返回

```python
def most_common_words (freqs):
    best = max(freqs.values())
    words = [
        key                                        #代表元 expression    
        for key, val in freqs.items()              #for循环
        if val==best                               #判断条件
        ]
    return (words, best)   
```

List comprehension的写法：\[expression for elem in collection if condition\]

相类似的字典也有dictionary comprehension，只需要把\[\] 换成{}

{key\_expression: value\_expression for value in collection if condition}



### Example：合并大小写的频次

mydic = {'a': 10, 'b': 6, 'A': , 'B': }

```python
new_mydic={
    k.lower(): mydict.get(k.lower(), 0) + mydic.get(k.upper(),0)
    for k in mydic.keys()
}

#output: {'a': 17, 'b': 34, 'z': 3}
```



### Example: Top K freq words

第一步：先把词频算出来 

第二步：sorting，按照单词的频率排序，而且是频率加负号，按负的频率排序，频率越大顺序越小。 或者也可以用key = lambda kv: kv\[1\], reverse=True 

sorted的syntax:   sorted\(iterable, key=key, reverse=reverse\)

再次注意，dictionary没有顺序！所以不能直接输出前x个。

```python
def top_k (nums, k):
    freq = {}
    for num in nums:
        if num in freq:
            freq[num]+=1
        else:
            freq[num]=1
    sorted_tuples = sorted(freq.items(), key=lambda kv: -kv[1]) #按照tuple第一个元素的相反数排序
    return [x[0] for x in sorted_tuples][:k]
```

前面部分是O\(n\), sort部分都是O\(nlogn\), 最后一步还是O\(n\), 最终就是O\(nlogn\)



### Example: Palindromic Testing

给一个单词，测试它能否被写成回文数的形式。能满足回文的条件，那就必须满足除了中间的数可能一个外，其余的都是偶数个；换句话说，奇数的字母只有1个，其余都是偶数个。所以我们需要建立字母到频率的字典，计算频率的个数。

```python
def is_palindromic(word):
    freq = {}
    for i in range(len(word)):
        if word[i] in freq:
            freq[word[i]]+=1
        else:
            freq[word[i]]=1
    odd_cnt = 0
    for key in freq.key():
        if freq[key]%2==1:
            odd_cnt += 1
            if odd_cnt > 1:
                return False
    return True 
```

Time Complexity: O\(n\)

Space Complexity: O\(C\) C是distinct chars in string 



### Example: Nearest repeated entries in an array

重复的单词最近的距离 key是单词，value是位置，距离只需要减一下、更新为距离。

```python
def nearest_repeat (arr):
    word_ind = {}
    dist = float('inf')
    for i in range(len(arr)):
        if arr[i] in word_ind:
            dist = min(dist, i -word_ind[arr[i]])
        word_ind[arr[i]] = i
    return dist
```

Time Complexity: O\(n\)

Space Complexity: O\(C\) C是distinct entries in arr



### Example: Longest Contained Range

brute-force: sort, O\(nlogn\), search, O\(n\)

set: “中心开花，左右出发“，以任何一个元素出发，往两边扩展

1. 先放进集合里
2. pop 任取元素，左右扩展

```python
def longest_contained_range(arr):
    unprocessed = set(arr)
    maxlen = 0
    while unprocessed:
        elem = unprocessed.pop()
        lower = elem - 1
        while lower in unprocesssed:
            unprocessed.remove(lower)
            lower = lower-1
        upper = elem + 1
        while upper in unprocessed:
            unprocessed.remove(upper)
            upper = upper+1
        maxlen = max(maxlen, upper-lower-1)
    return maxlen
```

时间 O\(n\)

空间 O\(C\)

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

python2 用grades.keys\(\) return的是\[‘A’, 'A', 'A+', 'B'\] （value的list）

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



## Set

有时我们并不关心value，只关心key本身，这个时候dict就退化成了set，集合。集合就是只包含了key的hashtable。需要注意的是，集合本身可变mutable，但是里面包含的元素不变。所以增删查改咯～

### Add

```python
x = {"a","b","c","d"}
x.add("e") #one element

x. update({"e", "f"}) #multiple elements
```

### remove 

如果remove的元素不在，使用x.remove会报keyerror； 但是使用x.discard\("a"\)不会报

如果从中随意删除任何一个\(因为set没有顺序，所以真的不知道删了哪个... \)，x.pop\(\)； 如果是清空，x.clear\(\) ；

### union

下面是集合操作的取并集 这个x和y是用set（）把list变成集合

x = set\(\["Postcard",  "Radio", "Telegram"\]\)

y = set\(\[ "Radio", "Television"\]\)

print x.union\(y\) 

### 更多

![](https://cdn.mathpix.com/snip/images/77rv6-zY5MX29CrX7WnJlZ2CnYKgkN2eZtkMrfn-hUU.original.fullsize.png)

set也有set comprehension

{expression for value in collection if condition}

```python
squared = {x**2 for x in [1,1,2]}
```



## Recap

1. One to one mapping 
2. &lt;key, value&gt; pair, key map to value
3. Key, no duplicates 
4. Value, allow duplicates 
5. hash\_set is a set {1,3}, it only contains keys 

In python, hash\_set is set; hash\_table is dictionary 

## 题目

### 2 sum sorted

因为是sorted，所以可以用2 pointers的方法，利用增加和减小的单调性：一个往前移一个往后移；如果现在的和比target小，移动i往后；如果比现在的target大，移动j往前。

```python
# Time O(n)
# Space O(1)
```

### 2 sum unsorted

利用set的性质，经过的数字都存下来；用target的数字和当前经过的数字相减，如果得到的结果在之前的set里，就找到了，return true。否则，false。

```python
# Time O(n)
# Space O(n)
```

### 2 sum with duplicates

\[10, 2, 2, 13, 2\], target 4 

* 如果题目要output多少对，用一个dict，存count： {10:1, 2:2, ...}
* 如果题目要output哪些index的组合，dict里是list of index: {10:\(0\), 2:\(1,2\), ...}

### 3 sum unsorted

方法一：走n遍的2 sum。O\( $$n^2$$ \)

方法二：先排序，反正已经O\( $$n^2$$ \)了，就算先排序也不会影响这个时间复杂度，那就排序吧！可以让空间复杂都变成O\(1\)。

### 4 sum unsorted

方法一：走n遍的3 sum。O\( $$n^3$$ \)

方法二：变成2个2sum，如何找到pair of 2 sum, 和是target。  
1. 先配对 2. sort一遍或者hashset  需要注意的是，在配对的时候要检查它是否是用过的 

### k sum

在array里选k个数字组成sum=target。DFS，k个数字的subset

### 2 difference sorted

2 pointers，错一位同向而行。因为如果是相向而行，目前的值大于target，那么移动i或者j都行，所以到底移动哪个？ 两个都尝试时间复杂度就会变高。同向而行，如果当前值大于target，移动i向前；如果当前值小于target，移动j向前。

### 2 difference unsorted





