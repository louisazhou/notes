---
description: 单链表和各种操作
---

# Linkedlist

Python List，存储是连续的\(store all data sequentially in memory\). 所以如果想要在list中插入元素，需要为原有的list分配新的空间，把原来的list和想插入的元素放在足够大的新的位置，而不是假设原数组的下一个位置没有元素直接去占领。

如何插入数据但不让原来的数据移动？

## Linked List

linkedlist虽然也是线性结构，但它对内存单元的要求没有list这样严格，所以它在内存中的存储可以是不连续的。通过对ref的连接，就可以把元素串在一起。从逻辑上看，它依然是一个线性结构，但是在内存上不要求一个挨着一个。每一个object都有对另一个object的引用，可以很方便找到下一个object在哪里。

链表Linkedlist由节点组成，每个节点包含一个data和一个表示结构的存储（reference），reference存储的是下一个object的地址。如果知道链表中的第一个节点，就可以通过不断访问它的next来遍历它的结构。

### OOP——object oriented programming 

Python处理的基本单元是对象（object）。Anything can be an object. 

#### Object:

1. States ，每个对象有特有的状态（eg. 特征，属性）。比如汽车的颜色、轮子、品牌、大小、重量、型号、排量... 都描述了汽车的特点。更抽象的，速度是汽车的物理状态。
2. Behavior, 对象的行为，描述了能对对象做什么事，有什么样的互动。比如汽车，能跑，能加速或减速、前进或后退。

以上就是面向对象最核心的概念。

#### Class: 

无论马自达还是兰博基尼，有唯一的VIN-number.定义class就描述了一个对象应该具有的状态和行为是什么。class是对于对象的描述，一个class是一个**blueprint**，描述了一系列我们想要的object具备的特点是什么。  


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

### Remove all vowels in a linked list

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
    dummy = ListNode(0)
    dummy.next = head
    fast, prev = dummy, dummy
    for _ in range(n): 
      if fast.next:
        fast = fast.next
      else:
        return dummy.next

    while fast.next:
      fast, prev = fast.next, prev.next
    
    prev.next = prev.next.next
    return dummy.next 
```

### Add two linked list which represents large number 

大数：传统语言int long的范围是 $$2^{32}$$ 范围之外称为大数

在Python里自动支持大数，转化好了

1. 如何靠右对齐，个位对齐？ Reverse
2. 进位 carry 
3. 两个linkedlist不等长 所以注意while循环的break，post-processing
4. reverse最后的结果

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

```python
def find_mid(head):
    if head is None or head.next is None:
        return head
    slow = head
    fast = head
    while fast.next is not None and fast.next.next is not None:
        fast = fast.next.next
        slow = slow.next
    return slow
```

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

### 

