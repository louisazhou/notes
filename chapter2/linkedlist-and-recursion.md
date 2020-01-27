---
description: 单链表和各种操作
---

# Linked List

Python List，存储是连续的\(store all data sequentially in memory\). 所以如果想要在list中插入元素，需要为原有的list分配新的空间，把原来的list和想插入的元素放在足够大的新的位置，而不是假设原数组的下一个位置没有元素直接去占领。

如何插入数据但不让原来的数据移动？

## Linked List

linkedlist虽然也是线性结构，但它对内存单元的要求没有list这样严格，所以它在内存中的存储可以是不连续的。通过对ref的连接，就可以把元素串在一起。从逻辑上看，它依然是一个线性结构，但是在内存上不要求一个挨着一个。每一个object都有对另一个object的引用，可以很方便找到下一个object在哪里。

链表Linkedlist由节点组成，每个节点包含一个data和一个表示结构的存储（reference），reference存储的是下一个object的地址。如果知道链表中的第一个节点，就可以通过不断访问它的next来遍历它的结构。

### OOP——object oriented programming 

Python处理的基本单元是对象（object）Anything can be an object. 

#### Object:

1. States ，每个对象有特有的状态（eg. 特征，属性）。比如汽车的颜色、轮子、品牌、大小、重量、型号、排量... 都描述了汽车的特点。更抽象的，速度是汽车的物理状态。
2. Behavior, 对象的行为，描述了能对对象做什么事，有什么样的互动。比如汽车，能跑，能加速或减速、前进或后退。

以上就是面向对象最核心的概念。

#### Class: 

无论马自达还是兰博基尼，有唯一的VIN-number.定义class就描述了一个对象应该具有的状态和行为是什么。class是对于对象的描述，一个class是一个**blueprint**，描述了一系列我们想要的object具备的特点是什么。  


{% hint style="info" %}
LinkedList的两个坑

1. NPE
2. 对头的控制（不一定是那个唯一的头 可能是中间的突然断掉了）
{% endhint %}

### Singly Linked List

一个单链表由0个或多个链节点\(a list node\)组成。从OOD的角度，如何构造一个list node？

#### 构造List Node

States: data + reference

```python
class ListNode(object):
    def __init__(self, value): #双下划线，特殊函数 名字是固定的，也是自己写class时最重要的函数。它的功能本质上是Initialize the object that you create
        self.next = None #初始化时更make sense的是直接None它 因为只有一个object的时候没有指向 但也可以self.next=next, 在一开始传两个参数
        self.value = value
        
node1 = ListNode("H") #Python和其他的不同，self不是用户自己传的，是python传的，所以用户传的是value。
node2 = ListNode("E")

node1.next = node2
```

self 是一个满足class ListNode的具体object，所以是对self进行初始化。在初始化时，为了满足对于state的要求，data和ref，就有了self.next 和 self.value. 分别对应reference，data。

当运行到line6时，

第一步：Python Environment创建了一个ListNode这样class的一个object

第二步：Python调用了\_\_init\_\_这个初始化函数。

创建object和写一个class之间是独立的，只有在line6和7运行时才会创建一个object。list node object的value指向一个字符串，而reference什么都不指向。

line9，从物理含义上，让node1里的next存node2的地址。所以node1走到next就能找到node2。这就让两个list node object产生了联系。

对于一个单链表，最基本的操作就是对这个list进行遍历\(traverse\)。

在这里最容易犯的错误是dereference的时候在None上操作了 比如

![](../.gitbook/assets/image%20%2850%29.png)

这里temp.next=temp.next.next没问题，但是current.next=current.next.next就会报错

### Traverse 遍历

遍历一个单链表，将它的值打出来。head是最重要的，head指的是一个单链表中的头节点。如果真要手写，那么我们会写的可能是 从第一个print head.value写到最后一个print head.next.next.next.value

或者，用循环iteration或递归recursion来遍历。

{% tabs %}
{% tab title="iteration" %}
```python
def traverse(head):
    while head is not None:     #while head 等价
        print head.value
        head = head.next
```
{% endtab %}

{% tab title="recurssion" %}
```python
def print_all_nodes(head):
    if not head:
        return 
    print head.val
    print_all_nodes(head.next)
    
    # or alternatively
    
    if head:
        print(head.val)
        print(print_all_nodes(head.next))
    return
```
{% endtab %}
{% endtabs %}

注意head是一个reference，并不是一个value。就像是身份证可以代表人，但是身份证和本人是两个完全不同的物理存在。所以line4的含义是reference的箭头。

遍历是其他所有操作的基础，帮助我们从头节点走到最后一个节点。

### Search 搜索

#### search by index

假设第一个list node的index是0，给一个单链表，找对应index的节点，输出。如果找不到，return None。

所以这个问题的核心还是遍历traverse。

