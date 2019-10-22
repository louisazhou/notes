---
description: 队列，先进先出； deque，双向队列
---

# Queue

队列是线性结构，First in First out policy. 

enque 

deque

## Interface

backed by list

```python
class Queue(object):
    def __init__(self):
        self._items = []
        
    def __len__(self):  #O(1)
        return len(self._items)
        
    def enqueue(self, item):  #O(1)
        self._items.append(item)

    def dequeue(self):   #O(n)
        if self.empty():
            return None
        item = self._item[0] 
        self._items.pop(0)  #等价于del self._items[0] 注意pop如果不指定是弹出最后一个
        return item
        
    def empty(self):
        return len(self._items)==0
        
    def front(self):
        if self.empty():
            return None
        return self._item[0]    
```

backed by singly linkedlist

```python
class _ListNode(object):
    def __init__(self, value):
        self.next = None
        self.value = value
class Queue(object):
    def __init__(self):
        self.size = 0
        self._head, self._tail = None, None
    def __len__(self):
        return self._size
    def empty(self):
        return self._size == 0
    def enqueue(self, value):
        if not self.empty():
            self._tail.next = ListNode(value)
            self._tail = self._tail.next
        else:
            self._head = self._tail = ListNode(value)
        self._size += 1
    def dequeue(self):
        if self.empty():
            return None
        value = self._head.value
        self._head = self._head.next
        if not self._head:
            self._tail = None
        self._size -= 1
        return value
    def front(self):
        if self.empty():
            return None
        return self._head.value
```

 

## Double Ended Queue: deque

两端都可以放拿元素，双端队列。如果把它当queue来用，只关注几个函数

append\(\)

appendleft\(\)

pop\(\)

popleft\(\)

右边append, 左边pop；左边append，右边pop

```python
from collections import deque
d = deque()
d.append(2)
d.append(3)
d.appendleft(1)
print d # 123
print 1==d.popleft() #True
print 3==d.pop() #True
```

## 经典问题 Sliding Window

一般可以用1 array或者2pointers 特点是随着窗口的增加，窗口内元素使得窗口的某种性质单调变化，eg. 长度、非负数组的元素总和。

如果不满足单调性，比如 有正有负的array，需要用其他方法，比如prefix sum。

类似的题目分成以下几种：

* 满足某一要求的max window size
* 满足某一要求的min window size
* 满足某一要求的固定size的window有多少个
* 一个window，使其内值得和/积 最大/最小/小于k/大于k
* at most, at least, consecutive, longest subarray等关键词转化成以上某种

### Average

给一个window size，一串数，算出sliding window的平均值

```python
from collections import deque
class MovingAverage(object):
    def __init__(self, size):
        self._nums = deque()
        self._max_size = size
        self._sum = 0
        
    def next(self, value):
        self._nums.append(value)
        self._sum+=value
        if len(self._nums)>self._max_size:
            self._sum-=self._nums.popleft()
        return self._sum*1./len(self._nums)      #O(1)
```

### **Minimum Size Subarray Sum**

Given an array of n positive integers and a positive integer s, find the minimal length of a contiguous subarray of which the sum ≥ s. If there isn't one, return 0 instead.

Example: 

Input: s = 7, nums = \[2,3,1,2,4,3\]

Output: 2

Explanation: the subarray \[4,3\] has the minimal length under the problem constraint.

```python
def minSubArrayLen(self, s, nums):
    min_len = sys.maxint
    Lst = [sys.max] * len(nums)
    cur_sum = 0
    left = 0
    
    for i in xrange(0, len(nums)):
        cur_sum += nums[i]  #[i, j] inclusive
        if cur_sum < s:
            continue
            
        while left < len(nums) and cur_sum >= s:
            min_len = min(min_len, i - left + 1)
            Lst[i] = min_len
            cur_sum -= nums[left]
            left += 1
     

    if min_len == sys.maxint:
        return 0
    else:
        return min_len
```

### Max

给一个window size，一串字母，算出无重复字母sliding window的最大长度

思路：LR指针，以每个元素结尾的substring的最长长度存成一个list，输出这个list的最大值。

已经存在的字母可以存成一个hash（dictionary），只要频率大于1就左移左指针，否则左指针不动，右指针右移，list记录的数字是r-l+1

```python
def lengthOfLongestSubstring(self, s):
    hashS=dict()
    l=0
    result_list=[]
    for r in rnage(0, len(s)):
        if s[r] not in hashS:
            hashS[s[r]]=0
        hashS[s[r]]+=1
        
        while l<len(s) and hashS[s[r]]>1:
            hashS[s[r]]-=1
            l+=1
        result_list.append(r-l+1)
        
    return max(result_list)
```

优化：如果只需要求一个max，那就用一个maxL变量维护最大值就行，不用维护整个数组。

### Max Consecutive Ones

