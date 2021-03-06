---
description: 每个node，比左child最大的大，比右child最小的小
---

# Binary Search Tree

## Implementation 

```python
class _Node(object):
    def __init__(self, key, value):
        self.key = key
        self.value = value
        self.left = self.right = None

class BinarySearchTree(object):
    def __init__(self):
        self.__root = None
        
    def __query(self, root, key):
        if not root:
            return None
        if key<root.key:
            return self.__query(root.left, key)
        elif key>root.key:
            return self.__query(root.right, key)
        else:
            return root.value
    
    def query(self, key):
        return self.__query(self.__root, key)
    
    def FindMinimum(self, root):
        if not root:
            return None
        return FindMinimum(self.left) or root
    
    def FindFirstLargerThanTarget(self, root, target):
        if not root:
            return None
        if root.value == target:
            return self.FindMinimum(root.right)
        elif root.value < target:
            return self.FindFirstLargerThanTarget(root.right, target)
        else:
            return self.FindFirstLargerThanTarget(root.left, target) or root
    
    def FindLastSmallerThanTarget(self, root, target):
        pass
    
    def insert(self, key, value):
        self.__root = self.__insert(self.__root, key, value)
    
    def __insert(self, root, key, value):
        if not root:
            return _Node(key, value) #这个时候就只是new了一个新的node
        if key < root.key:
            root.left = self.__insert(root.left, key, value) #在dereference这里才把那个new出来的node挂在对应的位置上
        elif key > root.key:
            root.right = self.__insert(root.right, key, value) #一个个往上挂回去 最后回去的时候就能回到正确的
        else:
            root.value = value 
        return root
    
    # 如果没有等号/if里面是return，那么回去的只是这个node而已
    # heap上：new出来了node，dereference的写
    # 刚才的子树，怎么来怎么挂，无差别的写
    # 如果return null 那么就做了pruning，一半的枝都没了
    
    # 三部曲：所有判断往下走；下层返回的挂上去；操作完的结果通过return来report给上一层
    
    def __deleteMin(self, root):
        if not root.left:
            return root.right
        root.left = self.__deleteMin(root.left)
        return root
    
    def __delete(self, root, key):
        if not root:
            return None
        if key < root.key:
            root.left = self.__delete(root.left, key)
        elif key > root.key:
            root.right = self.__delete(root.right, key)
        else:
            if not root.right:
                return root.left
            if not root.left:
                return root.right
            t = root
            root = self.__min(t.right)
            root.right = self.__deleteMin(t.right)
            root.left = t.left
        return root
    
    def delete(self, key):
        self.__root = self.__delete(self.__root, key)
```

也可以iteratively query和insert

```python
def query(self, key):
    curr = self.__root
    while curr:
        if key < curr.key:
            curr=curr.left
        elif key > curr.key:
            curr = curr.right
        else:
            return curr.value
    return value

# 保留prev，然后post-processing
def insert(self, key, value):
    if not self.__root:
        self.__root = _Node(key, value)
        return
    curr, prev, is_left = self.__root, None, None
    
    while curr:
        prev = curr
        if key < curr.key:
            is_left = True
            curr = curr.left
        elif key > curr.key:
            is_left = False
            curr = curr.right
        else:
            curr.value = value 
            break
    
    if not curr:
        node = _Node(key, value)
        if is_left:
            prev.left = node
        else:
            prev.right = node 
            
# 或者也可以看它的prev
```

## 检查是否BST

左节点, lower bound来自parent的lower bound, upper bound来自parent的值；

右节点, lower bound 来自parent的值，upper bound来自parent的upper bound

### 方法一: 从上往下传值

为了维护BST的性质，每一个node都必须有自己的\(min max\)区间，只要保证每一个node都在自己的区间内，就是BST，但凡有一个跳出了这个range，就不是BST。

line 14必须是闭，因为min max的物理意义都是exclusive

```python
class Solution(object):
  def isBST(self, root):
    """
    input: TreeNode root
    return: boolean
    """
    # write your solution here
    return self.isBSThelper(root, float('-inf'), float('inf'))
  
  def isBSThelper(self, root, minvalue, maxvalue):
    if not root:
      return True
    
    if root.val <= minvalue or root.val >=maxvalue:
      return False
    
    return self.isBSThelper(root.left, minvalue, root.val) and self.isBSThelper(root.right, root.val, maxvalue)
```

Time=O\(n\)

Space=O\(height\)

### 方法二：从下往上返值

左边是否BST，右边是否BST，左边的max是否小于root，右边的min是否大于root   
  
左边的max，右边的min，以及一个boolean 分别是否是BST 只是需要注意，如果左max右min是None的话，就用root.val

