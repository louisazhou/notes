# Heap in Java

## PriorityQueue

操作queue的interface，offer\(\) O\(log\), peek\(\) O\(1\), poll\(\) O\(logn\),  size\(\), isEmpty\(\); 不再是FIFO，而是按照优先级的out。最高层次的抽象，使用的角度，用它的语义层面它是queue；落实的层面，array。

**remove\(\) O\(logn\)** - 堆顶的pop，这是throw exception的

**remove\(Object\) O\(n\)** - remove特定的node，要search，最坏情况每个都要看，不能看到某个node就马上剪枝，因为左右的大小关系不知道。和BST不同，BST中的删除是O\(height\)

why array: 其实当然也可以用binary tree，但是这样做的存储效率非常低，locality和overhead的问题。更重要的原因是给一组数据来做heapify的时候，拿到的东西都是array，本身拿到的就是一个连续存放的array。此时，“树在我心中”。为什么是complete binary tree? 想想array里的数据也是连续存的。如此，array的每一个数都和binary tree有了一一映射。树长什么样是不会随内容的变化而变化的。

## Order

如何定义优先级？

### Comparable：Element-type implementing comparable interface

"可比较的"，自己和别人比，方法是compareTo，objectA.compareTo\(objectB\)

默认方法创建的装integer的heap是min heap 其实是因为默认生成的是“优先级最高”的堆。

```java
PriorityQueue<Integer> minHeap = new PriorityQueue<Integer>();
```



```java

//值一样返回0 说明优先级一样
//<0说明当前元素的优先级比传入的another更高。
```

Integer中有compareTo这一段代码，所以序号小，优先级高，返回小于0的数。通过返回值的=&lt;&gt;，判断priorityQueue的优先级相对大小。

### Comparator: Provide an extra comparator object to compare the elements

“用于比较的工具” 可以认为是第三方，它拿来两个来比，方法是compare,

myComparator.compare\(objectA, objectB\)

`interface Comparator<E> {   
int compare (E o1, E o2);  
}`

```java
class Cell {
    public int row;
    public int col;
    public int value;
    public Cell (int row, int col, int value) {
        this.row = row;
        this.col = col;
        this.value = value;
    }
}
```

```java
class MyComparator implements Comparator<Cell> {
    @override
    public int compare(Cell c1, Cell c2) {
        if (c1.value==c2.value) {
            return 0;
        }
    return c1.value < c2.value ? -1; 1;
    }
}
```

这里不要用c1.value-c2.value，因为这会有溢出的问题，不要！比如，c1是特别小的负数，c2是特别大的正数。

`PriorityQueue<Cell> minHeap = new PriorityQueue<Cell>(11, new MyComparator());`

如果已经有一个comparable\(装哪个类型，这个类型提供一个comparable\)，但是想用一个新的定义优先级的方法，那就新写一个comparator. 

```java
class ReverseComparator implements Comparator<Integer> {
    @override
    public int compare(Integer i1, Integer i2) {
        if (i1.equals(i2)) {
            return 0;
        }
        return i1 < i2 ? 1 : -1;
    }
}
```

comparable只能最多定义出来一种顺序，与生俱来的顺序，叫natural ordering，自然序。comparator是第三方的比较器，create

### 小技巧

如果想要的是自然序的反序，`Collections.reverseOrder()`, 创建了自然序反序的comparator，不需要自己创建一个comparator了。static method, 不需要任何instance就可以调用，时间复杂度O\(1\)。

`PriorityQueue<Integer> maxHeap = new PriorityQueue<Integer> (16, collections.reverseOrder());`

### Possible ways to provide 

1. 顶格写  Top-level class
2. 写到class里 Static Nested class 活在类里面的类
3. 匿名类 Anonymous class
4. Lambda Expressions 

![](../.gitbook/assets/image%20%2857%29.png)

## PriorityQueue Constructor

1.`PriorityQueue<Cell> heap = new PriorityQueue<Cell>();`

* initialize the internal array with default capacity \(11\)
* class Cell implements Comparable&lt;ell&gt;! 

2. `PriorityQueue<Cell> heap = new PriorityQueue<Cell>(16);`

* initialize the internal array with specified capacity \(16\)
* class Cell implements Comparable&lt;cell&gt;!    **natural ordering 所以假如调用了一个没有comparable的时候，会在运行的比较时抛异常 而且是在Java自己的源代码里抛出异常 cast exception** 

3. `PriorityQueue<Cell> heap = new PriorityQueue<Cell>(16, new MyComparator());`

* initialize the internal array with specified capacity \(16\)
* class MyComparator implements Comparable&lt;Cell&gt;!    

4. `PriorityQueue<Cell> heap = new PriorityQueue<Cell>(new MyComparator());`

* class MyComparator implements Comparator &lt;Cell&gt;
* **Java 8+ only**

{% hint style="info" %}
1. 16, 11是initial capacity； initial，说明这是初始，后面可以变；capacity意味着这是容量，能装多少，而不是size。 
2.  initial capacity不可以&lt;=0，所以传参的时候必须判断corner case 
3. method 1和2必须comparable 不然会cast异常
{% endhint %}

## Nested Class

Java里允许类里面嵌套类

Anonymous Class 匿名类 在方法里只在这里用这个类，只用这一次，不会重用，不存在复用，所以名字都没起，类中路人甲。在priority queue中路人甲很常见，因为我们需要给与comparator，这个顺序可能只在这个priorityqueue中用一次，也不会再被使用了。