给一串1、0，如果最多只能翻一次0变1，求最长的数列长度。

```python
def Solution():
    if len(nums)==0:
        return 0
    l, r, k = 0,0,1
    zero = 0
    ret = 1
    
    for i in xrange(0, len(nums)):
        if nums[r]==0:
            zero+=1
        
        while zero > k:
            if nums[l]== 0
                zero -= 1
            l += 1
        ret = max(ret, r-l+1)
    return ret
```

### **Find All Anagrams in a String**

Given a string s and a non-empty string p, find all the start indices of p's anagrams in s.

Strings consists of lowercase English letters only and the length of both strings s and p will not be larger than 20, 100.

The order of output does not matter.

Example 1:

Input:

s: "cbaebabacd" p: "abc"  


Output:

\[0, 6\]  


```python
class Solution(object):
    def findAnagrams(self, s, p):
        if len(s) < len(p):
            return []
        
        if len(p) == 0:
            return []
        
        hashP = dict()
        
        for c in p:
            if c not in hashP:
                hashP[c] = 0
            hashP[c] += 1 

        j = 0
        hashS = dict()
        cnt = 0
        ret = []
        for i in range(len(s)):
            if s[i] not in hashS:
                hashS[s[i]] = 0
            hashS[s[i]] += 1
                
            if s[i] in hashP and hashS[s[i]] == hashP[s[i]]:  #这里没有判断if in hash，发生了key error
                cnt += 1
                
            if i - j + 1 == len(p):
                if cnt == len(hashP):
                    ret.append(j)
                    
                hashS[s[j]] -= 1
                if s[j] in hashP and hashS[s[j]] == hashP[s[j]] - 1:  #bug 不能用 hashS[s[j]] < hashP[s[j]], 必须在临界点上
                    cnt -= 1
                j += 1
            
        return ret
```

### **Subarray Product Less Than K**

Your are given an array of positive integers nums.

Count and print the number of \(contiguous\) subarrays where the product of all the elements in the subarray is less than k.

Example 1:

Input: nums = \[10, 5, 2, 6\], k = 100

Output: 8

Explanation: 

The 8 subarrays that have product less than 100 are: \[10\], \[5\], \[2\], \[6\], \[10, 5\], \[5, 2\], \[2, 6\], \[5, 2, 6\]. Note that \[10, 5, 2\] is not included as the product of 100 is not strictly less than k.

```python
class Solution(object):
    def numSubarrayProductLessThanK(self, nums, k):
        if k <= 1:
            return 0
        left = 0
        product = 1
        ret = 0
        for i in xrange(len(nums)):
            product = product * nums[i]
            while product >= k:
                product = product / nums[left]
                left += 1
                
            ret += (i - left + 1)
            
        return ret 
```

### **Subarray Sum Equals K \(all positives\)**

```python
 def subarraySumALLPOS(self, nums, k):

        left = 0
        ret = 0
        sum = 0

        for i in xrange(len(nums)):
            sum += nums[i]
        	while sum >= k and left < len(nums):
            left += 1
            sum -= nums[left]
            if sum == k:
                ret += 1
      return ret
```

### **Subarray Sum Equals K \(general\)**

Given an array of integers and an integer k, you need to find the total number of continuous subarrays whose sum equals to k.

Example 1:

Input: ****nums = \[1,1,1\], k = 2

Output: 2

The range of numbers in the array is \[-1000, 1000\] and the range of the integer k is \[-1e7, 1e7\].

```python
def subarraySum(self, nums, k):
        prefix_hash = dict() #key is sum, value is cnt
        prefix_hash[0] = 1
        prefix_sum = 0
        
        ret = 0
        for n in nums:
            prefix_sum += n
            if prefix_sum - k in prefix_hash:
                ret += prefix_hash[prefix_sum - k]
                
            if prefix_sum not in prefix_hash:
                prefix_hash[prefix_sum] = 0
            prefix_hash[prefix_sum] += 1
            
        return ret
```

## Tree Level Order Traversal \(BFS\)

## Implement a queue with Max API 

```python
class QueueMax:
    def __init__(self):
        self.entries = []
        self.max_val = []
    
    def enqueue(self, x):
        self.entries.append(x)
        while self.max_val:
            if self.max_vals[-1] >=x:
                break
            self.max_vals.pop()
        self.max_vals.append(x)
        
    def dequeue(self):
        if self.entries:
            res = self.entries.pop(0)
            if res == self.max_val[0]:
                self.max_vals.pop(0)
            return res
        raise Exception("Empty Queue")
        
    def max(self):
        if self.max_vals:
            return self.max_vals[0]
        raise Exception("Empty Queue")
```

## 应用

1. 在工作中，data pipeline也会使用sliding window来存值，就像是在做down sampling，就可以存一个统计意义上更小的数据集。
2. 
![](../.gitbook/assets/image%20%289%29.png)



