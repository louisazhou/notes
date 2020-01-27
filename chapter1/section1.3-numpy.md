---
description: (unfinished) Numpy的ndarray、数学公式和矩阵使用、数据切片
---

# Numpy

## NDarray and Matrix

```python
lst = [[1,2,3],[4,5,6]] #2*3 array
a = np.array(lst) #create array from list


print(a.shape)
#(2,3) 因为这是一个2*3的矩阵

print(a.ndim)
#2 2 dimensions

print(a.dtype)
# numpy里的data type得是同一种类型，才能保证快速计算

print(a.size)
#6 总共有6个元素 
```

arange\(\)和linspace\(\)都可以生成一串数，不过linspace生成的是指定gap的数目，可以是float。

```python
b = np.arange(0, 10, 2, dtype='float') # create 1d array, [start, stop)
print(type(b)) # class 'numpy.ndarray'
print(b) #[0. 2. 4. 6. 8.]
print(b.shape) #(5,)

c = np.linspace(1.5, 2.5, 9) # create 1d array with float, [start, stop]
print(type(c)) # class 'numpy.ndarray'
print(c) #[1.5   1.625 1.75  1.875 2.    2.125 2.25  2.375 2.5  ]
print(c.shape) #(9,)
```

{% hint style="info" %}
**reshape\(\)** - 改变数组的形状。通过reshape生成的新数组和原始数组共用一个内存，也就是说，若更改其中数组的元素，另一个数组也将发生改变。使用时可以用np.reshape\(data, \(2,4\)\)或者data.reshape\(2,4\)

**arange\(\)** - 创建一个一维的等差数列数组，与Python中的range\(\)函数类似。区别在于，np.arange\(\)返回的是一个numpy数组，而Python中的range\(\)函数返回的是一个列表。arange允许步长为小数，而range不允许。
{% endhint %}

```python
h = b.reshape(5, 1)
print(h.shape)

i = c.reshape(-1, 1) #不知道该几行，但是只想要一列 可以用-1
print(i)
print(i.shape)

j = d.reshape((2, 3, 1))
print(j)
print(j.shape)
```

如果混着用slicing和integer indexing来获取matrix中的某一列，那么integer indexing不会保留数据原始的rank，但是slicing可以，比如

```python
a = np.array([[1,2,3,4], [5,6,7,8], [9,10,11,12]])

r1 = a[0, :] # rank 1 view of the first row
print(r1, r1.shape)
# [1 2 3 4] (4,)


r2 = a[0:1, :] # rank 2 view of the first row 
print(r2, r2.shape)
# [[1 2 3 4]] (1, 4)
```

当然也可以reindex一下 和之前一样，-1表示系统来根据element的个数指定行数。

```python
r1 = r1.reshape(-1, r1.shape[0])
print(r1, r1.shape)
# [[1 2 3 4]] (1, 4)
```

Numpy自己的加减乘除是一一对应的运算，比如以下都是element-wise

```python
a = np.array([[10, 11, 12], [13, 14, 15]])
print(a.shape)
print(a)
b = np.arange(1, 7).reshape((2, 3))
print(b.shape)
print(b)

# element-wise calculation:
print(a + b)
print(a - b)
print(np.add(a, b))
print(a * b)
```

如果想要做矩阵乘法，要用

```python
# Matrix product
print(np.dot(a, b.transpose())) # 2x3, 3x2
print(a.T.dot(b)) # 3x2, 2x3
```

沿着列或者行的加减法

```python
# calculation along row or column index, calculate by the index of the axis which is set
print(np.sum(a, axis = 0)) #[23 25 27]

print(np.sum(a, axis = 1)) #[33 42]
```

**轴（axis）**：每一个线性的数组称为是一个轴，也就是维度（dimensions）。比如，二维数组相当于是两个一维数组，其中第一个一维数组中每个元素又是一个一维数组，所以一维数组就是ndarray中的轴，**第一个轴（也就是第0轴）**相当于是底层数组，**第二个轴（也就是第1轴）**是底层数组里的数组。

很多时候可以声明axis。**axis=0 or 'index'**，表示沿着第0轴进行操作，即对每一**列**进行操作, apply function to each column；**axis=1 or 'columns'**，表示沿着第1轴进行操作，即对每一**行**进行操作, apply function to each row。

**秩（rank）**：维数，一维数组的秩为1，二维数组的秩为2，以此类推。即轴的个数。

![](../.gitbook/assets/image%20%2839%29.png)

用True/False可以很容易的为np array 做masking 比如

```python
a = np.array([[1,2,3,4], [5,6,7,8], [9,10,11,12]])
a_idx1 = (a < 5)
a_idx2 = (a >=5) & (a <= 10) 

print(a[a_idx1]) # return the corresponding array
print(a[a_idx2]) # return the corresponding array

print(a[(a > 5) & (a < 10)]) # single statement
```

Broadcasting \[[详情看这个](https://docs.scipy.org/doc/numpy-1.15.0/user/basics.broadcasting.html)\]  针对一行或一列做集体操作，比如给一行加一个数，给另一行加另一个数。

```python
a = np.array([[10, 11, 12], [13, 14, 15]])
a += np.array([-1, -2, -3]) # add [-1, -2, -3] to all columns for each row
a += np.array([[-1], [-2]]) # add [[-1], [-2]] to all rows for each column
```

numpy里的random，可以用来有放回抽样、无放回抽样、按照一定概率抽样、生成两个数据点范围内的random number

```python
np.random.seed(42) # same seed can repro the same scenario so it is deterministic

print(np.random.rand(2,2)) # Random numbers between [0,1) of shape 2,2
print(np.random.randn(2,2)) # Normal distribution with mean=0 and variance=1 of shape 2,2
print(np.random.randint(0, 10, size=[2,2])) # Random integers between [0, 10) of shape 2,2
print(np.random.random()) # One random number between [0,1)
print(np.random.random(size=[2,2])) # Random numbers between [0,1) of shape 2,2
print(np.random.choice(['a', 'e', 'i', 'o', 'u'], size=10)) # Pick 10 items from a given list, with equal probability
print(np.random.choice(['a', 'e', 'i', 'o', 'u'], size=10, p=[0.3, .1, 0.1, 0.4, 0.1])) # Pick 10 items from a given list with a predefined probability 'p'
print(np.random.choice(np.arange(100),size=[10,3],replace=True)) # get 10x3 random samples from [0-99] with replacement 
```

也可以用np手动做train test split

np.set\_printoptions\(threshold = np.nan\) 可以让numpy显示出来所有中间那些被隐藏掉的elements。

\[[其他的一些操作细节](http://blog.sciencenet.cn/blog-3031432-1064033.html)\]



  


