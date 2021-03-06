# Bit Operations

## Bit Representation

在C++ int/Java int, 是signed 32-bit，最高位是符号位，所以最大的int是2^31-1=2147483647. 

负数的表示是这个数字的正数的二进制表示取反然后+1\(complement\)，这样的好处是在计算机内部不需要cast sign。

|  | 原码 | 反码 | 补码 |
| :--- | :--- | :--- | :--- |
| 0 | 0000 | 0000 | 0000 |
| 1 | 0001 | 0001 | 0001 |
| -1 | 1001 | 1110 | 1111 |

|  | min | max | count |
| :--- | :--- | :--- | :--- |
| 原码 | 1111\(-7\) | 0111\(7\) | 15 |
| 反码 | 1000\(-7\) | 0111\(7\) | 15 |
| 补码 | 1000\(-8\) | 0111\(7\) | 16 |

![](../.gitbook/assets/image%20%2861%29.png)

如果只有4位，-8没有原码，所以没办法从补码推回原码。

> Integer.MIN\_VALUE-1会变成Integer.MAX\_VALUE

> Integer.MAX\_VALUE+1会变成Integer.MIN\_VALUE-1

### 10转2进制

正数:除2取余，直到为0，倒着读

### 2转10进制

正数:乘2的次幂加起来

### ASCII

用一个byte的7位，表示了2^7=128种状态

* a map of small numbers to characters
* 32~126 are printable; others are not printable
* 0~9, 'A'-'Z', 'a'-'z'都是连续的，所以可以通过这种方式求出它们和0、A或者a的距离

### Unicode

超级版ASCII，在ASCII的基础上扩充字符集合，在Java中的char就是Unicode。表示范围是2^16-1  
0~65535 前256位是ASCII 

在本质上，不管是Unicode还是ASCII都是character set; 在之后再经历character encoding，UTF-8, UTF-16.

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

* 对正数来说，只要不溢出，左移一位相当于\*2

### &gt;&gt; 右移 算术右移     左侧补充符号位

* -1和0不管右移多少位还是原数
* -8右移1位可以是-4 
* -7右移1位也是-4 \(所以右移一位不是严格的/2 只是比较接近/2\)

左移和右移不改变原来的值

### &gt;&gt;&gt; 右移 逻辑右移     左侧补充0， 叫做unsigned right shift 

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

```python
class Solution(object):
  def isPowerOfTwo(self, number):
    """
    input: int number
    return: boolean
    """
    # write your solution here
    if number<=0:
      return False
    while (number&1 ==0):
      number = self.rshift(number,1)
    return number == 1

  def rshift(self, val, n): 
    return (val & 0X7FFFFFFF) >> n
```

### Determine the \# of bits are different between 2 positive integers

xor 然后取多少个1

```python
class Solution(object):
  def diffBits(self, a, b):
    """
    input: int a, int b
    return: int
    """
    # write your solution here
    a ^= b
    count = 0
    while a != 0:
      count += a & 1
      a = self.rshift(a, 1)
    return count
  def rshift(self, val, n): 
    return (val & 0X7FFFFFFF) >> n
```

### 如何数1: Hamming Weight

### What happens if we assign a negative number to an unsigned integer

int: 32, signed;  
long: 64, signed;  
short: 16, signed;  
char: 16, unsigned;  
byte: 8, unsigned         -2^7, 2^7-1 256个

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

#### hashset size=26

#### boolean array, size =26

#### bitmap

Assume: 只有a-z的字母，那就可以用一个int，32位的前26位来map它们和字母a-z的关系

```java
int bitMap = 0;
for (int i=0; i<a.length();i++) {
    int k=a.charAt(i)-'a'; //减，因为算的是相对距离
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

```python
class Solution(object):
  def allUnique(self, word):
    """
    input: string word
    return: boolean
    """
    # write your solution here
    bitmap = [0]*8
    for char in word:
      idx = ord(char)
      row = idx//32
      col = idx%32
      bitpos = (1<<col)
      if bitmap[row]&bitpos!=0:
        return False
      else:
        bitmap[row]|=bitpos
    return True
```

```java
int[] bitMap = new int[8];
for (int i=0; i<a.length();i++) {
    int idx=a.charAt(i); //不需要减‘a’ 因为我们是针对整个ASCII表里本身的真实位置
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

```python
class Solution(object):
  def reverseBits(self, n):
    """
    input: long n
    return: long
    """
    # write your solution here
    for shift in range (16):
      right = (n >> shift) &1
      left = (n >> (31-shift)) &1
      if left!=right:
        n ^= (1 <<shift)
        n ^= (1 <<(31-shift))
    return n
```

### 10进制Number to 16进制

其实是0b到0X因为计算机里都是2进制，所以可以每四组一起，8421一个个抠出来；

怎么扣？\(&gt;&gt;28\)&0xF \(&gt;&gt;24\)&0xF

```python
#这个有点问题
class Solution(object):
  def hex(self, number):
    """
    input: int number
    return: string
    """
    # write your solution here
    result = []
    if number==0:
      return '0x0'
    while number>0:
      res = number%16
      if res<10:
        result.append(chr(res))
      else:
        result.append(chr(res-10+ord('A')))
      number = self.rshift(number, 4)
    result.append('x')
    result.append('0')
    return ''.join(result[::-1])
  
  def rshift(self, val, n): 
    return (val & 0X7FFFFFFF) >> n
```

### 实现Integer.parseint这个method

‘1912’ --&gt; ‘1’ ‘9’ ‘1’ ‘2’ --&gt;1 9 1 2 --&gt;1912

反过来 1912--&gt;--&gt;1 9 1 2 ‘1’ ‘9’ ‘1’ ‘2’--&gt;‘1912’ 这里有长到短的强制转化

### Convert 'd'--&gt;'D'

它-'a'+'A'

### Char to Hex digit

* 如果在0~9之间，-'0';
* 如果在'a'~'f'之间，-‘a’+10
* 如果在'A'~'F'之间，-‘A’+10



