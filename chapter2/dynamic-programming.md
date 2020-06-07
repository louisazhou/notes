# Recursion II and Memoization

## Recursion + 二维数组的结合

### N Queens

* 每一行有且仅有一个皇后
* 用一维数组 col\[8\], col\[i\]的意义：在第i行的皇后被放在了第几列
* 八层，每层决定一个皇后的列号，8^8
* 不需要吐，因为0~currentRol-1都是决定了的
* 时间复杂度：O\(n!\*n\) 每个节点都要for循环一下，然后最底下一层还要print也是O\(n\)
* 空间复杂度：O\(n\)

### 2D Spiral Print

* 子问题: 从N\*N到\(N-2\)\*\(N-2\)的螺旋打印 剥洋葱
* 如果是奇数开始，5-3-1；如果是偶数开始，6-4-2-0
* 记录start x、start y\(其实一样\)，因为第一次在\[0\]\[0\]第二次就在\[1\]\[1\]
* recursion tree只有一个叉
* 打印的时候左闭右开，第一行的最后一列的那个值留给列来填，这样就可以不重不漏
* 时间复杂度：O\(n^2\)    4n，4\(n-2\), 4\(n-4\), 有n/2层 
* 空间复杂度：O\(n/2\)

```python
class Solution(object):
  def spiral(self, matrix):
    """
    input: int[][] matrix
    return: Integer[]
    """
    # write your solution here
    result = []
    self.helper(matrix, 0, len(matrix), result)
    return result

  def helper(self, matrix, offset, size, result):
    if size==0:
      return
    if size==1:
      result.append(matrix[offset][offset])
      return
    for i in range (0, size-1, 1):
      result.append(matrix[offset][offset+i])
    for i in range (0, size-1, 1):
      result.append(matrix[offset+i][offset+size-1])
    for i in range (size-1, 0, -1):
      result.append(matrix[offset+size-1][offset+i])
    for i in range (size-1, 0, -1):
      result.append(matrix[offset+i][offset])
    self.helper(matrix,offset+1,size-2,result)
```

## Recursion + LinkedList

### Reverse Linked List in Pairs

Reverse pairs of elements in a singly-linked list.

**Examples**

* L = null, after reverse is null
* L = 1 -&gt; null, after reverse is 1 -&gt; null
* L = 1 -&gt; 2 -&gt; null, after reverse is 2 -&gt; 1 -&gt; null
* L = 1 -&gt; 2 -&gt; 3 -&gt; null, after reverse is 2 -&gt; 1 -&gt; 3 -&gt; null

{% hint style="info" %}
只需要遵循和reverse linked list一样的逻辑，知道：  
1. 黑框框在了哪里：两个node之后的那些全部都是黑框  
2. 红色那件事：让N1指向后面的黑框  
3. 蓝色那件事：让N2指向N1  
4. 当前层做点什么事: 红色和蓝色两件事
{% endhint %}

```python
class Solution(object):
  def reverseInPairs(self, head):
    """
    input: ListNode head
    return: ListNode
    """
    # write your solution here
    if not head or not head.next:
      return head
    
    node1, node2 = head, head.next

    rest=self.reverseInPairs(node2.next)
    node1.next=rest
    node2.next=node1
    
    return node2
```

## Recursion + string

### reverse a string using recursion 

### 缩写

* text & pattern  从pattern第0个位置开始看这是啥 
* 子问题：字母还是数字 如果是数字，一直到pattern的下一个不是一个数字位置，然后在text上从头开始匹配
* 如果是字母，和text的下一个位置匹配
* 根: 完整的text和pattern的匹配，分叉：每一个element分一茬，单叉树，直到空串和空串匹配 如果是\[\]\[\]，匹配上了，如果是\[\]和一个有值得，没有匹配上
* base case: Both lengths are 0, match ; either one is 0, false
* recursion rule: case 1: pattern\[0\] is a digit: then find all subsequent and adjacent digits after pattern\[0\], form a number \(num\), recursively call \(text.substring\(num\), pattern.substring\(?\)\)
* case 2: pattern\[0\] is a letter; if text\[0\]!=pattern\[0\], return false; recursively call \(text.substring\(1\), pattern.substring\(1\)\)
* text has length of m, pattern has length of n
* 时间复杂度：O\(\(m+n\)\*n\), 一支recursion叉，高度是 length of the pattern, n. 每一层做一个substring，时间复杂度是O\(length of substring\), 因为这个API做的事情是新开一个长度是\(k-1\)的array，所以一层的时间复杂度是O\(m+n\)
* 空间复杂度：O\(\(m+n\)\*n\), n个节点，substring

#### 优化

* pattern不去记string，而是记patternStart，表示现在看的pattern应该是pattern.substring\(patternStart\)

