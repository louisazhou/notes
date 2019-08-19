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

Space: O\(logn\) worse case  O\(n\)

