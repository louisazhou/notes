---
description: 时间复杂度、空间复杂度
---

# Fundamental Knowledge

## How to judge whether your program is good or not

就像是在奥运会上的体操，不是完成所有动作就有满分；评委是根据动作的好坏来评分的。在计算机里，就是用Big O notation来衡量时间和空间复杂度。  
Big O notation是一个upper bound，也就是worst case scenario.   
Theta n 是tight bound，  
Omega n是lower bound.   
但是在通常情况下，更在乎的是worst case scenario，这是因为：搜索算法有很多剪枝，没有tight bound；或者工业界只在意在顶峰时刻的复杂度，比如春运购票或者双十一期间支付宝的稳定性.

### Time Complexity 

比较算法时 

idea 1: Wall Time 

* 会受到import size的影响
* 会受到机器本身的影响

不够好

idea 2：CPU cycle （number of instructions the CPU needs to operate\)

* Relation between CPU cycle and input size: number of CPU cycles = constant \* size of input
* Big O notation: a way to describe the runtime or memory space requirement

  of an algorithm as input size changes worst case scenario, computation time.

这里有一个amortized time vs average time的概念，容易混淆

amortized time：average of a **sequence** of **operations**. 

average time: the average of **all possible inputs** in the input space 

Fibonacci 数列的时间复杂度是O\( $$2^{n}$$ \)

![](../.gitbook/assets/image%20%2868%29.png)

![](../.gitbook/assets/image%20%2847%29.png)

### Space Complexity 

虽然在定义中有两种，Space Complexity和Auxiliary space complexity, 但是大家更在乎后者.   
Auxiliary space complexity: 输入输出除外，为了实现这个算法所带来的空间消耗



## 基础知识

### 字符表示

1 Byte=8bits  
1 KB=1024 Byte

字母表和各种符号总共一百来个，所以如果使用二进制，需要2^7=128.

中文的字符有几万个，于是需要更多的位数，用Unicode，有10、16、20、32位。之所以会有乱码，就是因为大家Unicode的解码方式不同，导致很多汉字无法支持。

但是它存在的问题是不管什么字符、使用频率多高多低，都耗一样多的空间；于是就有了新的解决方案，UTF-8 它现在是可变长度。只有在byte不够的时候才会用更多byte

![](../.gitbook/assets/image%20%2882%29.png)

如果7位就够，就是1个byte，能用7bits

### FLAG算法题考点（SDE）：一个UTF8转成一个十进制

第一个byte是0开头，所以它就是一个字；第二个里能看到1110所以是3个bytes拼接在一起的

### 面试：如何表示负数

'Two's Complement' 是为了表示negative number而发明的 

正数取反+1，这是倒推的结果：因为我们希望1+（-1）=0. 因为只支持8bit，进位后计算机不认第九位进位后的结果，于是结果就对了，等于零。 

二进制的最高位是1就是负数。

### 面试：1G能否放下100万个URL

URL的平均长度？ 1字符2byte， 200chars所以400bytes  
100万=10^6 \*400~=10^3\*400kb =400MB&lt;1G

### 寄存器 register

寄存器与程序的交互最多，因为 保存运算数据、程序指令、地址、CPU状态

### 缓存 Cache

CPU存L1 cache, L2 cache   
内存RAM cache了本地数据  
提高分布式系统的速度，server也做cache  
浏览器会将访问域名服务器DNS得到的数据cache

### 面试热门：什么是TCP什么是UDP

TCP考点：握手、挥手

握手：客户端和服务器端建立连接，客户发一个请求给server

客户的request，服务器的回复，双方都收到了

![](../.gitbook/assets/image%20%2820%29.png)

  
挥手：

![](../.gitbook/assets/image%20%2856%29.png)

UDP不会提前确认，直接传输它想要传输的数据。所以TCP更可靠，UDP不那么可靠。TCP是有序的，有一个sequence number，知道每个信息排在第几位。TCP可以传输大量数据，UDP只能传少量数据；TCP慢，UDP快。



## Data Structure

Wikipedia: a particular way of organizing data in a computer so that it can be used efficiently. 其实就是存放数据的方式，就像是生活中用五斗柜和抽屉柜来存放衣服；Data structure就是

