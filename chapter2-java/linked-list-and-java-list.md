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

![](../.gitbook/assets/image%20%2828%29.png)

如果在这个时候加了

```java
temp.next = temp.next.next;
```

temp.next是object1，temp.next = null, 读一个null没有关系，不会NPE

但是下面的是NPE，因为相当于让current.next指向null.next

```java
current.next = current.next.next;
```

另外，如果刚才的curr、next、temp是在field里定义的，那么这些reference就存在了heap里。没有method的area （field里 method外）不能多次赋值，所以3，4，6，7行就不行。

## linked list Operation

linked list和语言无关，是一个data structure，都是由listnode组成的链表；但是大写的LinkdList是一个class

### length

```java
int length(ListNode head){
    int count=0;
    while (head!=null){ //head==null时退出，此时一定是过了最后一个节点 因为这个代码的逻辑是要路过每一个node，每路过一个就+1
        head = head.next;
        count++;
    } 
    return count;
}
```

### get\(index\)

```java
ListNode get(head, int index){
//asumption: index>=0
    ListNode prev = new ListNode(null);
    prev.next = head;
    while (index>0 and prev.next!=null){ //index<=0 or prev.next==null
        prev=prev.next;
        index--;
    }
    return prev.next;
}
```

这里return listnode object会更现实，因为这就不需要对val做assumption，以及在java里只有interger type才能有null

### appendHead\(\)

```java
ListNode appendHead(head, int val){
    ListNode newNode = new ListNode(val);
    newNode.next = head;
    return newNode;
}
```

### appendTail\(\)

```java
ListNode appendTail(head, int val){
    ListNode tail = new ListNode(val);
    if (head==null){
        return tail;
    }
    ListNode prev = head;
    while (prev.next!=null){
        prev=prev.next;
    }
    prev.next=tail;
    return head;
}
```

以上所有操作，虽然head都有在变，但是我们从头到尾没有失去对head的控制权，所以其实不需要dummyHead

### remove\(\)

## ArrayList vs LinkedList

| Operation | ArrayList | LinkedList |
| :--- | :--- | :--- |
| get\(int index\) at head/tail | O\(1\) | O\(1\) maintain both head and tail |
| get\(int index\) in middle | O\(1\) | O\(n\) |
| set\(int index\) at head/tail | O\(1\) | O\(1\) |
| set\(int index\) in middle | O\(1\) | O\(n\) |
| add\(int index\) in middle | O\(n\) | O\(n\) |
| add\(int index\) at head | O\(n\) | O\(1\) |
| add at tail | _**amortized O\(1\)**_  | O\(1\) |
| remove\(int index\) at head | O\(n\) | O\(1\) |
| remove\(int index\) at tail | O\(1\) | O\(1\) |
| remove\(int index\) at middle | O\(n\) | O\(n\) |
| size\(\) | O\(1\) | O\(1\) |
| isEmpty\(\) | O\(1\) | O\(1\) |

.size\(\)是array实际占用的内存，比如1,3,4,5,null,null,null .size\(\)是4，而.length\(\)是7.

在Java中一旦扩容了，就不会再自动缩回。除非用户call了trimtosize\(\)。这是因为扩容本身在实际生产中是一个为了稳定运行的很重要的信息，它代表了系统的稳定状态。另外，每次的缩小其实是浪费时间的。

* 如果有大量的random access，用array list；
* 如果总需要在头尾两段插入，用array list; 因为时间复杂度相似，用array list，避免overhead，更**高效**使用空间，避免不连续，也是为了locality；
* linkedlist比较灵活，像是瑞士军刀，虽然都能做，但是不一定都能做的好，比如它可以stack可以queue可以deque，而arraylist比较专一。
* 在Java中不要用stack和vector。需要用vector时用arraylist，需要用stack时用deque\(linkedlist, arraydeque\)

## LinkedList in Java

{% embed url="https://docs.oracle.com/javase/7/docs/api/java/util/LinkedList.html" %}

Java中的LinkedList是一个双链表，所以在数据结构里有tail, head, size；它的ListNode有prev还有next.

```java
class LinkedList<E>{
    private ListNode<E> head;
    private ListNode<E> tail;
    private int size; //维护size这个field，使它up-to-date，这叫eager-computation
    //每一次增删查改都马上更新
    //与之相反的是Lazy computation，就像是在DDL前才会做作业

}
```

eager computation 和 lazy computation：前者适用于 容易维护的操作； 后者适用于 难以维护或者使用不太频繁的

Class LinkedList&lt;E&gt; 这个尖括号是Generic，指的是这个linkedlist里是装这个的类型的object，E就是它的类型。另外，这个E只能是object，不能是primitive type。比如，一个&lt;student&gt;里只能是student，不能teacher；同时，也不能放int，因为是primitive；如果想装，只能用wrapper class，Integer. 

如果看上面的document，会发现这些method有很多可以给别的数据结构用。

### List Interface in Java 

接口：如果给某些数据结构说明“我需要这些功能”，比如set, get, add, remove. 它只提需求，不管实现。就像是PM和engineer，PM只提要求，不会在意实现。这在Java里就是Interface和具体的Class的关系。通过定义abstract method signature来提需求，implementation class干活。

```java
interface MyList{
    public boolean set(int index, Integer e); //实现一个get, abstract method,因为没有{}
    public Integer get(int index); //实现一个set, abstract method,因为没有{}
}

class MyArrayList implements MyList{ //相当于MyArrayList是MyList雇佣的一个engineer
    @Override
    public boolean set(int index, Integer e){
    //
        return false;
    } 
    
    @Override
    public Integer get(int index){
    
        return null;
    }
}

class MyLinkedList implements MyList{ //这里的实现方法可以不一样，但共同特点是，保证了自己里面都有MyList里规定的方法
//另外它们各自也可以有自己的方法 这就是优点

}
```

注意在java里有一个list，所以如果想要自己写一个interface，就只能叫MyList，下面同理。

使用interface, programming against interface 可以让代码更加flexible，提高复用性。

比如这个`List<Node> myList = new LinkedList<Node>();`  
另外上面不可以 `new List<Node>()` 因为java不可以new一个新的interface。

左边声明的类型比右边的更general；左边可以是右边实现的interface，也可以是和右边一样的类型。此时可以使用的method是List里的method，LinkedList里用来实现queue等的要求不会出现在mylist里。所以能调用哪些API是由左边声明的类型决定的。如此下来，只要是List的子类，都可以拿来实现这个interface。

interface和子类的关系 vs linkedlist实现了list的interface

implementing interface:   
implemented interface: linkedlist implements 一大堆, 包括queue, list之类的。linkedlist就实现了它implements的interface里的全部的method，少一个都不行。

```java
public Node getRandom(List<Node> input){ //可以被各种类型调用
    int randIndex = ...
    return input.get(randIndex); //可能每一个list的get是不一样的，在每个不同list实现对应的具体类中定义了
}

ArrayList<Node> arrayList; //以下每一个类型的list都能放心用上面的模板
getRandom(arrayList);

LinkedList<Node> linkedList;
getRandom(linkedList);

MyList<Node> myList;
getRandom(myList);
```

interface也可以实现interface，比如可以有一个`MyList2 extends MyList{ }`但是interface没有constructor。

## Abstract Class

一个会写代码的PM