```python
class Solution(object):
  def match(self, input, pattern):
    """
    input: string input, string pattern
    return: boolean
    """
    # write your solution here
    si, ti = 0, 0
    while (si < len(input) and ti < len(pattern)):
      if pattern[ti]<'0' or pattern[ti]>'9':
        if input[si]!=pattern[ti]:
          return False
        si+=1
        ti+=1
      else:
        count = 0
        while (ti<len(pattern) and pattern[ti]>='0' and pattern[ti]<='9'):
          count = count*10+(ord(pattern[ti])-ord('0'))
          ti+=1
        si+=count
    return si==len(input) and ti==len(pattern)
```

## Recursion + Tree bottom up

1. 从children要什么
2. 当前层做些什么、算什么、或者保存什么
3. 向parent返回什么\(和1一样\) 因为子问题逻辑一致

1和3的物理意义一样，不过作用的对象不一样

### Total in the left subtree

1. 左右子树分别的节点总数 l=left's total; r=right's total
2. 留下左子树的节点总数 self.left\_total = l
3. 返回l+r+1

关键在于第二步，1、3的信息需要足够解决第二问！和getHeight的区别是2和3，算的和返回的一样；但是在这道题里，2和3不一致；2存的left\_total，返回的是自己这棵子树上的所有节点的个数 所以2决定1，1然后物理意义决定3

```python
# Definition for a binary tree node with field to store # of nodes in its left child.
# class TreeNodeLeft:
#   def __init__(self, x):
#     self.val = x
#     self.left = None
#     self.right = None
#     self.numNodesLeft = 0

class Solution(object):
  def numNodesLeft(self, root):
    """
    :type root: TreeNode
    """
    if not root:
      return 0
    
    left = self.numNodesLeft(root.left)
    right = self.numNodesLeft(root.right)
    root.numNodesLeft = left
    
    return left+right+1
```

### node with max difference in the total number of left/right subtree

2. global\_max\_difference between the current node's left and right  
1.  size of left, size of right  
3. left\_size+right\_size+1

### LCA

> 算法由物理意义决定 物理意义由我决定  所以物理意义变了，算法也要对应着变  在这道题自己也是自己的ancestor

2. 

return value的物理意义: 

case1: 如果a和b都不在以p为root的子树里，return null  
case2: 如果a和b有一个在以p为root的子树里，return那个在的节点  
case3: 如果a和b都在，返回a和b的LCA

Base Case:

if p==null, 一定是返回null  
if p==a, 无论case2还是3都是 return a  
if p==b, return b  
合并: if p==null\|\|p==a\|\|p==b: return p

recursive rule

Q2: case 2.1 if LCA\(p.left, a, b\)==null and LCA\(p.right, a, b\)==null: return null  
case 2.2 if LCA\(p.left, a, b\)==a and LCA\(p.right, a, b\)==null, return a  
case 2.3 if LCA\(p.left, a, b\)==b and LCA\(p.right, a, b\)==null, return b  
case 2.4  
case 2.5  
case 2.6 if LCA\(p.left, a, b\)==c and LCA\(p.right, a, b\)==null; return c  
case 2.7 if LCA\(p.left, a, b\)==null and LCA\(p.right, a, b\)==c return c  
总结以上2.2~2.7: if LCA\(p.left, a, b\)!=null and LCA\(p.right, a, b\)==null  return LCA\(p.left, a, b\)  
if LCA\(p.right, a, b\)!=null and LCA\(p.left, a, b\)!=null  return p

Q1:   
leftResult=LCA\(p.left, a, b\)  
rightResult=LCA\(p.right, a, b\)  
Q2+Q3:   
if leftResult==null and rightResult==null: return null  
if leftResult!=null and rightResult==null  return leftResult  
if leftResult!=null and leftResult!=null  return p

```python
class Solution(object):
    def lowestCommonAncestor(self, root, p, q):
        """
        :type root: TreeNode
        :type p: TreeNode
        :type q: TreeNode
        :rtype: TreeNode
        """
        if not root:
          return root
        if root==p or root==q:
          return root
      
        ltree = self.lowestCommonAncestor(root.left, p, q)
        rtree = self.lowestCommonAncestor(root.right, p, q)
      
        if ltree and rtree:
          return root
        elif not ltree:
          return rtree
        else:
          return ltree
```

### LCA of k nodes

### LCA in k-nary tree

## top down

isBST，从上往下传范围，从下往上传isBS的boolean

{% embed url="https://cloud.tencent.com/developer/article/1086657" %}

{% embed url="https://phoenixzhao.github.io/一些动态规划的题目整理/" %}

{% embed url="https://segmentfault.com/a/1190000006672807" %}

{% embed url="https://blog.csdn.net/Dr\_Unknown/article/details/51939833" %}

{% embed url="https://blog.csdn.net/linhuanmars/article/details/38468361" %}

{% embed url="https://juejin.im/post/5d9ede57518825358b221349" %}



{% embed url="https://leetcode.com/problems/race-car/discuss/124326/summary-of-the-bfs-and-dp-solutions-with-intuitive-explanation" %}

### 从backtracking说起

1. 用aeiou生成string，但是有限制, 生成所有的permutation结果。

![](../.gitbook/assets/image%20%2824%29.png)

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

