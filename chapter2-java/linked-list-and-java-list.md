# Linked List and Java List

如果用array来实现queue或者stack，会面临空间的问题，因为Java的array需要定长。这里有了悖论：我们需要长度动态的数据结构来实现queue/stack，但是array不能动态。这时就有了linkedlist解决这一问题。

如何表示一个node？

```java
class ListNode{
    public int value; //any type
    public ListNode next; //next is a reference, and it points to another ListNode
    //public ListNode prev;
    
    public ListNode(int value){
        this.value = value;
        this.next = null;
    }
}
ListNode head = new ListNode(3);
head.next = new ListNode(4)
```

`next`是一张名片，指向一个listnode类型的object

### array vs linked list

**Memory Layout:** 都在逻辑上连续  
区别：array的存储物理地址连续 consecutive allocated memory space, no **overhead**  
但是linked list物理地址上不连续 non-consecutive, **overhead** of multiple objects with the 'next' reference.

> 如果能使用最低成本来做某事，那么刨除最低成本，剩下的都算overhead\(额外开销\)

**Random Access:** get ith element  
Array： O\(1\) 因为物理上连续，所以如果访问array\[17\]就是我们想要的第18号  
Linked List： worst case O\(n\) 当访问tail上的元素时

**Search：**search by value  
if sorted: array O\(logn\), linkedlist O\(n\) 本质原因是random access的时间复杂度不一样

### 内存上的情况

```java
ListNode current = null; //stack上建了一个current，空名片
ListNode next = null;
current = new ListNode(0); //heap上new了一个object，value是0，next是null current指向它
next = new ListNode(1);
ListNode temp = current; //stack上的temp，复制了current的名片，也指向了heap上的同一片空间
current.next = next; //heap上dereference，沿着这个名片找到heap上的ListNode，让这个listnode指向next所指向的那个object（ListNode1）
current = current.next //current指向了一个新的object，原来的名片没了
```

以上操作后，current = 1, next = 1, temp = 0  
line 6是最特殊的，因为它有dereference，它的操作是在heap上做的写，对heap上的链表结构有了改变。而其余的是stack上的，等函数调用结束后它们就随风而去了。

![](../.gitbook/assets/image%20%2826%29.png)

如果在这个时候加了

```java
temp.next = temp.next.next
```

temp.next是object1，temp.next = null, 读一个null没有关系，不会NPE

但是下面的是NPE，因为相当于让current.next指向null.next

```java
current.next = current.next.next
```

## Linked List Operation

### length

```java
int length(ListNode head){
    int count=0
    while (head!=null){ //head==null时退出，此时一定是过了最后一个节点 因为这个代码的逻辑是要路过每一个node，每路过一个就+1
        head = head.next
        count++
    } 
    return count
}
```

### get\(index\)

```java
ListNode get(head, int index){
//asumption: index>=0
    ListNode prev = new ListNode(null)
    prev.next = head
    while (index>0 and prev.next!=null){ //index<=0 or prev.next==null
        prev=prev.next
        index--
    }
    return prev.next
}
```

这里return listnode object会更现实，因为这就不需要对val做assumption，以及在java里只有interger type才能有null

### appendHead\(\)

```java
ListNode appendHead(head, int val){
    ListNode newNode = new ListNode(val)
    newNode.next = head
    return newNode
}
```

### appendTail\(\)

