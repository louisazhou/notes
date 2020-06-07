# Fundamentals to Java

## Semantics vs Implementations

Semantics: 菜谱， 攻略  
Implementation: 拿着菜谱做菜，照着攻略打游戏

## Object Oriented Paradigm

除了primitive type之外，其它都可以是object. primitive type: int, short, long, float, double, boolean 注意是int不是Integer.

有了一个class，有了一堆object，每个object都有自己的state\(**field**\)和behavior\(**method**\)，这个时候如果想要找到某个class定义的object，就可以拿着名片去找object，此时的过程叫做dereference，而这张写了object地址的名片叫做reference。一个对象该有什么状态和行为？**type**定义。

**object\(对象\)** 

**class\(类\)** 定义每一个这个类的object有哪些状态，但不定义某一特定的对象的field是什么，它相当于只是蓝图，只是一个抽象的约束。

**instance\(实例\)** 其实和object一回事 ****每一个instance都有它对应的具体状态和行为

**reference\(引用\)** 写了object地址的名片

### Work with Objects

```java
Student firstStudent = new Student ("Tom")
//Declaration声明： firstStudent是一个reference 一张名片 指向一个Student类型的object
//Instantiation实例化: new 修出来一个、造出来一个 
// = : 把new出来的 Student赋值给了firstStudent 
//Initialization初始化: 精装修一个符合要求的object 调用constructor, 根据用户的输入给某些field赋初始值
```

### Object Memory Layout

**Stack\(栈\)：**stack的每一层叫stack frame，每一个function call的stack frame都存了当前层变量，存的是系统调用的状况；这占了系统一部分内存。  
**Heap\(堆\)：**存的是new出来的object

stack上的一张名片指向了一个heap上的object

![](../.gitbook/assets/image%20%2843%29.png)

在一个方法调用结束之后，这两张名片就不在了，但是它们暂时还留在内存里，直到garbage collection回收。假如这时来一个`jack=rose`那么此时改的是stack里的jack的指向，执行后的效果是jack的这张名片不再指向Jack的这个object，而是指向了Rose的这个object。

method里的local variable: stack

只在class里但不在任何一个method里的field: heap

> “在class但不在method里是field，在method里是local variable”技术问题的描述要非常准确，不能只说“在class里define的”，因为method也是在class里的。
>
> 小技巧：看到赋值时，想：等号左边的内容是stack还是heap，以此思考这个reference到底是什么

### constructor: 

1. 没有返回值类型
2. 名字和类名一模一样（包括大小写）
3. 只有在field的名字和local variable相同时用this.指代当前instance自己

### Coding Style

{% embed url="https://google.github.io/styleguide/javaguide.html" %}

class：HelloWorld,   
method/field name: main\(\), myName 如果有两个单词，使用camelCase，命名规范不用\_

## Static 属于class，不属于instance

> Members \(fields, methods, classes\) belong to class, not object. 
>
> members意味着它可以修饰fields/methods/class

### Static fields

属于类的状态；  
不同的instance有属于自己的一个field，比如特斯拉车的电池容量；这些field的状态是属于每一个instance的。  
但是如果是同一个class new出来的instance，它们可能有一些共有的特性，比如能源类型（电车）、价位逼格（都很贵）...这些field是属于这个类“特斯拉”的状态。这种就是加了static，因为static是修饰这一个class的，所有的instance都用这一份，唯一一份，这就叫“belong to class, not object”。

### Static methods

属于类的行为；  
在这里不能访问属于instance的field （只有在instance里才可以）

|  | Class | instance |
| :--- | :--- | :--- |
| Class | Yes | Yes |
| instance | NO | Yes |

有class,不一定有instance，但是又instance一定有class

Class variable \(static\)  
instance variable \(non-static\)  
Class method \(static\)  
instance method \(non-static\)

## Final 一旦确定，不能修改

> "Once assigned, can never be changed"  
> final **class**: 不能被继承，没有子类可以以此为基础 \(inheritance\) 只要是这个类型的，就是这个类型的；  
> final **method**: 不能被overridden   
> final **variable**：once assigned, cannot be assigned again

