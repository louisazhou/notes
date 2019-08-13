---
description: 递归
---

# Recursion

## 引入

Recursion解决问题的方式是做规约（reduction），把未知的规约到已知的。Recursion其实解决的更小号问题就是它本身。比如，实现一个从1加到n的function

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

## Print a singly linked list recursively 

打印一个长度为n的链表，等价于以head为头和长度为n-1的链表

```python
def PrintSinglyLinkedList(head):
    if head is None:
        return
        
    print head.value
    
    PrintSinglyLinkedList(head.next)
```



