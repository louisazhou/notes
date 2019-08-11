---
description: (unfinished) Numpy的ndarray、数学公式和矩阵使用、数据切片
---

# Section1.3 Numpy

## NDarray and Matrix

```python
lst = [[1,2,3],[4,5,6]] #2*3 array
a = np.array(lst) #create array from list

print(type(a))
#output
#<class 'numpy.ndarray'>

print(a[0])
#output
#[1 2 3]

print(a[0][1])
#output
#2

print(a.shape)
#(2,3)

print(a.ndim)
#2

b = np.arange(0, 10, 2, dtype='float') # create 1d array, [start, stop)
print(type(b)) # class 'numpy.ndarray'
print(b)
#[0. 2. 4. 6. 8.]
print(b.shape)
#[5,]

h = b.reshape(5,1)
print(h.shape)
#(5, 1)

c = np.linspace(1.5, 2.5, 9) # create 1d array with float, [start, stop]
print(type(c)) # class 'numpy.ndarray'
print(c)
#[1.5   1.625 1.75  1.875 2.    2.125 2.25  2.375 2.5  ]
print(c.shape)
(9,)

i = c.reshape(-1, 1) #系统自动计算行，规定1列
print(i)
#[[1.5  ]
# [1.625]
# [1.75 ]
# [1.875]
# [2.   ]
# [2.125]
# [2.25 ]
# [2.375]
# [2.5  ]]
print(i.shape)

d = np.zeros((2, 3)) # all zeros
print(type(d)) # class 'numpy.ndarray'
print(d)
#[[0. 0. 0.]
# [0. 0. 0.]]

j = d.reshape((2, 3, 1)) #创建两个3*1的矩阵 构成三位数组
print(j)
#[[[0.]
#  [0.]
#  [0.]]

# [[0.]
#  [0.]
#  [0.]]]
print(j.shape)
#（2，3，1）

e = np.ones((2, 3)) # all ones
print(type(e)) # class 'numpy.ndarray'
print(e)
#[[1. 1. 1.]
# [1. 1. 1.]]

f = np.full((2, 3), 9) # constant matrix
print(type(f)) # class 'numpy.ndarray'
print(f)
#[[9 9 9]
# [9 9 9]]

g = np.eye(3) # 3 x 3 identity matrix
print(type(g)) # class 'numpy.ndarray'
print(g)
#[[1. 0. 0.]
# [0. 1. 0.]
# [0. 0. 1.]]
```

{% hint style="info" %}
**reshape\(\)** - 改变数组的形状。通过reshape生成的新数组和原始数组共用一个内存，也就是说，若更改其中数组的元素，另一个数组也将发生改变。使用时可以用np.reshape\(data, \(2,4\)\)或者data.reshape\(2,4\)

**arange\(\)** - 创建一个一维的等差数列数组，与Python中的range\(\)函数类似。区别在于，np.arange\(\)返回的是一个numpy数组，而Python中的range\(\)函数返回的是一个列表。arange允许步长为小数，而range不允许。
{% endhint %}

**轴（axis）**：每一个线性的数组称为是一个轴，也就是维度（dimensions）。比如，二维数组相当于是两个一维数组，其中第一个一维数组中每个元素又是一个一维数组，所以一维数组就是ndarray中的轴，**第一个轴（也就是第0轴）**相当于是底层数组，**第二个轴（也就是第1轴）**是底层数组里的数组。

很多时候可以声明axis。**axis=0**，表示沿着第0轴进行操作，即对每一**列**进行操作；**axis=1**，表示沿着第1轴进行操作，即对每一**行**进行操作。

**秩（rank）**：维数，一维数组的秩为1，二维数组的秩为2，以此类推。即轴的个数。

![](../.gitbook/assets/image%20%283%29.png)







\[[其他的一些操作细节](http://blog.sciencenet.cn/blog-3031432-1064033.html)\]



  


