---
description: 冒泡、选择、插入
---

# Basic Sorting

## Bubble Sort 冒泡排序

For each pass, we will move left to right swapping adjacent elements as needed. Each pass moves the next largest element into its final position. 

以下是它的visualization

{% embed url="https://www.hackerearth.com/zh/practice/algorithms/sorting/bubble-sort/visualize/" %}

简单说，内外两个循环，外循环控制这是第几轮交换（1轮让max在最后，2轮让second largest在倒数第二...），内循环控制相邻位置的交换。

{% tabs %}
{% tab title="solution 1" %}
```python
def bubble_sort (list):
    for n in range (len(list)-1,0,-1):
        for i in range(n):
            if (list[i]>list[i+1]):
                list[i],list[i+1]=list[i+1],list[i]
    return list
```
{% endtab %}

{% tab title="solution 2" %}
```python
def bubble_sort (list):
    for n in range(0,len(list)):
        for i in range (len(list)-n-1):
            if (list[i]>list[i+1]):
                list[i],list[i+1]=list[i+1],list[i]
    return list
```
{% endtab %}
{% endtabs %}

Space O\(1\)   \(in place\)

Time O\( $$n^{2}$$ \)

## Selection Sort 选择排序

For each pass, we will move left to right looking for the next largest value. Once that is found, it will be swapped into its final position.

以下是它的visualization

{% embed url="https://www.hackerearth.com/zh/practice/algorithms/sorting/selection-sort/visualize/" %}

先要解决的子问题：找到一个array中最大的数

```python
def find_max(array):
    max_value = array[0]
    for i in range (len(array)):
        if array[i]>max_value:
            max_value=array[i]
    return max_value
```

然后推广到每一层

{% tabs %}
{% tab title="solution 1 find\_max" %}
```python
def selection_sort (array):
    for i in range(len(array)-1,0,-1):
        max_index = 0
        for j in range(i+1):
            if array[max_index]<array[j]:
                max_index=j
        array[i], array[max_index] = array[max_index], array[i]
```
{% endtab %}

{% tab title="solution 2 find\_min" %}
```python
def selection_sort (array):
    for i in range (len(array)):
        min_index = i
        for j in range (i, len(array)):
            if array[j]<array[min_index]:
                min_index=j
        array[min_index],array[i]=array[i],array[min_index]
```
{% endtab %}
{% endtabs %}

Space O\(1\)   \(in place\)

Time O\( $$n^{2}$$ \)

### 3 stacks to sort an array

## Insertion Sort 插入排序

Insertion sort is based on the idea that one element from the input elements is consumed in each iteration to find its correct position i.e, the position to which it belongs in a sorted array.

It iterates the input elements by growing the sorted array at each iteration. It compares the current element with the largest value in the sorted array. **If the current element is greater, then it leaves the element in its place and moves on to the next element else it finds its correct position in the sorted array and moves it to that position.** This is done by shifting all the elements, which are larger than the current element, in the sorted array to one position ahead.

{% embed url="https://www.hackerearth.com/zh/practice/algorithms/sorting/insertion-sort/visualize/" %}

先要解决的子问题：如何在数组中插入一个数，让它能在correct position。

```python
def insert_num(array, n):
    idx=len(array)-1
    array.append(n)
    while idx>=0:
            if array[idx]>array[idx+1]:
                array[idx],array[idx+1]=array[idx+1],array[idx]
            idx-=1
    return array
```

### 方法一：

调用insert\_num这个help function，实现insertion sort

{% code title="方法一 O\(N\)的空间消耗" %}
```python
def insert_sort(array):
    new_array=[]
    for i in range (len(array)):
        insert_num(new_array, array[i])
    return new_array
```
{% endcode %}

Space O\(n\)                   在line2中建立了一个空数组，往空数组里加元素，多占了n   

Time O\( $$n^{2}$$ \)

所以这不是最优解

### 方法二：

像轮回洗牌似的操作, in place地进行插入排序。这个程序看起来会比上面的更简单，但是tricky的是要提前想明白所定义变量的物理意义。i之前的元素是已经排序好的，k是用来在内层循环中一个个往前找到cur应该处的位置的，在找的同时把元素一个个换过去，最后cur是现在需要排序的对象。

{% tabs %}
{% tab title="First Tab" %}
```python
def insert_sort (array):
    for i in range (len(array)):
        cur = array[i]
        k = i
        while k>0 and cur<array[k-1]:
                array[k]=array[k+1]
                k-=1
         array[k]=cur   
```
{% endtab %}

{% tab title="Second Tab" %}
```python
for i in range (len(array)):
      smallest = i
      for j in range (i+1,len(array)):
        if (array[j]<array[smallest]):
          smallest=j
      array[i],array[smallest]=array[smallest],array[i]
    return array
```
{% endtab %}
{% endtabs %}

Space O\(1\)   \(in place\)

Time O\( $$n^{2}$$ \)

### 方法三：

也可以直接去优化方法一，因为binary search可以简化找位置的操作，让时间复杂度缩减到 $$O(log(n))$$ , 交换的操作是 $$O(n)$$, 最后总的时间复杂度依然是$$O(log(n)+n)*n=O(n^{2})$$

{% code title="这个有点问题 但我还不知道啥问题= =" %}
```python
def insert_num (array, n):
  idx=len(array)-1
  array.append(n)
  if idx<0:
    return n
  else:
    insert_place=binary_search(array, n) 
    for i in range(insert_place+1, idx):
     array[i+1]=array[i]
    array[insert_place]=n
    return array
    
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
  return left 

array = [5,3,2,1]
new_array=[]
for i in range (len(array)):
  insert_num (new_array, array[i])

print new_array
```
{% endcode %}