![](../.gitbook/assets/image%20%2835%29.png)

```java
PriorityQueue<Cell> pQueue = new PriorityQueue<>
```

1\) 实现一个接口，comparator

2）定义一个类

3）创建一个instance

4）Call Constructor 

最外面的\(\)是constructor的 以上相当于

## 实现heap

```java
public class MinHeap {
    private int[] array;
    private int size;
    
    public MinHeap(int[] array) {
        if (array==null||array.length==0) {
            throw new illegalArgumentException("input array cannot e null or empty");
        }
        this.array = array;
        size = array.length;
        heapify();
    }
    
    //see below
}    
```

### PercolateUp -- offer/update

> 老板家儿子公司体验生活

* compare with the parent, swap until parent has higher priority than self

```java
private void percolateUp(int index) {
    int parent_idx = (index-1)/2;
    
    if (parent_idx < 0 or array[index] > array[parent_idx]) {
        return;
    }
     
     swap(index, parent_idx);
     array[index], array[parent_idx] = array[parent_idx], array[index];
    
     percolateUp(array, parent_idx)
}

private void percolateUp(int index) {
    while(index>0) { //index<=0 exit
        int parent_idx = (index-1)/2;
        if (array[parent_idx])>array[index] {
            swap(array, parent_idx, index);
        } else {
            break;
        }
        index = parent_idx
    }
}
```

### PercolateDown --poll/ update/ heapify

> 皇帝驾崩 percolate down的条件是只有堆顶没了，左右children都是好的；此时做percolate down，就能很好的维护heap property

* swap
* percolate down, compare with both children, swap with the smaller one

```java
//parent要和children交换，children可能有null 技巧是swapcandidate 默认和左边比，因为可能没有右边
//只有右边有子女且右边比左边小时才swap右边 而且要用长度判断，不能直接== 因为这个时候已经out of bound了

private void percolateDown(int index) {


}
```

### Heapify\(\)

#### 正经的heapify  O\(n\)

> 一个节点的时候就是一个heap，所以可以一个个做percolate down，让以新元素为parent的subtree变成一个更大的heap。

从底到上，从右往左的顺序，对非叶节点做percolate down，一路往回走 两个视角，array的视角，保证代码简单；tree的视角维护heap的性质。

如何找“最后一个非叶节点”？ \(最后一个元素index-1\)/2一定是parent = \(n-2\)/2=n/2-1 其中n是array length

#### 时间复杂度O\(n\)

总共k层，1个元素向下看k-1层，2个元素向下看k-2层... 

![](../.gitbook/assets/image%20%2853%29.png)

错位相减，k=logn

```java
private void heapify() {
        for (int i=size/2-1;i>-1;i--) {
            percolateDown(i);
        }
    }
```

#### 假heapify: 把n个元素插入进heap  O\(nlogn\)

> offer and percolate up

![](../.gitbook/assets/image%20%2883%29.png)

### Update\(\)

看变大还是变小，调整update的元素以保持heap property

```java

```

### Poll\(\)

> 把size当一个挡板，换完后挡板往前移 （size--）

### Offer\(\)





### Java的API vs 自己实现

* Java的API无限，自己实现的有size，如果想要很长的，那array list扩容，\*1.5挪了
* 如果想自己实现一个支持comparator的priority queue，改所有update比大小的地方换成compare\(a,b\)的方法

## Smallest K Elements in Unsorted Array

1. put all the elements in a min heap, pop k times  复杂度
2. put k elements in a max heap, insert \(n-k\) times 

```java
public static int[] smallestKElements(int[] array, int k) {
    Queue<Integer> minHeap = new PriorityQueue<>;
    for (int i = 0; i < array.length; i ++) {
        minHeap.offer(array[i]);
    }
    int [] result = new int[k];
    for (int i = 0; i< k; i++) {
        result[i] = minHeap.poll();
    }
    return result;
}
```

stream processing vs batch processing



## Sorting Algorithms \(and heap\)

Java的arrays.sort来sort int\[\] array, 会使用quicksort；如果使用的是Integer array, 会使用mergesort。

### quicksort 对primitive type的排序

time worst case O\(n^2\), average O\(nlogn\),   
space worst case O\(n\), average O\(logn\)

**unstable sort** 排完序之后的顺序可能会改变 但对于primitive type来说，但这无所谓

### mergesort  对object的排序

time worst case O\(nlogn\), average O\(nlogn\),   
space worst/average case O\(n\)

**stable sort** key一样时不会改变两个元素的先后顺序 对于有多个field的object非常重要

比如，如果在Excel sheet里，先按照从高到低的分数排序，再按照性别排序，那么每个性别内部排序也是对的

### heapsort space O\(1\), time O\(nlogn\)

selection sort虽然可以 in place，但是每次往后看选出当前待排序的元素的最小的，都要完整的扫一遍；如果能把后面的都做成heap，那么这个时候拿出来一个元素再对heap做调整的空间复杂度是logn。

用heap来加速的selection sort：heap sort

把待排序的放在左边的max heap里，把堆顶元素往最后swap，堆越来越小，已排序的越来越大；每次拿出一个元素是logn，总共n个元素，所以时间复杂度是nlogn; 空间上 in place. 

#### 工业界不常用 原因

1. 虽然看起来复杂度低，但是操作的时间，heapify的时间长；
2. 算法locality比较差，因为头部节点扔到尾部，long time overhead;
3. heap sort无法distribute/parallelize 执行人唯一