> 一个好的code应该有对于function的specification和一个return value的expectation。所以这些部分应该作为注释先写出来，也不容易忘记这个function是为了什么。
>
> 我们很多时候要做的第一件事是先check输入和expectation是否符合，也就是check validity。对于我们的问题来说，可能是空链表（head is None\)或者index&lt;0。

```python
def search_by_index(head):
    if head is None or index < 0:
        return None
```

接下来，错误的做法是，我们不能直接定义一个jump\_times去跳index次，因为很可能就越界了。但是，如果我们跳完之后发现自己已经到了None，就可以直接返回None. 所以我们只需要在原来遍历的基础上加一个条件

```python
for jump_times in xrange(index):
    head=head.next
    if head is None:
        return None
return head
```

或者用个while loop

```python
while (index>0 and head!= None):
    head=head.next
    index--
# index<=0 or head==None
return head
```

#### search by value

每个链节点都有一个value，所以，给定一个value，看它是否在链表里，如果在，返回它的index，如果不在，返回None。

```python
def search_by_value (head,value):
    if not head:
        return None
    while head is not None:
        if head.value==value:
            return head
        head = head.next
    return None
```

> 相等关系的判断：
>
> 1. == means equality test
> 2. is means identity test 
>
> 比如 a, b = \[1\], \[1\] 
>
> print a==b 打印True 内容相同，都是1
>
> print a is b 打印False 这两个是不是指向同一个object
>
> a=\[1\]
>
> b=a 让b也指向a所指向的object
>
> print a is b 输出的是True
>
> a = None 
>
> print a is None 输出是True，因为None是一个有且只有一个的object 所以再定义一个b=None 问print a is b 依然会输出true
>
> a=ListNode\(1\)
>
> b=ListNode\(1\)
>
> print a==b 输出是False！！！
>
> 这要非常注意，因为在Python中，如果在class中没有做比较关系的定义\(\_\_eq\_\_\)，那么在equality test（也就是==）中，这就完全等于identity test \(is\)。

```python
class listnode(object):
    def __init__(self, value):
        self.value=value
        self.next=None
    def __eq__(self, other):
        return isinstance(other, ListNode) and self.value == other.value
```

其中，\_\_eq\_\_是python中在实现a==b. 

isinstance\(\)也是必须的，因为我们需要保证两个被比较的object有相同的类型含义。

比如如果我们定义了另一个listnode object，

```python
class AnotherObject(object):
    def __init__(self, value):
        self.value = value
a=ListNode(1)
b=AnotherObject(1)
```

此时就不对了。

### Add 插入

#### Add to index

{% code title="方法一：头节点单独处理" %}
```python
def add_to_index(head, index, value):
    #head: type node, the first node of the passed singly linked list
    #index: type int, the position where you want to insert (starting from 0)
    #value: type *, the value that will be referred by the newly added list node object if our operation is successful
    #return: 必须返回一个头节点（修改后的链表的头节点），不然找不见list，也不知道这个function是否成功了
    
    if index == 0:
        new_head = ListNode(value)
        new_head.next = head
        return new_head
    else:
        prevNode = search_by_index(head, index-1)
        if prevNode is None:
            return head
        new_node = ListNode(value)
        new_node.next = prevNode.next
        prevNode.next = new_node
        return head
```
{% endcode %}

为了add a new node，其实不能直接走到index，而是要走到index-1的位置，因为单链表只能往后走，不能往前走。但是很棘手的是，头节点没有index-1. 所以定义一个new\_head，这也是如果我们改动了头节点然后返回的。

注意15～17行的顺序: 先让new\_node指向真正的下一个，再让prev指向new\_node。如果把16和17调换，那就不对了，因为new\_node就变成了指向自己，后面的没了。有了环形结构，这就不是单链表了。

{% code title="方法二：加一个sentinel，避免分条件讨论" %}
```python
def add_to_index(head, index, val): 
    fake_head = ListNode("whatever you want")
    fake_head.next = head
    insert_place = searchbyindex(fake_head, index)
    if insert_place is None: #在一个不存在的index或者越界的位置插入新的node
        return fake_head.next #此时返回的依然是原来的linkedlist
    new_node = ListNode(val)
    new_node.next = insert_place.next
    insert_place.next = new_node
    return fake_head.next
```
{% endcode %}

注意的是，加了sentinel（fake\_head）之后，如果想在原来的index**之前插入**一个元素，在加入了fake head后，就等价于在以fake head为首的新链表的index**之后**做插入。

在return时return的都是fake\_head.next. 因为引入fake\_head的原因是避免对head单独讨论。但是！fake\_head并不是真正的头节点，所以真的头节点永远都是fake\_head后面的1个。

如果是在原来链表的头节点插入，那么fake\_head之后的就是新的头节点。

dummy node 在两种情况非常好用1）在构建一个新的链表不知道谁是头的时候（比如谁小移谁，合并了两个linkedlist）2）在需要对头进行操作的时候（增删改）

