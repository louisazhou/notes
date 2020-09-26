# Advanced Sorting

### Merge Sort 归并排序

假如有一个sort好的array的前后半边，就可以用谁小移谁的方式sort完整个array

{% embed url="https://www.hackerearth.com/zh/practice/algorithms/sorting/merge-sort/visualize/" caption="" %}

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
#Time: O(n)
#对于这个function来说 Space: O(1)
```

```python
# slicing 
def merge_sort(array):
    if len(array)==0 or len(array)==1:
        return array

    mid = len(array)/2
    left = merge_sort(array[:middle])   #O(n) space due to slicing
    right = merge_sort(array[middle:])  #O(n) space due to slicing

    return merge(left, right) #调用一次merge是O(n)Time,在这里调用了logn次

# indexing 这个代码有问题= =
def merge_sort(array, left, right):
    if left==right:
        return array[left]

    mid = (left+right)//2     
    left = merge_sort(array, left, mid)    #O(logn)
    right = merge_sort(array, mid+1, right)  #O(logn)

    return merge(left, right) #调用一次merge是O(n)Time,在这里调用了logn次
```

**时间复杂度**

Upper Half to split: Time O\(n\)=1+2+4+...n/2  
Lower Half to merge: Time O\(nlogn\) 每一层merge的时间复杂度是O\(n\) 一共有h=logn层  
Total Time Complexity O\(nlogn\)

**空间复杂度**

如果用indexing，传index, 那么只需要分析call stack上的额外空间。  
在等待line7结果时，只有最左边的直上直下，这个时候的call stack上是一些mid，总共logn层，所以最左边的那一列的空间复杂都是O\(logn\) 但是这还不是最占空间的时刻；  
当每一层的调用都在line7结束了，在等待line8的right result时，也就是上图中红色的部分已经return好了，在left等待着right有结果来merge，这个时候红色部分+粉色部分的空间复杂是：红色O\(n\)=1+2+4+...n/2，粉色O\(logn\), 总共O\(n\). 直到最后，被merge之前的最后一瞬，都有O\(n\) space。

如果是每一步都new一个新的array，那这其实是overhead，虽然同样是O\(n\) space，但是如果用helper array，可以减少每一次创建和GC的overhead.

#### Followup: Linked List

**时间**

Upper Half to split: Time O\(nlogn\)=n+n+n+...n  
Lower Half to merge: Time O\(nlogn\) 每一层merge的时间复杂度是O\(n\) 一共有h=logn层  
Total Time Complexity O\(nlogn\)

**空间**

每层 O\(1\) 直上直下粉色路径的空间复杂都之和 空间改指针 所以O\(logn\)

#### Convert a String A1B2C3D4 to ABCD1234

只需要调整merge的那一步，让字母排在数字前面

#### Convert a String ABCD1234 to A1B2C3D4，要求in place

切一刀，交换一下位置；再切一刀，交换一下位置

找m, lm, rm

### Quick Sort 快排

选好一个pivot之后就可以把pivot左边的都放比pivot小的，pivot的右边都放比pivot大的；再分别对左右进行重复操作，就可以让整体sort好

{% embed url="https://www.hackerearth.com/zh/practice/algorithms/sorting/quick-sort/visualize/" caption="" %}

#### 一个挡板（赶紧忘了这个！）

store index是一个隔板，左边都是比pivot小的，右边都是比pivot大的。所以一开始store index是从0开始移动

\(..., store\_index）: &lt;pivot

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

{% hint style="info" %}
`randrange (start, stop, step)` doesn’t consider the last item i.e. it is exclusive. For example, `randrange (10,20,1)` will return any random number from 10 to 19 \(exclusive\). it will never select 20.
{% endhint %}

Time: Best Case and On average, both O\(nlogn\)

> 为什么Average是O\(nlogn\)
>
> 下面这个推导的最后结论

$$
\begin{array}{l}{\text { For any pivot position } i ; i \in\{0, \ldots, n-1\}} \\ {\cdot \text { Time for partitioning an array : } c n} \\ {\cdot \text { The head and tail subarrays contain } i \text { and } n-1-i \text { items, }} \\ {\text { respectively: } T(n)=c n+T(i)+T(n-1-i)} \\{\text { Average running time for sorting (a more complex recurrence): }} \\ {\qquad T(n)=\frac{1}{n} \sum_{i=0}^{n-1}(T(i)+T(n-1-i)+c n)} \end{array}
$$

最坏的情况不取决于input本身是否有序，而是取决于每次的pivot都选的特别差，比如每次都选到了最大的或者最小的，导致每一次都是n-1比它小，这就成了一个直上直下的一叉树，每一层都需要做n次交换，高度是n， worst case O\( $$n^{2}$$ \)

Space: O\(logn\) worst case O\(n\) 这次是只取决于高度了，因为每层的call stack上只分配了常数级别的空间（pivot\_index）

```python
class Solution(object):
  def quickSort(self, array):
    """
    input: int[] array
    return: int[]
    """
    # write your solution here
    if not array or len(array)<=1:
      return array

    return self.helper(array, 0, len(array)-1)

  def helper(self, array, start, end):

    from random import randrange #保证稳定的，防止被hacker攻击

    if start>=end:
      return

    pivot_index=randrange(start, end+1)
    new_index=self.partition(array, pivot_index, start, end)
    self.helper(array, start, new_index-1)
    self.helper(array, new_index+1, end)
    return array

  def partition(self, array, index, start, end):
    store_index=start
    array[end],array[index]=array[index],array[end]
    pivot=array[end]

    for i in range (start, end):
      if array[i]<pivot:
        array[i], array[store_index] = array[store_index],array[i]
        store_index+=1
    array[store_index], array[end] = array[end], array[store_index]

    return store_index
