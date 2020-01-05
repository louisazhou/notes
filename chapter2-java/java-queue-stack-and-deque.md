# Java Queue, Stack and Deque

## Java Interface:

FIFO: Queue, Deque \(not necessary\)  
LIFO: Stack \(do not use\), Deque

> deque和queue的关系，看Java Documentation, deque是queue的subinterface，所以deque实现了queue的功能。

## Implementation Class:

Array: ArrayDeque  
Linked List: LinkedList  
  
`Queue <> = new ArrayDeque`

## In Java

### Queue

> Java里的queue不一定是FIFO，例外是priority queue \(heap\)。所以其实in general， queue是一种数据结构，一头进，另一头出的基本功能。因为我们不希望random access, 所以arraylist就不合适了。

enqueue 入队: offer\(\) at the tail  
dequeue 出队: poll\(\) at the head  
peek     看第一个: peek\(\) look at the head without polling it out 

queue在空了的时候如果peek\(\)和poll\(\) 会拿出null. 下面表格的左右两组API不可以混用，必须consistent. 

$$
\begin{array}{|l|l|l|}\hline & {\text { Common APls }} & {\text { Queue }} \\ \hline \text { Type of } & {\text { throw exception }} & {\text { return special }} \\ {\text { operation }} & {} & {\text { value(null) }} \\ \hline \text { Insert } & {\text { add(e) }} & {\text { offer }(e)} \\ \hline \text { Remove } & {\text { remove() }} & {\text { poll() }} \\ \hline \text { Examine } & {\text { element() }} & {\text { peek }()} \\ \hline\end{array}
$$

在空了时如果调用remove\(\)或者element\(\), NoSuchElementException;   
add\(e\)的异常是满了，比如用ArrayBlockingQueue就有容量限制，会throw IllegalStateException。如果是offer\(\)，在满了时返回false，表示加失败了。

### Deque

stack在空了的时候如果peek\(\)和poll\(\) 会拿出null，deque也是一样. 

![](https://cdn.mathpix.com/snip/images/CYIlNjeVehW-TUvLDvAE3LgyWGl4UnI7qBBXNYAxgnA.original.fullsize.png)

### 其他

#### isEmpty\(\)

#### size\(\)

#### stack method的Push Pop Peep 尽量不用

其实对应的是2个throw exception那组的addfirst removefirst 和 peekfirst 真是讨厌

### Operation Cost O\(1\)

以上所有的实现，时间复杂度都是O\(1\) 常用LinkedList和ArrayDeque（Java6以后）实现。在实际使用中，更常用ArrayDeque，因为locality更低、overhead更好、出现null的时候没有歧义。而只有一个情况下需要使用linkedlist：版本是Java6之前的。

此外，ArrayDeque不允许null value，因为不能确定此时是值为null还是空了。这个时候如果用linkedlist实现就没有问题，因为list就可以存null（这里不是在说value是null）。

## Implementation

arrayDeque和Linkedlist是用来实现stack queue 和deque这几个data structure的implementation class

### Implement a Stack with Linked List

更好的是在头部插入删除，而不是在尾部。因为头部的操作更容易，而尾部即便是维护一个tail，每次在删除的时候就没了，又得再维护一个。

```java
class ListNode{
    int value;
    ListNode next;
    public ListNode(int value){
    this.value=value;
    }
}

public class Stack{
    private ListNode head;
    
    Stack(){
    }
    
    public void push(int e){
    ListNode newhead = new ListNode(e);
    newhead.next=head;
    head=newhead;
    }
    
    public integer pop(){
    if (head==null){
        return null;                  //不要return head 不然类型不一致
    }
    ListNode prev=head;
    head = head.next;
    prev.next=null;                     //这里要防呆
    return head.value;
    }
    
    public integer peek(){
    if (head==null){
        return null;                  //不要return head 不然类型不一致
    }
    return head.value;
    }

}


//push()
//pop()
//peek()
```

### Implement a Queue with Linked List

头进尾出：存在和上面一样的问题  
尾进头出：和刚才一样

```java
class ListNode{
    int value;           //如果允许存null value
    ListNode next;
    public ListNode(int value){
    this.value=value;
    }
}

public class QueueStack{
    private ListNode head;
    private ListNode tail;
    Queue(){
    }
    
    public void offer(int e){
    ListNode newtail = new ListNode(e);
    if (head==null){
        head = newtail;
        tail = head;
    }else{
        tail.next=newtail;
        tail=newtail;
    }
    
    public nteger poll(){
    if (head==null){
        return null;                  
    }
    ListNode node=head;
    head = head.next;
    if (head==null){         //重要 只剩一个元素的时候head和tail是一个
        tail = null;
    }               
    node.next = null;    
    return node.value;
    }
    
    public nteger peek(){
    if (head==null){
        return null;                  
    }
    return head.value;
    }

}

```

### Circular Array \(Ring Buffer\)

两种写法：

* `head = head +1==array.length?0: head+1;`
* `head = (head+1)%array.length;`

不要用这种写法，要防止overflow：  
`head++;  
array[(head%array.length)]`

当head==tail时，它要么是empty，要么满了，解决方法：

* 维护一个size head有数据，tail没数据，读的时候读的是head
* 如果不支持加一个size，就改变head和tail的物理意义，使得head+1==tail时是空，而head==tail时是满，也就是head指的格子不存数据，tail的格子也不存数据

![](https://cdn.mathpix.com/snip/images/K7HNXqc0JaOKeTywSKh1rlnH8fsTZMAoRo-dQuHu-0o.original.fullsize.png)

poll时: head+1的元素拿走，head++

如果满了，可以1.5X extension的话可以把数据从head拷贝到tail

### Implement a Stack with Array

