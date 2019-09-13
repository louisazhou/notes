---
description: 时间复杂度、空间复杂度
---

# Fundamental Knowledge

## Time Complexity 

比较算法时 

idea 1: Wall Time 

* 会受到import size的影响
* 会受到机器本身的影响

不够好

idea 2：CPU cycle （number of instructions the CPU needs to operate\)

* Relation between CPU cycle and input size: number of CPU cycles = constant \* size of input
* Big O notation: a way to describe the runtime or memory space requirement

  of an algorithm as input size changes worst case scenario, computation time.

Fibonacci 数列的时间复杂度是O\( $$2^{n}$$ \)

![](../.gitbook/assets/image%20%2813%29.png)

![](../.gitbook/assets/image%20%2810%29.png)

## Space Complexity 

输入输出除外，为了实现这个算法所带来的空间消耗

