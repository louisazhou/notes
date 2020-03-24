# Interface, Abstract Class, Access Modifier, Exceptions

## Inheritance

### override 父类子类之间

一个类extend另一个类，subclass, child class, derived class, extended class 与之相对的是 superclass/base class/parent class。

父类的private field是无法被child class直接看到的；能调用哪个method是用声明的类型来决定的，所以如果是父类声明，无法调用只属于子类的method。而在运行期间才会在实现里看method，如果实现类有或者有override，那么就override了父类；如果没有，就用父类的。这个现象叫多态。

1. 哪些函数能够调用取决于reference的类型  ---compile time
2. 被override的函数调用哪个版本，取决于object的类型 ---runtime

是runtime决定，这是因为instance是计算出来的。

@override是好习惯

### overload 同一类里

两个及两个以上的名字相同，signature其它部分都相同，但是input parameter list不同。overload在compile time就可以决定，因为看field是否match就行。

1. 先看reference里有没有对应的方法--compile time - 如果有，暂时决定调用这个方法 - 如果没有，看看用cast能否找到对应的方法（cast到最近可用的类型；尽量cast成parent而不是grandparent）如果有，那么compile通过，暂时决定调用这个方法
2. 在run time中，真正调用的是instance实现类的对应方法（包括从父类继承来的方法）如果instance类中override了之前在compile时暂时决定调用的方法，则真正调用的是这个override后的方法。

## Interface vs Abstract Class

### Abstract Class

抽象类：有一个或多个abstract method，abstract method没有{}, abstract class和abstract method都需要有关键字abstract, 比如 `public abstract class Figure` 

abstract class是能写代码的PM，因为里面可能会有实现了的method。如果继承过来一个abstract class，就要实现abstract class里面的所有method，而被abstract class实现了的也可以自己override，不过这里其实不用写override，因为是在实现。interface和abstract class都不能直接new出来。

### Interface

与abstract class的不同

1. Java不支持多继承class，会有diamond problem；但是继承interface的类不会有这个问题，因为这都是自己来实现的，不会出现歧义。换句话说doesn't allow multiple extends, but can implements multiple interfaces. 
2. abstract class有抽象类method也可以有实现了的；而interface只有abstract method而且所有method都必须public，毕竟是要等着别人来实现。abstract class可以有constructor，但是interface没有constructor，不过可以有public fields，只不过没啥用罢了。
3. extend class: is a; implement interface: has an ability \(is a figure; has an ability to bark\). 

### Interview Q：什么时候用Abstract Class vs Interface

1. 有函数实现公用，又不想给每个函数类都写一遍，就用abstract class，比如figure的例子
2. 有非public的method，想让它的封装性更好（private，protected，default）也用abstract class，因为interface只有public
3. 如果未来还想给它加一些method，也是用Abstract Class，这样子类也不用改。不然用interface，以后都得改。 
4. API很固定了，不可能再改了，就可以用一个interface。interface一般比较单一，因为可以多继承interface，不影响调用者继承别的类。

## Access Modifier

![](../.gitbook/assets/image%20%2883%29.png)

### Singleton

这个类只能有一个instance，把constructor做成private，就只能自己new。还需要是static，因为如果不是static，就得用instance调用了。还不能让人改，所以还需要final。

1. constructor 是private
2. 自己new的是这个类型，`private static final` 

应用：回收站 

## Exception

![](../.gitbook/assets/image%20%2886%29.png)

图中红色的是checked exception，要求一定要handle；蓝色叫unchecked exception，不一定要handle。如果想写一个不需要处理的exception，可以写一个`class myException extends runTimeException`

### 背锅 try catch 

```text
try {

} catch () {

} finally {

}
```

### 甩锅 throws

扔给caller，一直往上扔，直到main函数

### finally

finally除了断电、中毒... 都要执行，然后再执行其他method。“释放资源”

## Throw vs. Throws

> throw 是 走你 是祈使句；throws 是 我随时有可能会扔锅哦 陈述句，是一种状态

### Throw 

主动扔，throw instance. instance是个throwable或者throwable子类的object。意思是“显性扔出异常”。

`class myException extends Exception`

如果throw到了main函数都无法handle，就会在command window报错了。

### Throws

> throws is used in the head of method to indicate that this method might throw one of the listed type exceptions.

caller**必须**handle checked exception

`type method_name(parameters) throws exception_list`



