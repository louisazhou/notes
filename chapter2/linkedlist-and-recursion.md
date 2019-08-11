# Linkedlist and Recursion

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

无论马自达还是兰博基尼，有唯一的VIN-number.定义class就描述了一个对象应该具有的状态和行为是什么。所以class是对于对象的描述。所以一个class是一个**blueprint**，描述了一系列我们想要的object具备的特点是什么。.  


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

```python
def traverse(head):
    while head is not None:     #while head 等价
        print head.value
        head = head.next
```

注意head是一个reference，并不是一个value。就像是身份证可以代表人，但是身份证和本人是两个完全不同的物理存在。所以line4的含义是reference的箭头。

遍历是其他所有操作的基础，帮助我们从头节点走到最后一个节点。

### Search 搜索

#### search by index

假设第一个list node的index是0，给一个单链表，找对应index的节点，输出。如果找不到，return None。

所以这个问题的核心还是遍历traverse，



