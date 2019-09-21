---
description: 快速从一堆数据中找到极值
---

# Heap

用途：维护一个变化的数据集的最优质的。

逻辑上，堆是一个complete binary tree，即除了最后一层外都是满的，最后一层尽可能往左排。物理上，可以把Heap存在array里，因为它具有complete binary tree的性质。

Left Child Node Index = Parent Node Index \*2 +1

Right Child Node Index = Parent Node Index \*2 +2

Min-Heap: 每个node的val都比自己孩子节点小

Max-Heap：每个node的val都比自己孩子节点大



Heap上也有两个基本操作，和stack一样，push和pop

## Push/Insert

如果push到minheap的值是一个大的数，可以直接append；

如果push到minheap的值是一个比root还要小的数，sift-up operation；

```python
class Heap (object):
    def __init__(self):
        self.array = []

    def sift_up(array, index):
        parent_idx = (index-1)/2
        if parent_idx < 0 or array[index] > array[parent_idx]:
            return
        array[index],array[parent_idx] = array[parent_idx], array[index]
    
        sift_up(array, parent_idx)

    def push(self, val):
        self.array.append(val)
        self.sift_up(self.array, len(self.array)-1)    

arr = [0,1,5,6,8,-1]
sift_up (arr, len(arr)-1)
print(arr)
```

Time Complexity: O \(log n\)

## Pop

Pop后为了维护complete binary tree的性质，把最后一个最大的往上提，然后慢慢Sift-down

```python
def sift_down(array, index):
    left = index*2+1
    right = index*2+2
    small = index
    
    if left<len(array) and array[small]>array[left]:
        small = left
    if right<len(array) and array[small]>array[right]:
        small = right
    if small!=index:
        array[small], array[index] = array[index], array[small]
        
        sift_down(array, small)
        
def pop(self):
    res = self.array[0]
    self.array[0], self.array[-1] = self.array[-1], self.array[0]
    self.array.pop()
    self.sift_down(self.array,0)
    return res
```

Time Complexity: O \(log n\)

## Initialization/Heapify

从Array初始化一个Heap 其实是一个Sift down

```python
def build_heap(arr):
    for i in range(len(arr)/2-1, -1, -1)
        sift_down(arr)
```

Time Complexity: O\(n\) 等比数列求和、求极限推导

## Smallest K Elements

**soln1:** sort   O\(nlogn\)

**soln2:** quick select O\(kn\)

**Soln3:** 维护一个小根堆  Space O\(n\)

Step 1: Heapify all elements    O\(n\)

Step 2: Call pop\(\) k times to get the k smallest elements. O\(klogn\)

Time Complexity Total: O\(n+klogn\)

```python
import heapq

def kSmallest(array, k):
    if not array:
        return []
    res = []
    heapq.heapify(array)
    for i in range(min(k,len(array))):
        res.append(heapq.heappop(array))
    return res
```

**Soln4:** 维护一个大根堆  Space O\(k\)

Step 1: Heapify the first k elements to form a max-heap of size k     O\(k\)

Step 2: Iterate over the remaining n-k elements one by one. 

Compare with the largest element of the previous smallest k candidates. 

case 1: new element&gt;=top: ignore

case 2: new element&lt;top: update \(top -&gt;new element\)              O\(\(n-k\)logk\)

Total O\(k+\(n-k\)logk\)

```python
def kSmallest2(array, k):
    if not array:
        return array
    if k>=len(array):
        return array
    res = [-elem for elem in array[0:k]]
    heapq.heapify(res)
    for i in range(k,len(array)):
        if array[i] < -res[0]
            heapq.heappop(res)
            heapq.heappush(res, -array[i])
    return [-elem for elem in res]
```

|  | O\(n+klogn\) | O\(k+\(n-k\)logk\) |
| :--- | :--- | :--- |
| k&lt;&lt;n | O\(c\*n\) | O\(nlogk\) |
| k~~n | O\(nlogn\) | O\(n\) |

## Top K Frequent Elements Leetcode 347

Solution 1: 最直接的想法，max heap，全部进heap，取前k个。用-frequency的排序+min heap代替max heap的实现。

Solution 2：用k-size min heap，不断push，把最小的弹出去。注意输出时需要reverse一下。 

```python
def topKFrequentPQ(nums, k):
    # 统计频率 O（）
    freq_hash = dict() #0 frequency, 1 number
    for n in numbers:
        if n in freq_hash:
            freq_hash[n]+=1
        else:
            freq_hash[n] =1
    
    # 末尾淘汰 n*2*O(logk)
    heap=[]
    for n, f in freq_hash.items():
        # items()返回的是列表对象，iteritems（）返回的是iterator对象
        heapq.heappush(heap,(f,n))
        if len(heap)>k:
            heapq.heappop(heap)
    
    # 调换顺序 O(klogk)
    while len(heap)!=0:
        ret.append(heapq.heappop(heap)[1])
    ret.reverse()
    
    return ret
```

在这种方法下不需要每次heapify

## Merge K sorted array

Step 1: Create a min heap, put the first element of each array into the heap

Step 2: Each time pop an element from the heap, and then push the next element into the heap. 

```python
def mergek(arrays):
    if not arrays:
        return None
    heap = []
    for i in range (len()):
        if len(arrays[i]):
            heap.append((arrays[i][0], i, 0))
        heapq.heapify(heap)
        result=[]
        
        while heap:
            val, index_array, index_element = heapq.heappop(heap)
            result.append(val)
            if index_element+1<len(arrays[index_array]):
                heapq.heappush(heap, (arrays[index_array][index_element+1], 
                index_array, index_element+1))
                
        return result
```

Time: O\(2K+nlogk\)

k读入、k来heapify、nlogk

## 面试题

Linkedin：Can you say something you know about stack and heap?

数据结构的stack：LIFO，

操作系统的stack： 函数调用stack存reference，dfs

数据结构的heap：priority queue

操作系统的heap：存object，申请内存来用