```

#### [两个挡板三个区域](https://app.laicode.io/app/problem/10)

```python
class Solution(object):
  def quickSort(self, array):
    """
    input: int[] array
    return: int[]
    """
    # write your solution here
    if not array or len(array)<=1:
      return array
    left, right = 0, len(array)-1
    self.helper(array, left, right)
    return array

  def helper(self, array, left, right):
    if left>=right:
      return

    pivotIdx = self.partition(array, left, right)
    self.helper(array, left, pivotIdx-1)
    self.helper(array, pivotIdx+1, right)


  def partition(self, array, left, right):
    from random import randrange
    pivotIdx=randrange(left, right+1)
    pivot=array[pivotIdx]

    array[pivotIdx], array[right] = array[right], array[pivotIdx]  
    leftbound, rightbound = left, right-1

    while leftbound<=rightbound:
        if array[leftbound]<pivot:
          leftbound+=1
        elif array[rightbound]>=pivot:
          rightbound-=1
        else:
          array[leftbound],array[rightbound]=array[rightbound],array[leftbound]
          leftbound+=1
          rightbound-=1

    array[leftbound], array[right] = array[right], array[leftbound]
    return leftbound
```

#### [Array Shuffling ](https://app.laicode.io/app/problem/258)

2个挡板，3个区域，把非0放在左边，0放在右边

```python
class Solution(object):
  def moveZero(self, array):
    """
    input: int[] array
    return: int[]
    """
    # write your solution here
    if len(array)<=1:
      return array
    end = len(array)-1
    leftBound, rightBound = 0, end
    while leftBound<=rightBound:
      if array[leftBound]!=0:
        leftBound+=1
      elif array[rightBound]==0:
        rightBound-=1
      else:
        array[rightBound], array[leftBound] = array[leftBound],array[rightBound]
        leftBound+=1
        rightBound-=1
    return array
```

#### Rainbow Sort I

3个挡板，4个区域，三种颜色-1，0，1的顺序

-1 -1 -1 -1 0 0 0 0 0 -1 0 1 1 1 1  
i j k

\[0, i\) -1  
\[i, j\) 0  
\[j, k\] unknown  
\(k, n-1\] 1  
array\[j\]==-1: swap with array\[i\], i++, j++ j可以加，因为可以保证i换过来的一定是0  
array\[j\]==0: j++  
array\[j\]==1: swap with array\[k\], k-- j不加，因为k的物理意义是unknown 后面的都还不知道呢 不能加  
当j和k错开的时候停止，不是重叠，因为我们需要unknown里完全没有数字，所以只有在它俩交错才能保证里面没有东西。

Time: O\(n\)

```python
class Solution(object):
  def rainbowSort(self, array):
    """
    input: int[] array
    return: int[]
    """
    # write your solution here
    if not array or len(array)<=1:
      return array

    ones, zeros, negs = len(array)-1, 0, 0
    while zeros<=ones:
      if array[zeros]==-1:
        array[zeros],array[negs]=array[negs],array[zeros]
        zeros+=1
        negs+=1
      elif array[zeros]==0:
        zeros+=1
      else: 
        array[zeros],array[ones]=array[ones],array[zeros]
        ones-=1

    return array
