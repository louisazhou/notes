---
description: 普通版、2D版、万金油版、有重复元素版
---

# Binary Search

## 二分查找思想

每次缩小一半的查找范围，和中间值比较。大于中间值往左，小于中间值往右。通过L=mid+1 or R=mid-1改变LR边界值。

```python
def binary_search(nums, target):
 if not nums:
  return None
 left, right = 0, len(nums)-1
 while left<=right:
  mid=(left+right)/2
  if nums[mid]<target:
   left=mid+1
  elif nums[mid]>target:
   right=mid-1
  else 
   return mid
 return None 
```

Time: O\(logn\)

## 2D的二分查找

一个二维坐标可以和一维坐标联系，4\*4的matrix，（2，2）代表2\*4+2个元素比它小。index=row\_index\*m+col\_index.\(m: number of cols\).

所以，row\_index=index/m; col\_index=index%m.

第一个index是0，最后一个index是n\*m-1。所以初始化的left=0, right=n\*m-1.

```python
def binary_2D (matrix, target):
    if matrix == None or len(matrix) ==0:
        return None
    n,m=len(matrix),len(matrix[0])
    left,right=0,n*m-1
    while left<=right:     #当范围内还有元素时继续搜索，L>R时停止搜索
        mid = (left+right)/2 #python 2
        row=mid/m
        col=mid%m
        if matrix[row][col]>target:
            right=mid-1
        if matrix[row][col]<target:
            left=mid+1
        else:
            return(row,col)
    return None #跳出循环，还没找到元素
```

Time: O\(log\(n\*m\)\)



{% hint style="info" %}
1. 每次搜索都比上次少一半&lt;------&gt;所以我们必须保证每一次的搜索范围都在减小
2. 终止时剩下1～2个元素，才能确认target是否在这两个元素之中&lt;----&gt;淘汰元素时要保证target不会被淘汰
{% endhint %}



## 最接近某元素的element

eg \[1,2,5,9\] target=3, index=1, number=2

`错误示范` 

因为如果（0，1），剩两个数或者剩下一个数，都会在这里做死循环；违反了上面中的原则1，搜索空间并没有减小，因为line5的mid永远都是一个值。

不能让left和right之间没有元素，不然会让left和right来回传值, 所以while的条件必须要在left和right之中隔一个值

{% tabs %}
{% tab title="错误示范" %}
```python
def binary_search (nums, target):
    left=0
    right=len(nums)-1
    while left<=right:
        mid=(left+right)/2
        if nums[mid]>target:
            right=mid
        elif nums[mid]<target:
            left=mid
        else:
            return mid
return None
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
1. 要在LR之间隔一个元素
2. Post-processing 最后剩下LR还没有比较
{% endhint %}

`正确做法`

{% tabs %}
{% tab title="正确做法" %}
```python
def binary_search (nums, target):
    left=0
    right=len(nums)-1
    while left<right-1:
        mid=(left+right)/2
        if nums[mid]>target:
            right=mid
        elif nums[mid]<target:
            left=mid
        else:
            return mid
return left if abs(nums[left]-target)<abs(nums[right]-target) else right
```
{% endtab %}
{% endtabs %}

Time: O\(logn\)

## 有重复元素，返回第一个

> 如果找不到，return-1 （面试时，注意和面试官沟通，回复-1还是None）

1. num\[mid\]&lt;target, 左半边不要了，一定在右边，可以+1\(left=mid+1\)
2. num\[mid\]&gt;target, 右半边不要了，一定在左边，但是含mid\(right=mid\)
3. num\[mid\]=target, 虽然找到了，但也有可能不是第一个，所以right=mid

```python
class Solution(object):
  def firstOccur(self, array, target):
    """
    input: int[] array, int target
    return: int
    """
    # write your solution here
    if not array or len(array)==0:
        return -1
    
    left, right = 0, len(array)-1
    
    while left<right-1:
      
      mid = (left+right)//2
      
      if array[mid]==target:
        right=mid
      elif array[mid]<target:
        left=mid+1
      elif array[mid]>target:
        right=mid-1
    
    if array[left]==target:
      return left
    if array[right]==target:
      return right

    return -1  
```

## 有重复元素，返回最后一个

和First Occurrence的区别在 

1. mid=target时往左还是往右: 右

2. Post-Processing的顺序先后：先检查右边

```python
class Solution(object):
  def lastOccur(self, array, target):
    """
    input: int[] array, int target
    return: int
    """
    # write your solution here
    if not array or len(array)==0:
        return -1
        
    left, right = 0, len(array)-1
    
    while left<right-1:
      
      mid = (left+right)//2
      
      if array[mid]==target:
        left = mid
      elif array[mid]<target:
        left = mid+1
      elif array[mid]>target:
        right = mid-1
    
    if array[right]==target:
        return right
    if array[left]==target:
        return left
    
    return -1
```



## 应用

Pull Request有很多个版本，如果有一个version有bug，在version7发现了，快速找到这个有bug的version的第一个version



## 题目

### Sqrt（）

找一个最接近于平方根的整数, floor

#### 方法一：试

```python
def sqrt(n):
    val = 1
    while val*val<=n
        val+=1
    return val-1
```

Time O\( $$\sqrt(n)$$ \)

Space O\(1\)

#### 方法二：binary search

if mid\*mid&lt;n: go right \[mid, right\]

if mid\*mid&gt;n: go left \[left, mid\]

if mid\*mid==n: return mid

```python
def sqaure_root(n):
    if n<=1:
        return n
    left, right = 1,n #其实可以写n/2 因为一定在1～n/2之间
    while left < right-1:
        mid = (left+right)/2
        midsq = mid*mid
        if midsq ==n:
            return mid
        elif midsq>n:      #其实应该是right=mid-1
            right = mid
        else:
            left = mid
    if right*right<=n:
        return right
    else:
        return left
```

#### Find the bug, sorted release, UNKNOWN size

1. Find the end 倍增法
2. Binary Search

```python

```

Time: O\(log 2\(first\_bug\_version\)\) 