```python
def isBSTHelper(root):
    if not root:
        return (True, None, None)
    
    left_res = isBSThelper(root.left)
    right_res = isBSThelper(root.right)
    
    if not left_res[0] or not right_res[0]:
        return (False, None, None)
    
    if left_res[2] and root.val < left_res[2]:
        return (False, None, None)
    
    if right_res[1] and right_res[1] < root.val:
        return (False, None, None)
    
    #(True, root下的最小值，root下的最大值)    
    return (True, left_res[1] or root.val, right_res[2] or root.val)
    
def isValidBST(root):
    return isBSTHelper(root)[0]
```

```python
def isValidBST(root):
    return isBSTHelper(root)[0]

def isBSTHelper(root):
    if not root:
        return True, None, None
    
    lr, lmin, lmax = isBSTHelper(root.left)
    rr, rmin, rmax = isBSTHelper(root.right)
    
    return lr and rr and (not lmax or lmax<root.val) and (not rmin or root.val<rmin),\
    lmin or root.val,\
    rmax or root.val
```

### 方法三：inorder打印，应该ascending order

step 1: inorder traverse the tree and store all elements in an array  
step 2: iterate over the array to determine whether a\[i\] &lt;a\[i+1\]  
  
space consumption 很糟糕  Space O\(n\) Time O\(n\)

```python
def validate(root):
    results = []
    inorder(root, results)
    for i in xrange(len(results)-1):
        if results[i]>=results[i+1]:
            return False
    return True

def inorder(root):
    if not root:
        return
    inorder(root.left, results)
    results.append(root.value)
    inorder(root.right, results)
```

或者不存array，边traverse边比较 Space O\(height\) Time O\(n\)

```python
def isValidBST(root):
    prev = [None]
    res = [True]
    inorder(root, prev, res)
    return res[0]
    
def inorder(root, prev, res):
    if not root:
        return
    
    inorder(root.left, prev, res)
    
    if prev[0] and prev[0] >= root.val:
        res[0] = False
    
    prev[0] = root.val
    
    inorder(root.right, prev, res)
```

```python
def isValidBST(root):
    prev = [None]
    return inorder(root, prev)

def inorder(root, prev):
    if not root:
        return True
    if not inorder(root.left, prev):
        return False
    if prev[0]>=root.val:
        return False
    prev[0]=root.val
    return inorder(root.right, prev) 
```

## Create a BST from a sorted list of integers 

Given the following sequence:

in-order: 1 2 4 6 7 8 9 10 12

How many BSTs can you create for n input numbers？

For 0 or 1 number, just 1 unique tree, None or TreeNode\(x\);

For n&gt;1, there will be 1 value at root, with whatever remains on the left and right to form separate subtrees. Iterate through all the values that could be the root. 

count =0

count += count\_trees\(i-1\)\*count\_trees\(n-i\), for i in range\(i, n+1\)

Another similar problem is counting the \# of valid parenthesis. 

**Catalan Number**  $$\frac{C_{2n} ^{2}}{n+1}$$ 



Which is the best one? 

—— Balanced Tree

```python
class Solution():
    def sortedListToBST(self, head):
        arr = []
        while head:
            arr.append(head.val)
            head = head.next
        return self.create(arr)

    def create(self, nums):
        if not nums:
            return None
        return self.bst(nums, 0, len(nums)-1)
    
    def bst(nums, start, end):
        if start>end:
            return None
        mid = (start+end)//2
        root = TreeNode(nums[mid])
        root.left = bst(nums, start, mid-1)
        root.right = bst(nums, mid+1, end)
        return root
```

## 利用BST的性质

### BST版的LCA

```python
def lca(root, node1, node2):
    if not root:
        return None
    if node1<=root<node2:
        return root
    
    elif root<node1
        return lca(root.right, node1, node2)
    
    elif root>node2
        return lca(root.left, node1, node2)
```

### BST版的search element

return以它为root的subtree

{% code title="如果不是一个BST，不用BST的性质" %}
```python
class Solution (object):
    def searchBST(self, root, val)
    """
    type root: TreeNode
    type val: int
    type return: TreeNode
    """
    
    if root is None:
        return None
    
    if root.val == val:
        return root
    
    left = self.searchBST(root.left, val)
    right = self.searchBST(root.right, val)
    
    if left:
        return left
    if right:
        return right
    return None
```
{% endcode %}

{% code title="利用BST的性质" %}
```python
class Solution (object):
    def searchBST(self, root, val)
    """
    type root: TreeNode
    type val: int
    type return: TreeNode
    """
    
    if root is None:
        return None
    
    if root.val == val:
        return root
    
    elif left.val < val:
        return self.searchBST(root.right, val)
    
    else:
        return self.searchBST(root.left, val)
```
{% endcode %}

