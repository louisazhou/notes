# Heap in Java

## PriorityQueue

操作queue的interface，offer\(\) O\(log\), peek\(\) O\(1\), poll\(\) O\(logn\),  size\(\), isEmpty\(\); 不再是FIFO，而是按照优先级的out。最高层次的抽象，使用的角度，用它的语义层面它是queue；落实的层面，array。

**remove\(\) O\(logn\)** - 堆顶的pop，这是throw exception的

**remove\(Object\) O\(n\)** - remove特定的node，要search，最坏情况每个都要看，不能看到某个node就马上剪枝，因为左右的大小关系不知道。和BST不同，BST中的删除是O\(height\)

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

4. `PriorityQueue<Cell> heap = new PriorityQueue<Cell>(16, new MyComparator());`

* class MyComparator implements Comparator &lt;Cell&gt;
* Java 8+ only

{% hint style="info" %}
1. 16, 11是initial capacity； initial，说明这是初始，后面可以变；capacity意味着这是容量，能装多少，而不是size。 
2.  initial capacity不可以&lt;=0，所以传参的时候必须判断corner case 
3. method 1和2必须comparable 不然会cast异常
{% endhint %}

## Nested Class

Java里允许类里面嵌套类

Anonymous Class 匿名类 在方法里只在这里用这个类，只用这一次，不会重用，不存在复用，所以名字都没起，类中路人甲。在priority queue中路人甲很常见，因为我们需要给与comparator，这个顺序可能只在这个priorityqueue中用一次，也不会再被使用了。

![](../.gitbook/assets/image%20%2834%29.png)

```java
PriorityQueue<Cell> pQueue = new PriorityQueue<>
```

1\) 实现一个接口，comparator

2）定义一个类

3）创建一个instance

4）Call Constructor 

最外面的\(\)是constructor的 以上相当于



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

