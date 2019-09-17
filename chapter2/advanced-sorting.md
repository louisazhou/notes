---
description: 'Merge-Sort,  Quick Sort'
---

# Advanced Sorting

## Merge Sort 归并排序

{% embed url="https://www.hackerearth.com/zh/practice/algorithms/sorting/merge-sort/visualize/" %}

子问题：如何merge两个sorted array

```python
def merge(array1, array2):
    i=j=0
    results=[]
    while i<len(array1) and j<len(array2):
        if array1[i]<array2[j]:
            results.append(array1[i])
            i+=1
        else:
            results.append(array2[j])
            j+=1
    while i<len(array1):
        results.append(array1[i])
        i+=1
    while j<len(array2):
        results.append(array2[j])
        j+=1
    return results
```

Time: O\(n\)

Space: O\(1\)

```python
def merge_sort(array):
    if len(array)==0 or len(array)==1:
        return array
    
    mid = len(array)/2
    left = merge_sort(array[:middle])   #O(n)
    right = merge_sort(array[middle:])  #O(n)
    
    return merge(left, right)
```

每一层merge的时间复杂度是O\(n\) 一共有h=logn层，所以总的时间复杂度是nlogn

空间复杂度 call stack: logn 再加merge的空间消耗n 所以总的空间复杂度是O\(n\) 不过假如说我们传index但是不slicing，就是O\(logn\)

## Quick Sort 快排

{% embed url="https://www.hackerearth.com/zh/practice/algorithms/sorting/quick-sort/visualize/" %}

store index是一个隔板，左边都是比pivot小的，右边都是比pivot大的。所以一开始store index是从0开始移动

（..., store\_index）: &lt;pivot

\[store\_index, i\): &gt;pivot

\[i,...\) unknown

```python
def partition (lst, start, end, pivot_index):
    lst[pivot_index], lst[end] = lst[end], lst[pivot_index]
    store_index = start
    pivot = lst[end]
    for i in range (start, end):
        if lst[i]<pivot:
            lst[i], lst[store_index] = lst[store_index], lst[i]
            store_index+=1
    lst[store_index],lst[end]=lst[end], lst[store_index]
    return store_index
    
from random import randrange

def quick_sort(lst, start, end):
    if start>=end:
        return 
    pivot_index = randrange(start, end+1)
    new_pivot_index = partition(lst, start, end, pivot_index)
    quick_sort(lst, start, new_pivot_index-1)
    quick_sort(lst, new_pivot_index+1, end)
```

Time: On average O\(nlogn\) 最坏的情况是有序的，每次的pivot都选的特别差, worst case O\( $$n^{2}$$ \)    所以我们需要randrange

Space: O\(logn\) worst case  O\(n\)



### Find the k-th largest element in an array

Soln1: Brute Force: Sort the array in descending order, and return the element at index \(k-1\). O\(nlogn\)

Soln2: Heap

Soln3: QuickSort     Average O\(n\)  Worst Case $$O(n^2)$$ 

每次扔一半 左边是比pivot大的，右边是比pivot小的

```python
import random

def find_kth_largest(arr, k):
    left, right = 0, len(arr)-1
    while left<=right:
        pivot_idx=random.randint(left, right)
        new_pivot_idx=partition(left, right, pivot_idx, arr)
        if new_pivot_idx == k-1:
            return arr[new_pivot_idx]
        elif new_pivot_idx > k-1:
            right = new_pivot_idx -1
        else:
            left = new_pivot_idx + 1
            
def partition(left, right, pivot_idx, arr):
    pivot = arr[pivot_idx]
    new_pivot_idx = left
    arr[pivot_idx], arr[right] = arr[right], arr[pivot_idx]
    for i in range(left, right):
        if arr[i]>pivot:
            arr[i], arr[new_pivot_idx]=arr[new_pivot_idx], arr[i]
            new_pivot_idx += 1
    arr[right], arr[new_pivot_idx] = arr[new_pivot_idx], arr[right]
    return new_pivot_idx
    
arr = [3,2,5,1,4,0]
print(find_kth_largest(arr,1))
```

$$\begin{array}{l}{\text { Time complexity: }} \\ {\begin{aligned} T(n) &=T(n / 2)+O(n) \\ &=T(n / 4)+O(n / 2+n) \\ &=\ldots \\ &\left.=T\left(n / 2^{n} k\right)+O\left(n+n / 2+\ldots+n / 2^{\wedge} k\right)\right] \\ &=T\left(n / 2^{n} k\right)+O\left(n+2^{*}\left(1-0.5^{\wedge} k\right)\right) \\ \text { Let } n &=2^{\wedge} k \\ T(n) &=T(1)+O(2 n-2) \Rightarrow T(n)=O(n) \\ \text { Space complexity: } O(1) \end{aligned}}\end{array}$$ 

## 面试题目：

### 为什么基于比较的排序 时间复杂度下界是Omega\(nlogn\)

Given n numbers, there are n! possible permutations. For any value, x!=y, x&lt;y is half of the permutation. In each comparison, we are left with n!/2 permutation. In the end, we operate log\(n!\) to achieve sorting. 

Olog\(n!\)=O\(nlogn\)      \(Stirling Formula\)

### 如果有1Mb的数据，用哪个sorting algorithm? What about 10Mb,100Mb,1Gb,1Tb,1Pb?

1Mb:  Memory sorting 随便算法

1Tb: Disk sorting, External Sorting 外排，单机，多机，最后merge，写回磁盘 



