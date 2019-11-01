# Recursion II and Memoization

{% embed url="https://leetcode.com/problems/race-car/discuss/124326/summary-of-the-bfs-and-dp-solutions-with-intuitive-explanation" %}



1. 用aeiou生成string，但是有限制, 生成所有的permutation结果。

这是一个经典的多阶段决策问题

```python
def bt(perms, perm, n, followers):
    if len(perm)==n:
        perms.append(''.join(perm))
        bt()
```

但是假如题目只需要输出总共多少个结果的话，那bt就太浪费了，因为这就是一个 $$4^n$$ 的时间复杂度。

2. 所以这时候有了memoization。

因为我们需要做的其实是一个并集操作，找到集合里面的元素，加起来。  
a\*\*\* ****= ae \*\*  
  
f\(n\)=f\(n-1, with 'a' as the first char\) + f\(n-1, with 'e'  as the first char\)+...  
f\(n-1, with 'a' as the first char\)=f\(n-1, with 'e' as the first char\)  
  
if f\(n,c\): the total number of strings of length n that satisfy our constraints above and the first character must be c. 

3. Wildcard    
\* 可以匹配0个或多个任意字符，. 可以匹配1个字符

s: 