### Remove 删除

#### Remove from index

```python
def remove_from_index (head, index):
    # validity check is implicit, in search_by_index function
    # find the node with a reference of index
    # return the fake_head
    fake_head = ListNode(None)
    fake_head.next = head
    prev_node = search_by_index(fake_head, index)
    
    if prev_node is None or prev_node.next is None:
    # it is likely that we find the prev of the node, but not the node itself
        return fake_head.next
    
    remove_node = prev_node.next
    prev_node.next = remove_node.next
    remove_node.next = None #or don't write anything
    
    return fake_head.next
```

```python
class ListNode(object):
    def __init__(self, value):
        self.value=value
        self.next=None

        
class SinglyLinkedList(object):
    def traverse(self, head):
        while not head:
            print (head.value)
            head=head.next
    
    def searchByIndex(self, head, index):
        #sanity check
        if index<0 or not head:
            return None
        for jump_steps in range(index):
            head=head.next
            if head is None:
                return None
        return head
    
    def searchByValue(self, head, value):
        if not head:
            return None
        while head is not None:
            if head.value==value:
                return head
            head=head.next
        return None
    
    def addByIndex(self, head, index, value):
        newnode=ListNode(value)
        fakehead=ListNode(None)
        fakehead.next=head
        insertplace=self.searchByIndex(fakehead, index)
        if insertplace is not None:
            newnode.next=insertplace.next
            insertplace.next=newnode
        return fakehead.next
    
    def deleteNode(self, head, index):
        fakehead=ListNode(None)
        fakehead.next=head
        pred_node=self.searchByIndex(fakehead, index)
        if pred_node is not None and pred_node.next is not None:
            pred_node.next=pred_node.next.next
            pred_node.next.next = None
        return fakehead.next
```

{% code title="Count number of nodes" %}
```python
class Solution(object):
  def numberOfNodes(self, head):
    """
    input: ListNode head
    return: int
    """
    # write your solution here
    steps = 0 
    while head:
      steps+=1
      head=head.next
      
    return steps
```
{% endcode %}

## How to design a linked list class

```python
class _ListNode(object):
    def __init__(self, val):
        self.value = val
        self.next = None
        
        
class MyLinkedList(object):
    def __init__(self):
    #一般不是从状态开始想起，而是想，它能实现什么样的功能，再在class中实现这些功能 这叫实现 是实现的一部分 我们一般不希望用户看到实现的一部分，而只是希望用户看到接口是什么
    # we should have a reference that ALWAYS points to the first node of the internally maintained singly linked list
        self._head = None #逻辑上的实现，这里None说得过去（区分下面的）永远maitain一个指向单链表的头节点
        self._tail = None #永远指向单链表最后一个元素    
        self._size = 0 #永远表示 # of nodes in the list
    
    def _get(self,index): 
    # return the node we found, 区分于下面的这个
    # assume index is valid.
        node = self._head #不然就会破坏上面的ALWAYS
        for _ in xrange(index):
            node = node.next
        return node
    
    def get(self, index): #instance method or object method 调用这些方法一定要通过对象来调用 free function就不用self（写在class外面的不用）
    #返回第index个node的node value
    # How do we know the index is out of range?
        if index < 0 or index >=self._size:
            return -1
        return self._get(index).value
    
    
    def addAtHead(self, index, val):
        #如果一个链表只有一个节点，它既是头节点，又是尾节点
        if size._size ==0:
            self._head = self._tail =_ListNode(val)
            self._size += 1
        else:
            new_head = _ListNode(val)
            new_head.next = self._head #新的头节点的后面应该是原来的头节点
            self._head = new_head
            self._size += 1
        
    
    def addAtTail(self, val):
        # 需要先判断现在的链表是不是空的，不然默认tail是None 就没办法.next了
        if size._size ==0:
            self._head = self._tail =_ListNode(val)
            self._size += 1
        else:
            self._tail.next = _ListNode(val)
            self._tail = self._tail.next
            self._size += 1
        
    def addAtIndex(self, index, val):
        
    
    def deleteAtIndex(self, index):
        if index < 0 or index>= self._size:
            return
        if index==0:
            self._head = self._head.next
            self._size -= 1
            # 如果remove掉的就是原来链表的最后一个 
            if self._size == 0:
                self.tail = None
            else:
                node = self._get(index-1)
                node.next=node.next.next
                # 如果remove掉的就是原来链表的tail
                if index=self._size -1:
                    self._tail=node
                self._size -=1
            
                
                  
ll = MyLinkedList() # We should have an empty linked list after initialization (not None) 容积在，只是容器是空的
```

ll = MyLinkedList\(\) 这个clause结束后调用init 初始化函数，创建object

ll.addAtHead\(1\) 等价于MyLinkedList.addAtHead\(ll, 3\)

