# Bit Operations

## Bit Representation

在C++ int/Java int, 是signed 32-bit，最高位是符号位，所以最大的int是2^31-1=2147483647. 负数的表示是这个数字的正数的二进制表示取反然后+1，这样的好处是在计算机内部不需要cast sign。

## Bit Operation

### & bitwise AND\(&&\)

* 逻辑短路是&&或者AND 
* &是bitwise operation，所有数和0&都是0，和1&都是自己

### \| bitwise OR \|\|

* 逻辑短路是\|\|或者OR
* \|是bitwise operation，所有数和1\|都是1，和0\|都是自己

### ~ NOT

* 负数的取反加1的“取反”就是这个~ tilde
* -a == \(~a\)+1 if a&gt;=0

### ^ XOR

* 0^0=0; 1^1=0
* 0^1=1; 1^0=1
* 交换律 a XOR b = b XOR a
* 结合律 a XOR \(b XOR c\) = \(a XOR b\) XOR c
* 0是单元 0 XOR a = a
* a XOR a =0

![](../.gitbook/assets/image%20%2858%29.png)

### &lt;&lt; 左移     右侧补充零

### &gt;&gt; 右移     左侧补充符号位

* -1和0不管右移多少位还是原数

左移和右移不改变原来的值

### &gt;&gt;&gt; 左侧补充0， 叫做unsigned right shift 

## Practical Use Cases

### Compression 

空间上的压缩：比如 浏览器版本的支持，用一个int表示32个支持或不支持；从右往左，位数依次表示 是否支持当前版本 一个int，4 byte就够

时间上的提升： 用位操作 32个操作变1个操作，32倍的提升

> 这些提升都是常数倍的 所以不可能是O\(nlogn\)到O\(n\)的 但是也挺大的提升了

## Building Blocks

### Bit tester: Given a number, whether its kth bit is 1

比如，想要知道第k位是否是1，从右到左从0开始数

* x&\(1&lt;&lt;k\)==0 or 8
* \(x&gt;&gt;k\)&1==0 or 1  \(preferable\)

### Bit setter: Set kth bit to 1, and the rest stays the same

* x = x \| \(1 &lt;&lt; k\)    任何位or0都是自己； or1都是1

### Bit setter: Set kth bit to 0, and the rest stays the same

* x = x & \(~\(1&lt;&lt;k\) \) 任何位&1都是自己 &0都是0

## Problems

### Determine whether a number x is a power of 2 \(2^n, n&gt;=0\)

> 1. x&gt;0 必须满足
> 2. x的二进制表示只有一个1

要clarify: 是一个整数

```python
count = 0
for i in range 32:
    count += (x>>i)&1
return x>0 and count==1
```

如果x是2的幂，and了x-1后一定不会有重叠的1位，所以&之后一定是0  
如果x不是2的幂, and了x-1后只会消掉一个1，所以&后一定不可能为0

{% code title="奇技淫巧" %}
```python
x>0 and (x&(x-1)==0)
```
{% endcode %}

### Determine the \# of bits are different between 2 positive integers

xor 然后取多少个1

### 如何数1: Hamming Weight

### What happens if we assign a negative number to an unsigned integer

int: 32, signed;  
long: 64, signed;  
short: 16, signed;  
char: 16, unsigned;  
byte: 8, unsigned

所以下面的Java Code: 

```java
short a = -1        //0b 1111 1111 1111 1111
char b = (char) a   //0b 1111 1111 1111 1111
(int)b              //0b 0000 0000 0000 0000 1111 1111 1111 1111
(int)a      // -1
```

Principle 1: 当同位数的整形之间互相转化时，二进制的表示是不变的，变的是后续语句对这个数的理解

Principle 2: extend时，如果原先是有符号数，补充符号位，如果是无符号数，补充0

Principle 3: 长变短时，直接truncate前面多余的位数

### Determine whether a string contains unique characters

xor每一个char不对，因为

a-&gt; 65, aa--&gt;0, aaa-&gt;65 扎心了

Assume: 只有a-z的字母，那就可以用一个int，32位的前26位来map它们和字母a-z的关系

```java
int bitMap = 0;
for (int i=0; i<a.length();i++) {
    int k=a.charAt(i)-'a';
    int bitPos = (1 << idx);
    if (bitMap & bitPos != 0) {
        return false;
    } else {
        bitMap |= bitPos;
    }
}
return true;
```

如果是ASCII，256个，就一定是0~255的这个有序区间里。需要8个integer来表示，比如'b'=98，b%32=2，b/32=3，说明在3bucket里的第二个从右往左2位（都是从0开始）

```java
int[] bitMap = new int[8];
for (int i=0; i<a.length();i++) {
    int idx=a.charAt(i);
    int row = idx/32;
    int col = idx%32;
    int bitPos = (1 << col); //x&(1<<k) or (x>>k)&1
    if (bitMap[row] & bitPos != 0) {
        return false;
    } else {
        bitMap[row] |= bitPos;
    }
}
return true;
```

### How to reverse all bits of a number 

#### 用2 pointers，reverse string的方法

记得swap后赋值，因为int是primitive type 

swap里的操作，交换，如果

XOR 1就是取反

### 10进制Number to 16进制

其实是0b到0X因为计算机里都是2进制，所以可以每四组一起，8421一个个抠出来；

怎么扣？\(&gt;&gt;28\)&0xF \(&gt;&gt;24\)&0xF

```java
public String toHex(int a) {
    char[] result = new char[8];
    

}
```

#### 