### Range Sum of BST

Given the root node of a BST, return the sum of values of all nodes with the value between l and r \(inclusive\). 

{% code title="如果不是一个BST，不用BST的性质" %}
```python
class Solution (object):
    def rangesumBST(self, root, L, R)
    
    if not root:
        return 0
    r = 0
    
    if root.val >= L and root.val <= R:
        r += root.val
    r += self.rangeSumBST(root.left, L, R)
    r += self.rangeSumBST(root.right, L, R)
    
    return r
```
{% endcode %}

{% code title="利用BST的性质" %}
```python
class Solution(object):
    def rangeSumBST(self, root, L, R):
        """
        :type root: TreeNode
        :type L: int
        :type R: int
        :rtype: int
        """
        if root is None:
            return 0
    
        if root.val < L:
            return self.rangeSumBST(root.right, L, R)
        
        elif root.val > R:
            return self.rangeSumBST(root.left, L, R)
       
        else:
            return self.rangeSumBST(root.left, L, R) + root.val + self.rangeSumBST(root.right, L, R)
```
{% endcode %}

### Get Keys In Binary Search Tree In Given Range

Time: worst case O\(n\)  
更紧凑的Time complexity: O\(height+\# of nodes in the range of \[k1, k2\]\)  
Space: O\(height\), worst case O\(n\)

```python
class Solution(object):
  def getRange(self, root, min, max):
    """
    input: TreeNode root, int min, int max
    return: Integer[]
    """
    # write your solution here
    result = []
    self.getRangehelper(root,min,max,result)
    return result
  
  def getRangehelper(self,root,min,max,result):
    if not root:
      return
    
    if root.val>min:
      self.getRangehelper(root.left,min,max,result)
    
    if min<=root.val<=max:
      result.append(root.val)
    
    if root.val<max:
      self.getRangehelper(root.right,min,max,result)
```

```python
class Solution(object):
  def getRange(self, root, min, max):
    """
    input: TreeNode root, int min, int max
    return: Integer[]
    """
    # write your solution here
    result = []
    self.getRangehelper(root,min,max,result)
    return result
  
  def getRangehelper(self,root,min,max,result):
    if not root:
      return
    
    if root.val<min:
      return self.getRangehelper(root.right,min,max,result)
    
    if min<=root.val<=max:
      self.getRangehelper(root.left,min,max,result)
      result.append(root.val)
      self.getRangehelper(root.right,min,max,result)
      
    
    if root.val>max:
      return self.getRangehelper(root.left,min,max,result)
```

### Search in BST

```python
class Solution(object):
  def search(self, root, key):
    """
    input: TreeNode root, int key
    return: TreeNode
    """
    # write your solution here
    if not root or root.val==key:
      return root
    return self.search(root.left if key<root.val else root.right, key)
```

```python
class Solution(object):
  def search(self, root, key):
    """
    input: TreeNode root, int key
    return: TreeNode
    """
    # write your solution here
    curr=root
    while (curr is not None) and curr.val!=key:
      curr = curr.left if key < curr.val else curr.right
    return curr
```

### Insert in BST

```python
class Solution(object):
  def insert(self, root, key):
    """
    input: TreeNode root, int key
    return: TreeNode
    """
    # write your solution here
    if not root:
      newnode = TreeNode(key)
      return newnode
    
    if key<root.val:
      root.left=self.insert(root.left, key)
    
    elif key>root.val:
      root.right=self.insert(root.right, key)
    
    return root
```

```python
class Solution(object):
  def insert(self, root, key):
    """
    input: TreeNode root, int key
    return: TreeNode
    """
    # write your solution here
    newnode = TreeNode(key)
    if not root:
      return newnode
    curr = root

    while curr.val!=key:
      if curr.val<key:
        if not curr.right:
          curr.right = newnode
        curr = curr.right
      else:
        if not curr.left:
          curr.left = newnode
        curr = curr.left
    
    return root
```

### Delete

```python
class Solution(object):
  def deleteTree(self, root, key):
    """
    input: TreeNode root, int key
    return: TreeNode
    """
    # write your solution here
    if not root:
      return root
    
    if root.val==key:
      if not root.left:
        return root.right
      elif not root.right:
        return root.left
      elif not root.right.left:
        root.right.left = root.left
        return root.right
      else:
        newRoot = self.deleteSmallest(root.right)
        newRoot.left = root.left
        newRoot.right = root.right
        return newRoot
    
    if root.val>key:
      root.left = self.deleteTree(root.left,key)
    elif root.val<key:
      root.right = self.deleteTree(root.right,key)
    return root

  def deleteSmallest(self, root):
    while root.left.left:
      root = root.left
    smallest = root.left
    root.left = root.left.right
    return smallest
```