如果违背了final的要求，那么编译器会标红，无法编译。  
指向关系不能变，但是指向的内容可以变  
constant的默认值是 0 False Null  
static final=constant value 常量，全局独此一份且不会变

## Java Parameter Passing

### Pass by Value 

* **Pass by Value \(Java\)**: The method parameter **values** are **copied** to another variable and then the copied object is passed, that’s why it’s called pass by value.
* ~~**Pass by Reference  \(Python\)**: An alias or reference to the actual parameter is passed to the method, that’s why it’s called pass by reference. 引用传递，传的是找到这个参数的引用或address~~

如果传的参数是primitive type，就是复制这个primitive type  
如果传的参数是objects，就是复制这个object的reference （复印名片）



## Array vs. Objects

* arrays are **objects \(heap中\)**
* 因为array是new出来的

在Java的文档里，没有array这个class，只能用Java中设定好的方式来 syntax sugar  
Arrays可以用来操作array，比如Arrays.sort\(\), Arrays.toString

* 在Java中有一个array的length field 所以没有`arr.length()`,只有`arr.length`访问field没有括号
* array length field cannot be changed after created **\(final\)**  看下面的 `int[] arr=new int[5]; arr= new int[100]` 这个本质是new了一个新的array，原来的是5还是5 
* 所以array只适合确定长的情况，如果做题的时候不确定需要创建的这个array的长度，需要的是List，比如ArrayList和LinkedList

### 高维数组

Java中的高维数组是指向低维数组的reference的array，是array of references；虽然reference之间两两连续，但是这几维之间可以存得不连续 要在高维数组的定义时给出最高维

### 访问

O\(1\)做random access

### 常见错误

#### ArrayIndexOutOfBoundException 越界 New的Array只有1个，但是访问第2个

#### NullPointerException 空指针  沿着空名片找对象 也叫Dereference Null

```java
//NPE
int[] array=null; //没有钱包
int value=array[1]; //想从钱包拿出钱

//ArrayIndexOutofBoundException
int[][] array=new int[5][]; //{null, null, null, null, null}
int[] v1 = array[5]; //钱包里钱不够

int v2
```

reference

每一个operator都有一个return value,包括赋值语句。赋值的return value是所赋成功的值。



```java
int x = 11;
int y = 8;
system.out.println(x=y) //return value is 8; assign the value of y to x, and then print x 
```

x/y, the type of the operator's return is the type of the operands with widest range.

implicit cast: long z = 5/3  double y=3 在这里都在右边的计算完成后把它转成了左边的这个type   
forced cast: 可以高转低，要自己aware of loss

## Autoboxing and Unboxing 

### Primitive type vs. Wrapper class

* int: Integer
* long: Long
* char: Character
* double: Double
* boolean: Boolean

Wrapper Class objects, just like string, are **IMMUTABLE** 

`Integer a = 1;  //a-->object1  
a = 2;         //a-->object2` 

reassign了一个heap

### **Why Wrapper?**

1. **Generic type can not be primitive type. List&lt;Integer&gt;, not List&lt;int&gt;**
2. It can help provide useful functionalities and contracts \(equals\(\), hashCode, json serializer之类 注意comparable的子类才会有compareTo, object的子类不会有compareTo\)
3. 可以有null

> 但是能用primitive还是用primitive，因为wrapper class必然会有overhead

### Autoboxing

Java compiler makes automatic conversion from the primitive type to the wrapper class. 

> equals\(1\)也是做了autoboxing，因为equals来自object

### Unboxing

From wrapper class to primitive type 在做+, -, \*, /, &gt;&lt;这些操作时都是针对primitive type

== 的操作，如果是一个int a == Integer b 的时候，其实是比value，因为只有两边都是object的时候才是比地址，其他时候都是比值；如果一边是object，另一边是primitive，尝试对object做unboxing，然后比值，如果无法unboxing会报错。

如果Integer a &gt; Integer b，试着对两边做unboxing，如果可以unboxing成primitive，就比值，不能unboxing就报错。

`Integer a = 4;  //autoboxing  
a += 4;         //a = a+4 unboxing`

`int temp = a.intValue();  
temp += 4;  
a = Integer.valueOf(temp);`

