# Map/Set/Hash

## Set

Set is an interface, 可以实现它的class有：

* Hashset: 内部没有顺序，操作O\(1\)
* Treeset: 内部有顺序，增删查改O\(logn\); key可以按照natural order或者comparator的顺序排好 内部是self-balancing BST \(red-black tree\)
* LinkedHashSet: 内部维护了插入的顺序，可以从head一直next到tail；其实是个linkedlist, &lt;key，previous，next&gt;, key满足set的条件，没有重复；增删查改O\(1\)

## Map

is also an interface, &lt;key, value&gt;, 可以实现它的class：

* HashMap
* TreeMap
* LinkedHashMap

### class HashMap&lt;K,V&gt;

K: the **type** of keys maintained by this map;  
V: the **type** of mapped values  
因为是generics 所以不支持primitive的类型

V put\(K key, V value\) put可以用来放，可以用来update，所以返回的type是原来的value或者null  
V get\(Object key\) 查key，返回对应的value；如果不存在返回null  
V remove\(Object key\) 返回删除的record 如果不存在，也不error，返回null  
boolean containsKey\(Object key\) 是否存在  
**boolean containsValue\(Object value\)** -- **O\(n\)** 如果想O\(1\)，可以做一个反向的hashmap，value变key，key变value，然后如果key现在有重复了，直接list of objects  
void clear\(\)  
int size\(\)  
boolean isEmpty\(\)

> 上面是因为比较的时候用的是.equals\(\)，这里的参数是object

### Hashtable

被抛弃了，因为thread safe

### class HashSet&lt;K&gt;

内部有一个private的HashMap，把所有V的部分都placeholder了。

## Collision Control

### Separate Chaining 

读完整个list，发现没有，那就把插入的元素放在头部，因为刚插入的元素被读的可能性比较大

### Open Addressing

如果hash后去找，直到空位为止；如果中间有删除怎么办？删除了之后空了的位置mark一下，以后插入的时候可以在这个被mark的地方插入。可以lazy compute，每过一点时间清空了重算hash，把该腾的腾出来。

## Implementation

### hashCode\(\)

1. hashCode\(\)的实现对hashmap的性能很重要，因为需要让hashCode足够均匀
2. Java默认的hashCode\(\)是基于地址计算的 所以就算是我们认为一样的key，也会被放进不同的bucket里。所以即使equals相等了，hashCode却不等，也无法通过get\(\)找到正确的&lt;key,value&gt;。

### equals

1. == 对于primitive来说比的是值是否相等，primitive没有equals\(\)；对于object来说==比的是地址，equals\(\)怎么比取决于class的实现，如果class里没有实现，用的是object默认的方法，object默认方法就是==。
2. a.equals\(b\)和b.equals\(a\)不是绝对相等 比如linkedlist和arraylist如果都存了一样的东西，如果l1.equals\(l2\)会return True 因为不同类型也可以相等，因为list下有一个abstract list, 它是linkedlist和arraylist的父类。在使用时尽量用什么类型put就用什么类型查找，因为它会override本身object类型的equals。

以上两个都得override

在Java里有一个equals和hashCode的contract，当equals override了之后，需要保证两个equals的hashCode也一样。所以hashCode\(\)也要改。

需要注意的是，HashMap can only have one null key, it is always mapped to bucket 0 `(if key==null return 0)` 

### Rehashing, load factor

load factor控制着rehashing，如果 number of &lt;k,v&gt;/number of buckets过大，比如大于0.75，就会rehashing。所有的元素都会参与rehashing。





