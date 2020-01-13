---
description: 递归和linkedlist
---

# Recursion 1

## Fibonacci 引入

1. 表象上 function calls itself
2. 实质上 boil down a big problem to smaller ones \(size n depends on n-1, n-2 ...\)
3. 实现上  - base case smallest problem to solve - recursive rule 当前层干一点什么事可以解决这个问题

Recursion解决问题的方式是做规约（reduction），把未知的规约到已知的。Recursion其实解决的更小号问题就是它本身。比如，实现一个从1加到n的function

![](../.gitbook/assets/image%20%2834%29.png)

```python
class Solution(object):
  def fibonacci(self, K):
    """
    input: int K
    return: int
    """
    # write your solution here
    if K < 0:
      return 0

    if K == 0 or K == 1:
      return K
    
    return self.fibonacci(K-1)+self.fibonacci(K-2)
```

$$
Time: \mathrm{O}\left(1+2+2^{2} +2^{3} +\ldots .+2^{n-1}\right)=\mathrm{O}\left(2^{n} \right) \\
Space: O(n)
$$

### 常见错误

1. Recursive Function 的时间复杂度等于recursion tree节点的总个数？  ——除非每一个节点的operation是O\(1\)
2. Recursive Function 的时间复杂度等于最后一层节点的个数？ ——如果是一个直上直下一叉树就错得离谱了
3. Recursive Function 的时间复杂度等于最后一层节点的时间复杂度之和？ ——同上
4. Recursive Function 的空间复杂度等于recursion tree的层数或者高度？ ——除非每一层的space是O\(1\)
5. Recursive Function 的空间复杂度等于所有节点的空间复杂度之和？ ——只和直上直下的粉红色那条call stack有关，因为冯诺依曼原理，每一层调用结束之后原来的那个空间就被释放了

**Call stack:** can be regarded as a **globally accessible information** that tells you what happened before each _break point_ in each level. 

## getSum 引入

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

使用一次递归，其实本质上就是在实现一个数学归纳法induction的证明。

## pow\(a, b\)

```python
class Solution(object):
  def power(self, a, b):
    """
    input: int a, int b
    return: long
    """
    # write your solution here
    if b==0:
      return 1
    elif b==1:
      return a

    midres = self.power(a, b//2)

    return midres*midres if b%2==0 else midres*midres*a
```

如果不存下midres，直接return两次，那么就多了很多重复计算，此时的时间复杂度是O\(b\)，空间复杂度是O\(logb\).

## Linked List Recursion

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

1. 翻转后面的那些
2. 让后面的node2指向自己 node2.next=head
3. 让head.next=null
4. 新的linkedlist的头 也就是node n

时间、时间都是O\(n\)

```python
def reverse (head): #reverse the linkedlist headed by head
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

## Iterative vs Recursive Way