关于前面的\_ 在Python中，如果一个entity（variable or function）是我们自己实现的一部分，那应该自己来命名自己的entity，比如\_get。用户不应该直接去改变这个。另外，前面maintain了几个状态，我们在做interface的时候也需要去考虑每个状态



## 双链表



## 总结对比 Linked List vs. List

![](https://cdn.mathpix.com/snip/images/nVjhoatUKUkljHRr-mYgMKpttW3daBY9MVm-D_NOiVQ.original.fullsize.png)

什么时候用list？

想要O\(1\)获取value

什么时候linkedlist

想要O\(1\)remove，delete，add

## 题

### Insert in a Sorted Linkedlist

1. Assumption  - 是不是一定有比target大的数 ： 不知道 - Duplication 的时候还要不要加 ： 可以有 插一个一样的在它之前 - Data Type 万一是字符串、double...： 都是整数 - Sorted是ascending还是descending 比如没有给例子 ： ascending
2. Data Structure:  - dummyHead 因为新头旧头可能不是一个头 - curr 判断target是否是要插在curr的后面 也就是和curr.next比较
3. Initialize - curr=dummyHead - targetNode= new ListNode\(target\)
4. For each step: case 1: 先保证 case 2:
5. Termination Condition: curr.next==null

```python
class Solution(object):
  def insert(self, head, value):
    """
    input: ListNode head, int value
    return: ListNode
    """
    # write your solution here
    
    dummyHead=ListNode('dummy')
    dummyHead.next = head
    prev=dummyHead

    while prev.next and prev.next.val < value:
      prev=prev.next

    newnode = ListNode(value)  
    newnode.next = prev.next
    prev.next = newnode

    return dummyHead.next
```

在Java中，logical operator有short circuit 而&&和\|\|是这种逻辑运算符；相反，&和\|是bitwise operation，没有short circuit。在Python中，and和or是logical operation。位运算是为了数本身，是数学上的操作。

在或操作上前一个是false或者且操作上前一个是true才会短路。

### Reverse a Singly Linked List

#### 如果iterative way

面试过程clarification, algorithm, result, test的顺序一步步来； 

1. One sentence high-level description of the problem  Do a linear scan on each element, move next pointer to the previous element
2. Clarification of the problem assumptions
3. Data Structure \(物理意义 or semantic\) 不变的关系 curr: the node which I want to change its next to its previous node prev: curr's previous node in the input linkedlist next: curr's next node in the input linkedlist
4. Algorithm - **initialize**  prev: null, curr=head, next=curr.next - **for each step:**   curr.next=prev, 先移previous，prev=curr 再移curr，curr=next （这一步也可以放在第一步）最后移next，next=curr.next - **termination condition: curr是要被反转的，所以一定是** curr==null 时间O\(n\) 空间O\(1\)

{% hint style="info" %}
注意不管是Python的None还是Java的null，在这里都不是新建一个listnode\(None\) 或者listnode\(null\)，而是就是None/null，也就是没有ListNode，什么都没有。
{% endhint %}

{% tabs %}
{% tab title="Python" %}
```python
class Solution(object):
  def reverse(self, head):
    """
    input: ListNode head
    return: ListNode
    """
    # write your solution here
    prev, curr = None, head

    while curr:
      next=curr.next ##preserve the next pointer since we are changing it afterwards
      curr.next=prev
      prev=curr
      curr=next
    
    return prev
```
{% endtab %}

{% tab title="Java" %}
```java
public class Solution {
  public ListNode reverse(ListNode head) {
    // Write your solution here
    ListNode prev = null;
    while (head!=null) {
      ListNode next=head.next;
      head.next=prev;
      prev=head;
      head=next;
    }
  return prev;
  }
}
```
{% endtab %}
{% endtabs %}

#### 如果recursively

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


class Solution(object):
  def reverse(self, head):
    """
    input: ListNode head
    return: ListNode
    """
    # write your solution here
    if not head or not head.next:
      return head
    
    newhead = self.reverse(head.next)
    head.next.next=head
    head.next=None

    return newhead
```

### Reverse Linked List in Pairs

Reverse pairs of elements in a singly-linked list.

**Examples**

* L = null, after reverse is null
* L = 1 -&gt; null, after reverse is 1 -&gt; null
* L = 1 -&gt; 2 -&gt; null, after reverse is 2 -&gt; 1 -&gt; null
* L = 1 -&gt; 2 -&gt; 3 -&gt; null, after reverse is 2 -&gt; 1 -&gt; 3 -&gt; null

{% hint style="info" %}
只需要遵循和reverse linked list一样的逻辑，知道：  
1. 黑框框在了哪里：两个node之后的那些全部都是黑框  
2. 红色那件事：让N1指向后面的黑框  
3. 蓝色那件事：让N2指向N1  
4. 当前层做点什么事: 红色和蓝色两件事
{% endhint %}

```python
class Solution(object):
  def reverseInPairs(self, head):
    """
    input: ListNode head
    return: ListNode
    """
    # write your solution here
    if not head or not head.next:
      return head
    
    node1, node2 = head, head.next

    rest=self.reverseInPairs(node2.next)
    node1.next=rest
    node2.next=node1
    
    return node2
```

```python
class Solution(object):
  def reverseInPairs(self, head):
    """
    input: ListNode head
    return: ListNode
    """
    # write your solution here
    return self.reverse(head)
    
  def reverse(self, head):  
    if not head or not head.next:
      return head
    
    secondhead=self.reverse(head.next.next)
    newhead=head.next
    newhead.next=head
    head.next=secondhead
    return newhead
```

 

### Remove all vowels in a linked list

curr:物理意义是什么？  
不是让curr和target比较，而是curr.next和target作比较，这样就不需要再来一个prev了 

Termination Condition

{% code title="remove a value from a linked list" %}
```python
class Solution(object):
  def removeElements(self, head, val):
    """
    input: ListNode head, int val
    return: ListNode
    """
    # write your solution here
    dummyHead = ListNode('dummy')
    dummyHead.next = head
    prevNode = dummyHead

    while prevNode.next:
      if prevNode.next.val==val:
        prevNode.next=prevNode.next.next #注意这里不要移动prevNode！
      else:
        prevNode=prevNode.next

    return dummyHead.next
```
{% endcode %}

{% tabs %}
{% tab title="双指针" %}
```python
def remove(head):
    fake_head = ListNode(None)
    fake_head.next = head
    prev = fake_head
    curr = head
    vowel = set(['a', 'e', 'i', 'o', 'u'])
    
    while curr:
        if curr.val in vowel:
            prev.next = curr.next
        else:
            prev = curr
        curr = curr.next
     return fake_head.next
```
{% endtab %}

{% tab title="单指针" %}
```python
def remove(head):
    fake_head = ListNode(None)
    fake_head.next = head
    curr = fake_head
    vowel = set(['a', 'e', 'i', 'o', 'u'])
    
    while curr and curr.next: #判断是否为None
        if curr.next.val in vowel:
            curr.next = curr.next.next
        else:
            curr = curr.next
    return fake_head.next
```
{% endtab %}
{% endtabs %}

### Remove Nth Node From End of List

设立两个指针p1和p2，一开始它们都指向队首的dummy，然后单独令p2向后跳n个元素，这样p1和p2之间就相隔了n个元素，如果这个时候p2发现自己不能完整跳完n步，说明给的数字太大了，越界，所以直接return。  
令p1和p2同时向后不断跳，直到p2已经到了最后一个node，由于此时p1和p2之间相隔n个元素，所以p1的下一个元素就是我们要删除的元素了。

为了方便删除第一个元素，建议设立一个空的“头结点”。

```python
class Solution(object):
  def removeNthFromEnd(self, head, n):
    """
    input: ListNode head, int n
    return: ListNode
    """
    # write your solution here
    dummyHead=ListNode('dummy')
    dummyHead.next=head
    curr,fast=dummyHead,dummyHead

    while n>0 and fast:
      fast=fast.next
      n-=1
    
    if not fast:
      return dummyHead.next

    while fast and fast.next:
      fast=fast.next
      curr=curr.next

    curr.next=curr.next.next
    return dummyHead.next
```

### Partition a Linked List

Given a linked list and a target value T, partition it such that all nodes less than T are listed before the nodes larger than or equal to target value T. The original relative order of the nodes in each of the two partitions should be preserved.

**Examples**

* L = 2 -&gt; 4 -&gt; 3 -&gt; 5 -&gt; 1 -&gt; null, T = 3, is partitioned to 2 -&gt; 1 -&gt; 4 -&gt; 3 -&gt; 5 -&gt; null

1. Assumption: - &lt; 和 &gt;= - Duplication - Integer
2. Data Structure - dummyHeadSmall smallTail - dummyHeadLarge LargeTail
3. 最后一定记得把largetail的尾巴断了

```python
class Solution(object):
  def partition(self, head, target):
    """
    input: ListNode head, int target
    return: ListNode
    """
    # write your solution here
    dummyHeads=ListNode('Small')
    tails = dummyHeads
    dummyHeadl=ListNode('Large')
    taill = dummyHeadl
    curr=head

    while head:
      if head.val<target:
        tails.next=head
        tails=tails.next
      else:
        taill.next=head
        taill=taill.next
      head = head.next
    
    tails.next=dummyHeadl.next
    taill.next=None #最后一定记得把largetail的尾巴断了 不然就是一个loop了

    return dummyHeads.next
```

### Add two linked list which represents large number 

大数：传统语言int long的范围是 $$2^{32}$$ 范围之外称为大数

在Python里自动支持大数，转化好了

1. 如何靠右对齐，个位对齐？ Reverse
2. 进位 carry 
3. 两个linkedlist不等长 所以注意while循环的break，post-processing
4. reverse最后的结果

{% code title="这个题目是已经reverse过了的 所以不需要reverse function" %}
```python
class Solution(object):
  def addTwoNumbers(self, l1, l2):
    """
    input: ListNode l1, ListNode l2
    return: ListNode
    """
    # write your solution here
    dummyHead = ListNode('dummy')
    tail=dummyHead
    curr = 0
    
    while l1 or l2 or curr!=0:
      if l1:
        curr += l1.val
        l1=l1.next
      if l2:
        curr += l2.val
        l2=l2.next
  
      tail.next=ListNode(curr%10)
      tail=tail.next
      curr = curr//10
      
    return dummyHead.next
```
{% endcode %}

{% code title="这写的什么玩意 赶紧忘掉" %}
```python
def reverse_list(node):
    previous_node = None
    while node:
        next_node = node.next
        node.next = previous_node
        previous_node = node
        node = next_node
    return previoous_node

def add_list(head1, head2):
    #1 reverse
    new_head1 = reverse_list(head1)
    new_head2 = reverse_list(head2)
    #2 add
    fake_head = ListNode(None) 
    cur_node = fake_head
    carry = 0
    while new_head1 and new_head2:
        temp_sum = new_head.val + new_head2.val + carry
        carry = temp_sum/10
        cur_node.next = ListNode(temp_sum%10)
        cur_node = cur_node.next
        new_head1 = new_head1.next
        new_head2 = new_head2.next
    #3 post processing
    while new_head1:
        temp_sum = new_head1.value+carry
        carry = temp_sum/10
        cur_node.next = ListNode(temp_sum%10)
        cur_node = cur_node.next
        new_head1 = new_head1.next
    while new_head2:
        temp_sum = new_head2.value+carry
        carry = temp_sum/10
        cur_node.next = ListNode(temp_sum%10)
        cur_node = cur_node.next
        new_head2 = new_head2.next
    if carry>0:
        cur_node.next = ListNode(carry)
    return reverse_list(fake_head.next)
```
{% endcode %}

###  If a linked list is a palindrome

####  方法一：Copy Linked List and Reverse

1. make a copy 
2. reverse the copied list
3. compare the two

缺点是空间消耗是O\(n\)



方法二：从中点reverse

1. find the middle of the list
2. cut in the middle and reverse the second half
3. compare first half and reversed second half

关于快慢指针找中点

{% code title="这写的什么乱七八糟的玩意 赶紧忘掉" %}
```python
class Solution(object):
  def middleNode(self, head):
    """
    input: ListNode head
    return: ListNode
    """
    # write your solution here
    if not head or not head.next:
      return head

    slow,fast=head,head

    while fast.next and fast.next.next:
      slow=slow.next
      fast=fast.next.next
    
    return slow
```
{% endcode %}

注意while的条件，这其实是提前一点停止，保证slow在中间而不是中间右一个

```python
def is_palindrome(head):
    fake_head = ListNode('fake_head')
    fake_head.next = head
    mid_node = find_mid(head)
    head2 = mid_node.next
    #break
    mid_node.next = None
    head1 = head
    head2 = reverse_list(head2)
    while head1 and head2:
        if head1.value != head2.value:
            return False
        head1 = head1.next
        head2 = head2.next
    return True

#test
head = ListNode(1)
head.next = ListNode(2)
head.next.next = ListNode(0)
```

corner case：None、数量不同、奇偶

{% code title="上面的都忘掉 以这个为准" %}
```python
class Solution(object):
  def isPalindrome(self, head):
    """
    input: ListNode head
    return: boolean
    """
    # write your solution here
    if not head or not head.next:
      return True
    
    mid = self.findmin(head)
    head1, head2 = head, mid.next
    mid.next = None
    head2 = self.reverse(head2)
    return self.compare(head1, head2)
  
  def findmin(self, head):
    slow, fast = head, head.next
    while fast and fast.next:
      slow = slow.next
      fast = fast.next.next
    return slow
  
  def reverse(self, head):
    if not head or not head.next:
      return head
    newhead = self.reverse(head.next)
    head.next.next=head
    head.next=None
    return newhead
  
  def compare(self, head1, head2):
    while head1 and head2:
      if head1.val!=head2.val:
        return False
      head1=head1.next
      head2=head2.next
    return True 
```
{% endcode %}

### 找中点

clarrification: 一上来要问clarification: 比如，偶数个的node的时候中点是左边的还是右边的？ 左边的，因为有左可以找到右，有右就找不到左了；以及左边的会更方便做题的分析，比如merge sort

data structure: slow and fast, fast之前包含fast的节点个数=2\*slow之前包含slow的节点个数

initialization:slow=head, fast=head.next

```python
class Solution(object):
  def middleNode(self, head):
    """
    input: ListNode head
    return: ListNode
    """
    # write your solution here
    # # of node on the left of fast = 2* # of node on the left of slow (including)
    if not head or not head.next:
      return head
    
    slow, fast = head, head.next

    while fast and fast.next:
      slow=slow.next
      fast=fast.next.next
    
    return slow 
```

> online vs. offline: 
>
> offline algorithm: 必须读出所有的数据  
> online algorithm: 不需要读出所有数据
>
> 如果有一个linkedlist，不知道linkedlist有多长，那么可以先读完一遍然后再读半遍； 如果是slow&fast，物理意义永远不变，哪怕只做了一半断电了，这个时候slow停下的位置依然是中点；这对于“数据量大小未知”的问题非常方便。

### Merge 2 linked list

1. Clarification - ascending or descending - data type: integer - what to do when there is duplication: 
2. Data Structure: DummyHead curr1 curr2 curr

{% hint style="info" %}
**dummyHead:**   
\(1\) When we need to change the head / when the headNode might be changed  
\(2\) When we need to build a new Linked List from scratch \(from 0 node\)  
**dummyTail:**  
When we need to expand elements from the end of the Linked List. 
{% endhint %}

```python
class Solution(object):
  def merge(self, one, two):
    """
    input: ListNode one, ListNode two
    return: ListNode
    """
    # write your solution here
    dummyHead=ListNode('dummy')
    tail = dummyHead
    curr1, curr2 = one, two
    
    while curr1 and curr2:
      if curr1.val<=curr2.val:
        tail.next=curr1
        curr1=curr1.next
      else:
        tail.next=curr2
        curr2=curr2.next
      tail = tail.next
    
    tail.next = curr1 if curr1 else curr2
    
    return dummyHead.next
```

### ReOrder Linked List

Reorder the given singly-linked list N1 -&gt; N2 -&gt; N3 -&gt; N4 -&gt; … -&gt; Nn -&gt; null to be N1- &gt; Nn -&gt; N2 -&gt; Nn-1 -&gt; N3 -&gt; Nn-2 -&gt; … -&gt; null

**Examples**

* L = null, is reordered to null
* L = 1 -&gt; null, is reordered to 1 -&gt; null
* L = 1 -&gt; 2 -&gt; 3 -&gt; 4 -&gt; null, is reordered to 1 -&gt; 4 -&gt; 2 -&gt; 3 -&gt; null
* L = 1 -&gt; 2 -&gt; 3 -&gt; null, is reordred to 1 -&gt; 3 -&gt; 2 -&gt; null

{% hint style="info" %}
Step 1: Find Mid and Split to 2 Linked List  
Step 2: Reverse the second Linked List  
Step 3: Merge the two Linked List
{% endhint %}

```python
class Solution(object):
  def reorder(self, head):
    """
    input: ListNode head
    return: ListNode
    """
    # write your solution here
    if not head or not head.next:
      return head
    
    mid = self.findmid(head)
    head1 = head
    head2 = mid.next
    mid.next = None
    head2 = self.reverse(head2)
    newhead = self.merge(head1, head2)
    return newhead

  def findmid(self, head):
    slow, fast = head, head.next
    while fast and fast.next:
      slow=slow.next
      fast=fast.next.next
    return slow

  def reverse(self, head):
    if not head or not head.next:
      return head

    newhead = self.reverse(head.next)
    head.next.next=head
    head.next=None
    
    return newhead

  def merge(self, head1, head2):
    dummyHead=ListNode('dummy')
    tail=dummyHead
    
    while head1 and head2:
      tail.next=head1
      head1=head1.next  #注意这一行和下一行不能换位置 要先让h1脱险
      tail.next.next=head2
      head2=head2.next
      tail=tail.next.next

    tail.next = head1 if head1 else head2

    return dummyHead.next
```

### Merge Sort Linked List

```python
class Solution(object):
  def mergeSort(self, head):
    """
    input: ListNode head
    return: ListNode
    """
    # write your solution here
    if not head or not head.next:
      return head
    mid = self.findmid(head)
    head1, head2 = head, mid.next
    mid.next = None
    left = self.mergeSort(head1)
    right = self.mergeSort(head2)

    return self.merge(left, right)
    

  def findmid(self, head):
    if not head or not head.next:
      return head
    slow, fast = head, head.next
    while fast and fast.next:
      slow=slow.next
      fast=fast.next.next
    return slow
  
  def merge(self, head1, head2):
    dummyHead = ListNode('dummy')
    tail = dummyHead

    while head1 and head2:
      if head1.val<head2.val:
        tail.next=head1
        head1=head1.next
      else:
        tail.next=head2
        head2=head2.next
      tail=tail.next
    
    tail.next=head1 if head1 else head2

    return dummyHead.next
  
```

## 链表、环的问题

### Check if linkedlist has cycle

{% hint style="info" %}
这里可以不需要一开始的sanity check
{% endhint %}

```python
class Solution(object):
  def checkCycle(self, head):
    """
    input: ListNode head
    return: boolean
    """
    # write your solution here
 #   if not head:
 #     return False
    slow, fast = head, head

    while fast and fast.next:
      slow=slow.next
      fast=fast.next.next
      if fast==slow:
        return True

    return False
```

### Cycle Node In Linked List

**找到环的入口点？**

定理：**slow和fast相遇点为p，让slow从head开始，fast从p开始，每次往后各走一步，直到slow和fast再次相遇，则相遇点即为环的入口。**

证明：

当fast若与slow相遇时，slow肯定没有走遍历完链表，而fast已经在环内循环了n圈\(n&gt;=1\)。假设slow走了s步，则fast走了2s步（fast步数还等于s 加上在环上多转的n圈），设环长为r，则：

2s = s + nr 即：s= nr

设整个链表长L，环入口与相遇点距离为x，起点到环入口点的距离为a。

则s=a+x, L=a+r。那么a + x = nr = \(n – 1\)r +r = \(n-1\)r + L - a，则有a = \(n-1\)r + \(L – a – x\)。

\(L–a–x\)为相遇点到环入口点的距离，由此可知，从链表头到环入口点等于\(n-1\)循环内环+相遇点到环入口点，于是我们从链表头、相遇点分别设一个指针，每次各走一步，两个指针必定相遇，且相遇第一点为环入口点。

```python
class Solution(object):
  def findCycle(self, head):
    """
    input: ListNode head
    return: ListNode
    """
    # write your solution here
    if not head or not head.next:
      return None
    slow, fast=head, head
   
    while fast and fast.next:
      slow=slow.next
      fast=fast.next.next
      if slow==fast:
        break
    
    if slow==fast:
      slow=head
      while slow!=fast:
        slow=slow.next
        fast=fast.next
      return slow
    
    return None

```

### **如何知道环的长度？**

记录下碰撞点meet，slow、fast从该点开始，再次碰撞所走过的操作数就是环的长度r

### Insert into a Cyclic Sorted List

```python
class Solution(object):
  def insertCircularList(self, head, newVal):
    """
    input: ListNode head, int newVal
    return: ListNode
    """
    # write your solution here
    newNode=ListNode(newVal)

    if not head:
      newNode.next=newNode
      return newNode

    prev, curr = head, head.next
    
    while True:
      if prev.val<=newVal and curr.val>=newVal: #新节点需要插入到某2个节点之间，这两个节点是顺序的
        break
      if prev.val>curr.val and (curr.val>newVal or prev.val<newVal): #新节点需要插入到某2个节点之间，这两个节点是有break的 5——>6——>1 插入0 或者插入7
        break
      if curr is head: #遍历链表找到尾结点
        break
      
      prev = prev.next
      curr = curr.next
    
    newNode.next=curr
    prev.next=newNode
    
    return head
```

### **判断两个无环单链表是否相交**

如果相交，给出相交的第一个点。

一、将其中一个链表L2首尾相连，检测另外一个链表L1是否存在环，如果存在，则两个链表相交，而检测出来的依赖环入口即为相交的第一个点。但是这样改变了输入的结构，不太好。

二、如果两个链表有一个公共结点，那么该公共结点之后的所有结点都是重合的。那么，它们的最后一个结点必然是重合的。因此，我们判断两个链表是不是有重合的部分，只要分别遍历两个链表到最后一个结点。如果两个尾结点是一样的，说明它们用重合；否则两个链表没有公共的结点。

在上面的思路中，顺序遍历两个链表到尾结点的时候，我们不能保证在两个链表上同时到达尾结点。这是因为两个链表不一定长度一样。但如果假设一个链表比另一个长l个结点，我们先在长的链表上遍历l个结点，之后再同步遍历，这个时候我们就能保证同时到达最后一个结点了。由于两个链表从第一个公共结点开始到链表的尾结点，这一部分是重合的。因此，它们肯定也是同时到达第一公共结点的。于是在遍历中，第一个相同的结点就是第一个公共的结点。

在这个思路中，我们先要分别遍历两个链表得到它们的长度，并求出两个长度之差。在长的链表上先遍历若干次之后，再同步遍历两个链表，知道找到相同的结点，或者一直到链表结束。此时，如果第一个链表的长度为m，第二个链表的长度为n，该方法的时间复杂度为O\(m+n\)。

### 判断两有环单链表是否相交 <a id="&#x5224;&#x65AD;&#x4E24;&#x6709;&#x73AF;&#x5355;&#x94FE;&#x8868;&#x662F;&#x5426;&#x76F8;&#x4EA4;"></a>

思路：

1. 先判断两链表是否均有环，有一个无环则不相交
2. 若入环节点一样，则相交
3. 若入环节点不一样，则从一个入环点往后遍历，若能和另一个链表的入环点相遇则相交，若遍历回自己的入环点还没相遇，则不相交

注意：

* 第 3 步里从一个入环点往后遍历，注意起始条件

