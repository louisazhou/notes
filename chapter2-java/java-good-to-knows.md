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

![](../.gitbook/assets/image%20%2842%29.png)

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

