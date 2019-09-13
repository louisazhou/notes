---
description: (unfinished) 引用、浅拷贝和深拷贝
---

# Section1.1 Reference, Deep Copy and Shallow Copy

## Reference

![Reference](../.gitbook/assets/image%20%2814%29.png)

```python
b = [1 , 2]
a = [b, 3, 4]

c = a
print c
>>> [[1, 2], 3, 4]
id(a)
>>> 4408148552
id(c)
>>> 4408148552
```

c = a 表示 c 和 a 指向相同的地址空间，并没有创建新的对象。在Python里，变量名永远都是reference，我们是把pass by reference. 

## Shallow Copy

![Shallow Copy](../.gitbook/assets/image%20%2816%29.png)

```python
import copy
d = copy.copy(a)
print d
>>>[[1, 2], 3, 4]

id(a)
>>>4408148552
id(d)
>>>4408199792

id(a[0])
>>>4408022944
id(d[0])
>>>4408022944

d[0][0] = 5
print a
>>> [[5, 2], 3, 4]
```

d = copy.copy\(a\) 创建了一个新对象，复制了原有对象的引用。

{% code-tabs %}
{% code-tabs-item title="如果不是compound objects，而只是简单的list，不会受到影响" %}
```python
a = [1, 22]
b = a[:]
b[0] = 3

print(a)
>>> [1,22]


#或者

import copy
a = [1, 22]
b = copy.deepcopy(a)
b[0] = 3

print(a)
>>> [1,22]
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/image%20%2820%29.png)

对象复制\(y=x\)和浅复制\(z=np.copy\(x\)\)一个list，list元素其实都是那些，所以x和yID相同，都指的同一片空间\[1,2,3\]。

## Deep Copy

![Deep Copy](../.gitbook/assets/image%20%288%29.png)

e = copy.deepcopy\(a\) 新建了一个新对象，完整的在内存中复制原有对象。

```python
e = copy.deepcopy(a)
print e
>>> [[1, 2], 3, 4]

>>> 4408148552
id(e)
>>> 4408394792

id(a[0])
>>> 4408022944
id(e[0])
>>> 4408398432

e[0][0] = 5
print a
>>> [[1, 2], 3, 4]
```

![](https://cdn.mathpix.com/snip/images/UCb7WwJga-UCxh0_HIwESpPCa3172Q6ITQW6RrQWsM8.original.fullsize.png)

上面这个例子，左图是

```python
a = [[1]]
b = a[:]
b.append(2)
print(a)
>>> [[1]]

print (id(a))
>>> 139751593534312
print (id(b))
>>> 139751593536616
print (id(a[0]))
>>> 139751593456272
print (id(b[0]))
>>> 139751593456272
```

而右图是

```python
a = [[1]]
b= a[:]
a[0].append(2)
print(b)
>>> [[1,2]]
b.append(3)
print(b)
>>> [[1, 2], 3]
print(a)
>>> [[1,2]]
```

使用深拷贝时，需要注意以下两个问题:

* 递归对象拷贝: Recursive objects \(compound objects that, directly or indirectly, contain a reference to themselves\) may cause a recursive loop.
* 大对象拷贝: Because deep copy copies everything it may copy too much, e.g., administrative data structures that should be shared even between copies.

以下是Python给的解释

The difference between shallow and deep copying is only relevant for compound objects \(objects that contain other objects, like lists or class instances\):

* A shallow copy constructs a new compound object and then \(to the extent possible\) inserts references into it to the objects found in the original.
* A deep copy constructs a new compound object and then, recursively, inserts copies into it of the objects found in the original.

## 函数传值

```python
def changeme(mylist):
    mylist.append([1,2,3,4])
    return
    
mylist=[10,20,30]
changeme(mylist)
print(mylist)
```

这个时候append完了以后是\[10, 20, 30, \[1, 2, 3, 4\]\] , 这是因为对mylist reference的对象上直接操作了。

但是，如果只是函数传值，传值后变量该是什么其实还是什么，传的只是变量指向的那个单元

再看两个例子

```python
def funB(a_list):
    a_list.append(1)

def funcA():
    a_list = [2,3]
    funcB(a_list)
```

![](https://cdn.mathpix.com/snip/images/akQogfX5WmqDzxF8iE75AxPgBSKQIoLQ0xT4jwjiOHo.original.fullsize.png)

发生的是上图的操作，append了之后改变了funcA（）

```python
def funB(a_list):
    a_list=[5,6]

def funcA():
    a_list = [2,3]
    funcB(a_list)
```

![](https://cdn.mathpix.com/snip/images/UlQpYk8qrj_5Y8XNAaNCm4MTu0F4Dc2QhWMIrblp0Fs.original.fullsize.png)

发生的是上图的事情

