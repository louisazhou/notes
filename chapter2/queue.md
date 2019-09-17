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

## 例题：Sliding Window

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

在工作中，data pipeline也会使用sliding window来存值，就像是在做down sampling，就可以存一个统计意义上更小的数据集。



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

