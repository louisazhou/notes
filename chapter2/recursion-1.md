---
description: 递归和linkedlist
---

# Recursion 1

## 引入

1. 表象上 function calls itself
2. 实质上 boil down a big problem to smaller ones \(size n depends on n-1, n-2 ...\)
3. 实现上  - base case smallest problem to solve - recursive rule 当前层干一点什么事可以解决这个问题

Recursion解决问题的方式是做规约（reduction），把未知的规约到已知的。Recursion其实解决的更小号问题就是它本身。比如，实现一个从1加到n的function

![](../.gitbook/assets/image%20%2828%29.png)



### iteratively

```python
def getsum(n):
    acc=0
    for num in xrange (1,n+1):
        acc+=num
    return acc
```

### recursively

$$\begin{array}{l}{f(n)=f(n-1)+n} \\   {\begin{aligned} f(4) &=f(3)+4 \\ &=(f(2)+3)+4 \\ &=((f(1)+2)+3)+4  \end{aligned}}\end{array}$$ 

```python
def getsum(n):
    if n==1:
        return 1
    return n+getsum(n-1)
```

递归的调用带来一个新的execution environment（运行式环境）。



使用一次递归，其实本质上就是在实现一个数学归纳法的证明。在数学归纳法（induction）里，我们有

1. Induction Base Verification \(Base Case\) P\(0\) and P\(1\)
2. Recursion Rule: Assuming k&lt;n, P\(k\) is true. Generally, k=n-1. Focus on proving the correctness of P\(n-1\)--&gt;P\(n\) 

### Print a singly linked list recursively 

打印一个长度为n的链表，等价于以head为头和长度为n-1的链表

```python
def PrintSinglyLinkedList(head):
    if head is None:
        return
        
    print head.value
    
    PrintSinglyLinkedList(head.next)
```

### Search by Value

```python
def search_by_value (head, value):
    if head is None:
        return None
    if head.value==value:
        return head
    return search_by_value (head.next, value)
```

### Search by Index 

### Search by Value

### Add to Index

### Remove from Index to Recursion 

### Reverse a singly linked list

```python
def reverse (head):
    if head is None or head.next is None:
    # linked list is empty or contains only one node
        return head
    
    node = reverse(head.next)
    # 当前head的头节点就是所反转list的尾节点 在执行完head.next这一行之后，head.next的值还是原来的
    tail = head.next
    tal.next = head
    head.next = None 
    
    return node
```

