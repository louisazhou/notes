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

```python
class Solution(object):
  def fibonacci(self, K):
    """
    input: int K
    return: long
    """
    # write your solution here
    if K<=0:
      return 0
    array = [0]*(K+1)
    array[1] = 1
    for i in range (2,K+1):
      array[i] = array[i-2]+array[i-1]
    return array[K]
```

### 



## 一维原始数据，求最大、最小

> linear scan and look back to the previous elements

### Longest Ascending Subarray

```python
class Solution(object):
  def longest(self, array):
    """
    input: int[] array
    return: int
    """
    # write your solution here
    if len(array)==0:
      return 0
    result, curr = 1, 1
    for i in range(1, len(array)):
      if array[i]>array[i-1]:
        curr+=1
        result = max(result, curr)
      else:
        curr=1
    return result
```

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

```python
class Solution(object):
  def maxProduct(self, length):
    """
    input: int length
    return: int
    """
    # write your solution here
    if length==2:
      return 1
    array = [0]*(length+1)
    array[2] = 1
    for i in range (3, len(array)):
      for j in range(1, i//2+1):
        array[i] = max(array[i], j*max(i-j, array[i-j]))
    return array[length]
```

### Dictionary Problem

> 和绳子的题很像。但是只有左大段可以从表格读出来，右小段只能通过题目已知条件获得  
>   
> 大段：读表格，读M\[i\]的值获取solution；小段：manual操作   
>   
> 所以 左大段+右小段的思路更加general

M\[i\]的物理意义 前i个字母是否能切成单词的concatenation: true or false

### Jump Game 1

Given an array of non-negative integers, you are initially positioned at the first index of the array. Each element in the array represents your maximum jump length at that position. Determine if you can reach the last index. 

#### 从右往左做linear scan，从终点开始看，只要当前落脚点能True

Base Case: M\[length-1\] = True, because it's the target itself  
Induction Rule: M\[i\] 的物理意义: Whether I can jump from the current index to the target  
M\[i\] = true if 存在落脚点j，j在i的右侧，且不超过i&lt;j&lt;=min\(i+A\[i\], A.length-1\) 使得M\[j\]=True, or  I can jump directly from i-th index to the target  
Return: M\[0\]

Time = O\(n^2\)

```python
#有点问题
class Solution(object):
  def canJump(self, array):
    """
    input: int[] array
    return: boolean
    """
    # write your solution here
    if len(array)==1:
      return True
    canJump = [False]*len(array)
    for i in range (len(array)-2, -1, -1):
      if i+array[i]>=len(array)-1:
        canJump[i] = True
      else:
        for j in range (array[i],0):
          if (canJump[j+i]):
            canJump[i] = True
            break
    return canJump[0]

```

#### 从左到右linear scan，从起点开始看

Base Case: M\[i\]的物理意义: whether I can jump from the start to the i-th index  
M\[0\] = true  
Induction Rule: M\[i\]=True if there exists a j where j&lt;i and M\[j\]==true, and j+A\[j\]&gt;=i  
Return: M\[length-1\]

Time = O\(n^2\)

### Jump Game 2

从a\[0\]到a\[n-1\]的最少步数

### Largest Sum of Subarray 

M\[i\] 物理意义 the sum of the largest sum subarray that ends at a\[i\] 

空间可以优化到O\(1\)，因为只在用最近的一个

#### followup：how to return the left-right border of the solution 

![](../.gitbook/assets/image%20%28101%29.png)

left: 达到M\[i\]时subarray的左边界\(闭区间\)的位置  
i: 达到M\[i\]时的右边界\(闭区间\)的位置

Initialize:  
M\[0\] = a\[0\]  
left = 0



### Maximum subarray sum in a circular array.

#### 问题描述

```text
Example 1:
Input: [1,-2,3,-2]
Output: 3
Explanation: Subarray [3] has maximum sum 3

Example 2:
Input: [5,-3,5]
Output: 10
Explanation: Subarray [5,5] has maximum sum 5 + 5 = 10

Example 3:
Input: [3,-1,2,-1]
Output: 4
Explanation: Subarray [2,-1,3] has maximum sum 2 + (-1) + 3 = 4

Example 4:
Input: [3,-2,2,-3]
Output: 3
Explanation: Subarray [3] and [3,-2,2] both have maximum sum 3

Example 5:
Input: [-2,-3,-1]
Output: -1
Explanation: Subarray [-1] has maximum sum -1
```

#### 解题思路

Case1: max subarray只出现在array中间的一段，没有从最末连接到开头：直接求max subarray sum。 Case2: max subarray需要从最末连接到开头： 1. 求array中间出现的一段min subarray sum. 2. 用array所有元素的总的sum来减去中间出现的这一段min subarray sum，就是结果。 Return max\(case1, case2\)

此外要额外考虑的情况：如果所有array内的所有元素都是负数，则不需要考虑case2，直接返回case1的结果。

#### 复杂度分析

* 时间 `O(n)`
* 空间 `O(1)`

#### Proposed Code

```java
class Solution {
    public int maxSubarraySumCircular(int[] A) {
        if (A == null || A.length == 0)
            return Integer.MIN_VALUE;

        if (A.length == 1)
            return A[0];

        boolean allNegative = true;
        int total = 0;
        // check whether all numbers are negative and calculate total sum
        for (int num : A) {
            if (num >= 0)
                allNegative = false;

            total += num;
        }
        // case 1
        int sum1 = getMaxOrMinSubarraySum(A, true);
        // case 2 - skip if all numbers are negative
        int sum2 = allNegative ? Integer.MIN_VALUE : total - getMaxOrMinSubarraySum(A, false);
        return Math.max(sum1, sum2);
    }
    private int getMaxOrMinSubarraySum(int[] A, boolean isMax) {
        int result = isMax ? Integer.MIN_VALUE : Integer.MAX_VALUE;
        int cur = A[0];
        for (int i = 1; i < A.length; i++) {
            cur = isMax ? Math.max(A[i], A[i] + cur) : Math.min(A[i], A[i] + cur);
            result = isMax ? Math.max(result, cur) : Math.min(result, cur);
        }
        return result;
    }
}
```



### Unsorted Array 最长连续1

### 十字架

> 如果是brute force 需要n^3
>
> 这不是二维DP，而是四个方向的一维DP

Step1: 从左到右，从右到左，从上到下，从下到上，分别做“最长连续1” O\(4n^2\)

Step2: 四个方向的结果在当前取一个min O\(n^2\)

return global max among all M\[i\]\[j\]

### X

变成斜着做

### 空心1

### 火柴

每个顶点只考虑两个方向，右和下

可以两种表示，要么就tuple，要么就2-bit

### 任意subarray sum

#### prefix sum+ 1D

M\[i\] represents the sum from index 0 to index i 

sum\[i...j\] = M\[j\] = M\[i-1\] 但是容易出界 更好的是M\[j\]-M\[i\]+input\[i\]

Time = O\(n\)  
Extra Space = O\(n\)  
Query Time = O\(1\)

### sum最大的长方形

#### Prefix Sum 1D

#### Prefix Sum 2D

![](../.gitbook/assets/image%20%2873%29.png)

蓝-绿-红+橙





## 二维

### Edit Distance

### Largest Square of 1's in a Binary Matrix 

M\[i\]\[j\] 物理意义 以a\[i\]\[j\]为右下角的最大全1正方形的边长



