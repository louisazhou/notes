# Dynamic Programming

## Memoization

### 普通的Fibo

```java
public int fibN(int n) {
    if (n==0||n==1) {
        return n;
    }
    return fibN(n-1)+fibN(n-2);
}
```

### 优化重复计算Fibo

```java
int[] memo; //or HashMap, <key = Integer (n), value = Integer(Fib(n))>

public int fibN(int n) {
    if (n==0||n==1) {
        return n;
    }
    
    if (memo[n]!=0) {
        return memo[n];
    }
    
    int result= fibN(n-1)+fibN(n-2);
    memo[n] = result;
    return result;
}
```

Time = O\(n\)  
Extra Space = O\(n\)

### 从小到大填表

> DP的解题方式就是如何定义表格里每个element的意义，以及把表格里的value填满

```java
public int fibN(int n) {
    int[] fibsFound = new int[n+1];
    fibsFound[0] = 0;
    fibsFound[1] = 1;
    for (int i=2; i<=n; i++) {
        fibsFound[i] = fibsFound[i-1] + fibsFound[i-2];
    }
    return fibsFound[n];
}
```

从小到大记录subsolution

* base case
* induction rule

## 一维原始数据，求最大、最小

> linear scan and look back to the previous elements

### Longest Ascending Subarray

Given an unsorted array, find the length of the longest subarray in which the numbers are in ascending order. 

区别sub-array vs sub-sequence: 

* sub-array: contiguous elements in an array
* sub-sequence: not necessarily contiguous \(can jump\)

memo\[i\] 的物理意义: within the range from the 0-th element to the i-th element, the maximum length of the ascending subarray, including the i-th element

```java
index = 0 1 2 3 4 5 6 7
input = 7 2 3 1 5 8 9 6
M[]   = 1 1 2 1 2 3 4 1
globalMax = 4
```

Base Case: memo\[0\] = 1   
Induction Rule: within the range from the 0-th element to the i-th element, the maximum length of the ascending subarray, including the i-th element  
M\[i\] = M\[i-1\]+1  if input\[i-1\]&lt;input\[i\]  
1                          otherwise

Time = O\(n\)  
Space = O\(n\)

#### 优化空间复杂度O\(1\)

只存一个元素，previous element

### Maximal Product When Cutting Rope

Given a rope with integer-length n, how to cut the rope into m integer-length parts with length p\[0\], p\[1\]...p\[m-1\], in order to get the maximal product of p\[0\]\*p\[1\]\*p\[2\]...p\[m-1\], m is determined by you and must be greater than 0 \(at least one cut must be made\).

#### Solution 1: DFS \(recursion\)

先考虑最右边的第一刀cut在哪里，然后再往左切

Time: O\(n!\)

#### Solution 2: DP 左大段+右大段

Base Case：  
size=1  M\[1\] = invalid       //max\(maxProd\(1\), 1\)  
Induction Rule:   
M\[i\]的物理意义：i-m rope，cut at least once, what's the maximum product  
size = 2:  __there's only 1 way to cut this rope; its M\[2\] = max \(M\[1\], 1\) \* max \(M\[1\], 1\) = 1\*1 = 1  
size = 3:  there are two possible ways for the first cut  
                 case 1:  __- \| - - M\[3\] = max\(M\[1\],1\)\*max\(M\[2\],2\) = 1\*2 = 2  
                 case 2: - - \| -  M\[3\] = max\(M\[2\],2\)\*max\(M\[1\],1\) = 2  
                 M\[3\] = max\(case1, case2\) = 2

size = 4: there are 3 possible ways for the 1st cut  
                case 1: max\(M\[1\],1\)+max\(M\[3\],3\) = 1\*3 = 3  
                case 2: max\(M\[2\],2\)+max\(M\[2\],2\) = 2\*2 = 4  
                case 3: max\(M\[3\],3\)+max\(M\[1\],1\) = 1\*3 = 3  
                M\[4\] = max\(case1, case2, case3\) = 4

return M\[n\]

Time: O\(n^2\) 因为linear scan回头看所有元素，所以变成了n^2

#### Solution 2: DP 左大段+右小段

> 更general, 适用于最小可分元素是similar but not identical的情况

M\[5\] = case 1: max\(M\[4\],4\)\*1  
            case 2: max\(M\[3\],3\)\*2  
            case 3: max\(M\[2\],2\)\*3  
            case 4: max\(M\[1\],1\)\*4

### Dictionary Problem

> 和绳子的题很像。但是只有左大段可以从表格读出来，右小段只能通过查dict，题目已知条件，获得  
>   
> 大段：读表格，读M\[i\]的值获取solution；小段：manual操作   
>   
> 所以 左大段+右小段的思路更加general

### Jump Game

Given an array of non-negative integers, you are initially positioned at the first index of the array. Each element in the array represents your maximum jump length at that position. Determine if you can reach the last index. 

#### 从右往左做linear scan，从终点开始看，只要当前落脚点能True

Base Case: M\[length-1\] = True, because it's the target itself  
Induction Rule: M\[i\] 的物理意义: Whether I can jump from the current index to the target  
M\[i\] = true if 存在落脚点j，j在i的右侧，且不超过i&lt;j&lt;=min\(i+A\[i\], A.length-1\) 使得M\[j\]=True, or  I can jump directly from i-th index to the target  
Return: M\[0\]

M = O\(n^2\)

#### 从左到右linear scan

