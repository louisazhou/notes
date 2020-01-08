# Recursion II and Memoization

{% embed url="https://cloud.tencent.com/developer/article/1086657" %}

{% embed url="https://phoenixzhao.github.io/一些动态规划的题目整理/" %}

{% embed url="https://segmentfault.com/a/1190000006672807" %}

{% embed url="https://blog.csdn.net/Dr\_Unknown/article/details/51939833" %}

{% embed url="https://blog.csdn.net/linhuanmars/article/details/38468361" %}

{% embed url="https://juejin.im/post/5d9ede57518825358b221349" %}



{% embed url="https://leetcode.com/problems/race-car/discuss/124326/summary-of-the-bfs-and-dp-solutions-with-intuitive-explanation" %}

### 从backtracking说起

1. 用aeiou生成string，但是有限制, 生成所有的permutation结果。

![](../.gitbook/assets/image%20%2827%29.png)

这是一个经典的多阶段决策问题

```python
followers={'a':['e'], 'e':['a','i'],'i':['a','o','u','e'],'o':['i','u'],'u':['a']}

def bt(perms, perm, n, followers):
    
    if len(perm)==n:
        perms.append(''.join(perm))
        return
    
    for char in followers[perm[-1]]:
        perm.append(char)
        bt(perms, perm, n, followers)
        perm.pop()

perms=[]
perm=['']
bt(perms, perm, n, followers)
```

但是假如题目只需要输出总共多少个结果的话，那bt就太浪费了，因为这就是一个 $$4^n$$ 的时间复杂度。

### 这时候有了memoization

因为我们需要做的其实是一个并集操作，找到集合里面的元素，加起来。  
a\*\*\* ****= ae \*\*  
  
f\(n\)=f\(n-1, with 'a' as the first char\) + f\(n-1, with 'e'  as the first char\)+...  
f\(n-1, with 'a' as the first char\)=f\(n-2, with 'e' as the first char\)  
  
if f\(n,c\): the total number of strings of length n that satisfy our constraints above and the first character must be c. 

f\(n,'a'\)=f\(n-1, 'e'\)  
f\(n,'e'\)=f\(n-1, 'a'\)+f\(n-1,'i'\)  
f\(n,'i'\)=f\(n-1,'a'\)+f\(n-1,'o'\)+f\(n-1,'e'\)+f\(n-1,'u'\)  
...  
Base Case: f\(1,c\)=1

```python
followers={'a':['e'], 'e':['a','i'],'i':['a','o','u','e'],'o':['i','u'],'u':['a']}
def f(n, c, followers):
    if n==1:
        return 1
    return sum([f(n-1,p) for p in followers[c]])
```

就像是fibo\(n\)=fibo\(n-1\)+fibo\(n-2\) 这道题里也有很多的重复运算，所以可使用python的decorator，只需要在代码里加一行，不需要修改本身的逻辑结构（比如手动加一个dict），就可以实现memoization，也就是recursion without repetition，传说中的DP。

### Python Decorator

2. Wildcard， regex   
`*`可以匹配0个或多个任意字符 不会单独存在，前面必须有\[a-z\]或者.  
`.` 可以匹配1个字符  
return 如果两个char分别s（chars）和p（matchers）的话，是否能匹配上（boolean）。  
所以所有情况的可能是a-z, \[a-z\]\*, '.', '.\*'.

s\[0\], s\[1..n\]    
p\[0,1\], p\[2...n\]   
case 1： \[a-z\]\* or .\*  
                 Enumerate over all the possible matching length of this descriptor   
                 p\[2:\]试着match0个或者1个，2个...s\[0:l\]   
                 如果match0个，isMatch\(s, p\[2:\]\)  
                 如果不能match0个，就接着match1个，isMatch\(s\[1:\], p\[2:\]\)  
                 接着试着match2个，isMatch\(s\[2:\], p\[2:\]\)  
  ...  
以上都是说 s\[0:l\]包含和p\[0\] 一样的character。.也可以认为是一样的，因为它可以match任何字符。

case2：\[a-z\] or .  
                isMatch\(s\[1:\], p\[1:\]\)

{% code title="加个decorator" %}
```python
from functools import lru_cache
class Solution:
    @lru_cache(maxsize=None)
    def isMatch(self, s,p):
        if not p:
            return not s  #当且仅当用完p且s也用完了才match
        if len(p)>1 and p[1]=='*':
            if self.isMatch(s,p[2:]): #匹配0个
                return True
            l=1
            while l<=len(s) and (p[0]=='.' or s[l-1]==p[0]): #匹配多个
                if self.isMatch(s[1:],p[2:]):
                    return True
                l+=1
            return False
        if s and (p[0]=='.' or s[0]==p[0]): # case 2
            return self.isMatch(s[1:],p[1:])
        return False
```
{% endcode %}

{% code title="如果手写一个decorator" %}
```python
def memo(func): #decorator accepts a function and returns a new function
    cache={}
    def new_func(*args): #the newfucntion accepts any args (0 or 1 or many)
        if args not in cache: # if not in 
            cache[args]=func(*args) # execute the function the user defined
        return cache[args] # if exists, return the results
    return new_func
    
class Solution:
    @memo
    def isMatch(self, s,p):
        if not p:
            return not s  #当且仅当用完p且s也用完了才match
        if len(p)>1 and p[1]=='*':
            if self.isMatch(s,p[2:]):
                return True
            l=1
            while l<=len(s) and (p[0]=='.' or s[l-1]==p[0]):
                if self.isMatch(s[1:],p[2:]):
                    return True
                l+=1
            return False
        if s and (p[0]=='.' or s[0]==p[0]):
            return self.isMatch(s[1:],p[1:])
        return False
```
{% endcode %}