```

### Find the k-th largest element in an array

Soln1: Brute Force: Sort the array in descending order, and return the element at index \(k-1\). O\(nlogn\)

Soln2: Heap

Soln3: QuickSort Average O\(n\) Worst Case $$O(n^2)$$

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

### Rainbow Sort 系列

#### I 3种颜色 -1，0，1

下面这个赶紧忘掉 写的什么玩意

{% tabs %}
{% tab title="Python" %}
```python
class Solution(object):
  def rainbowSort(self, array):
    """
    input: int[] array
    return: int[]
    """
    # write your solution here
    if not array or len(array)<=1:
      return array

    left,index,right=0,0,len(array)-1
    while index<=right:
      if array[index]==-1:
        array[index], array[left]= array[left], array[index]
        index+=1
        left+=1
      elif array[index]==1:
        array[index], array[right] = array[right],array[index]
        right-=1
      else:
        index+=1
    return array
```
{% endtab %}

{% tab title="Java" %}
```text

```
{% endtab %}
{% endtabs %}

#### II 4种颜色 0，1，2，3

只有在大量重复数字的sort才有意义

```python
class Solution(object):
  def rainbowSortII(self, array):
    """
    input: int[] array
    return: int[]
    """
    # write your solution here
    return self.quickSort(array, 0, len(array) - 1)

  def quickSort(self, A, start, end):
      if start >= end:
          return A

      left, right = start, end
      # key point 1: pivot is the value, not the index
      pivot = A[(start + end) // 2];

      # key point 2: every time you compare left & right, it should be
      # left <= right not left < right
      while left <= right:
          while left <= right and A[left] < pivot:
              left += 1

          while left <= right and A[right] > pivot:
              right -= 1

          if left <= right:
              A[left], A[right] = A[right], A[left]

              left += 1
              right -= 1

      self.quickSort(A, start, right)
      self.quickSort(A, left, end)
      return A
```

#### III K种颜色，用quick sort的思想

传入两个区间，一个是颜色区间 color\_from, color\_to。另外一个是待排序的数组区间 index\_from, index\_to.  
找到颜色区间的中点，将数组范围内进行 partition，&lt;= color 的去左边，&gt;color 的去右边。  
然后继续递归。  
时间复杂度 O\(nlogk\)n是数的个数， k 是颜色数目。这是基于比较的算法的最优时间复杂度。

不基于比较的话，可以用计数排序（Counting Sort）

```python
class Solution(object):
  def rainbowSortIII(self, array, k):
    """
    input: int[] array, int k
    return: int[]
    """
    # write your solution here
    if not array or len(array)<=1:
      return array
    return self.sort(array, 0, len(array)-1,1,k)

  def sort(self, array, idx_l, idx_r, color_l, color_r):
    if idx_l==idx_r or color_l==color_r:
      return

    pivot=(color_l+color_r)//2
    left, right = idx_l, idx_r

    while left<=right:
      while left<=right and array[left]<=pivot:
        left+=1
      while left<=right and array[right]>pivot:
        right-=1
      if left<=right:
        array[left],array[right]=array[right],array[left]
        left+=1
        right-=1

    self.sort(array,idx_l,right,color_l,pivot)
    self.sort(array,left,idx_r,pivot+1,color_r)

    return array
```

### 面试题目：

#### 为什么基于比较的排序 时间复杂度下界是Omega\(nlogn\)

Given n numbers, there are n! possible permutations. For any value, x!=y, x&lt;y is half of the permutation. In each comparison, we are left with n!/2 permutation. In the end, we operate log\(n!\) to achieve sorting.

Olog\(n!\)=O\(nlogn\) \(Stirling Formula\)

#### 如果有1Mb的数据，用哪个sorting algorithm? What about 10Mb,100Mb,1Gb,1Tb,1Pb?

1Mb: Memory sorting 随便算法

1Tb: Disk sorting, External Sorting 外排，单机，多机，最后merge，写回磁盘

