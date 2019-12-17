# Java Good to Knows

## Pass by Value 

* **Pass by Value**: The method parameter values are copied to another variable and then the copied object is passed, that’s why it’s called pass by value.
* ~~**Pass by Reference**: An alias or reference to the actual parameter is passed to the method, that’s why it’s called pass by reference.~~

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

local variable: stack

field: heap

> 小技巧：看到赋值时，想：等号左边的内容是stack还是heap，以此思考这个reference到底是什么

### constructor: 

1. 没有返回值类型
2. 名字和类名一模一样（包括大小写）
3. 只有在field的名字和local variable相同时用this.指代当前instance自己

### Coding Style

{% embed url="https://google.github.io/styleguide/javaguide.html" %}

class：HelloWorld,   
method/field name: main\(\), myName 如果有两个单词，使用camelCase，命名规范不用\_





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