> 如果对null也做了.valueOf会NPE
>
> 1）所以dict.get\(mid\)==target 而target是个int，那么NPE了，因为==的时候.valueof
>
> 2）integer type的i1,i2 然后i1&lt;i2的时候也是做了unboxing

### int\[\] vs. Integer\[\]

autoboxing和unboxing只发生在int和integer之间，array是不能直接做的，会有compile error。所以如果想要cast，只能一个个取出来

### 常量池

-128~127，一个byte的range；编译器里会把-128-127都放在池子里，赋值的时候Integer都会指向同一个object。

`Integer a = 127；  
Integer b = 127;  
System.out.println(a==b);  //依然是比地址  
//这个时候return true`

`Integer a = 127；  
Integer b = new Integer 127;  
System.out.println(a==b);  //依然是比地址  
//这个时候return false`

`Integer a = 128；  
Integer b = 128;  
System.out.println(a==b);  //依然是比地址  
//这个时候return false`

这件事情告诉我们 

1. 不要用==比较两个object，要调用a.equals\(b\)绝对安全，因为这会给唯一的结果，永远都是true

> 这个gc 5吗？

#### Comparators Revisited

如果在comparator里，写出了这样的：  
`public MyComparator implements Comparator<Integer> {  
    @Override  
    public int compare(Integer i1, Integer i2) {  
        if (i1==i2) {  
             return 0  
}  
}  
}` 

错了！ 

可以用以下四种方案：

1. return i1.compareTo\(i2\); //调用了Integer.compare\(i1,i2\);
2. i1.equals\(i2\)
3. i1.intValue\(\)==i2.intValue\(\)
4. 改function里面的，不比较 直接在if else外面 return 0

但是不要i1-i2 因为会overflow

## Strings

* strings are objects
* 在strings class里，有value, offset, 还有count
* `char[] array = {'a','b','c','d'}; offset = 1; count = 2; //array(1,2), bc`

### 又一次常量池

`String s1 = "abc";       
String s2 = "abc";  
System.out.println(s1==s2);    
//又是True`   

所有hard code的string都在同一个常量池里，就认为是同一个；别的，比如string builder弄出来的，或者new出来的，就变成了False

所以，一定要用equals\(\)，因为这是strings override的equals，它先看长度是否一样，然后一个个比值，这样得到的等不等是consistent的

`String sa = "a";       
String sb = "b";  
String sab = "a" + "b";     //compile time concat "ab"`

`sab == "a" + "b";   //true     
sab == sa + "b";    //false run time concat  
sab = sa + sb;     //false run time concat`

### Constructor 

* String\(\)
* String\(String value\)
* String\(char\[\] array\)
* String\(char\[\] array, int offset, int length\)
* String\(StringBuilder builder\)

### 各种size或者length

* int\[\].length
* string.length\(\)
* string builder.size\(\)

### Concatenate Strings

避免使用+或者concat，因为string是immutable的，所以每次的concatenate是很贵的，比如s每个都是m，那么s1+s2+s3+...sn 的时间复杂度 O\(n^2\*m\)，空间复杂度 如果GC不是立刻发生的就是O\(n^2\*m\) 所以要用string builder或者char array，这样即使算上扩容，amortized time complexity就是O\(m\*n\)。

### Number String转化

#### Primitive Type

`int i = 0;`

1. String si = i + ‘’  //'0'
2. 
#### Wrapper Class

### Chars and Substrings

* `String substring(int beginIndex, int endIndex)` '\[\)'
* `String substring(int beginIndex)`

#### 和offset count的关系

在Java 7u6之前，用它，优点是省空间，缺点是费空间

想象 假如这个时候s1没了，这个heap上的空间会一直在

![](../.gitbook/assets/image%20%287%29.png)

![](../.gitbook/assets/image%20%284%29.png)

![](../.gitbook/assets/image%20%2873%29%20%281%29.png)

### API

![](../.gitbook/assets/image%20%2884%29.png)

![](../.gitbook/assets/image%20%2848%29.png)

### StringBuilder, StringBuffer

> stringbuffer是老的，就像是hashtable和hashmap的区别

* append\(\)

![](../.gitbook/assets/image%20%286%29.png)

