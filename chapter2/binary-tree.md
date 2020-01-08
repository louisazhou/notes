---
description: 二叉树 80～90%和递归相关的的面试问题都在考binary tree
---

# Binary Tree

定义：一个树的每个节点不超过2个child nodes

```python
class TreeNode:
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None 
```

创建二叉树

```python
root = TreeNode(10)
root.left = TreeNode(5)
root.right = TreeNode(10)
... 
```

可见这也可以写成一个recursive的定义

其实，在SQL中的query plan tree也是一个 tree

![](https://cdn.mathpix.com/snip/images/IlHeI6uzHNmS8ftC_Lqd8tzcYOjofFr7G_wzQDDzjF4.original.fullsize.png)

## Tree中寻找一个节点

如果3个节点，不做任何限制，顺序可以有3！个。但如果对排列做一个规定，左子树一定要在右子树之前，那么就是3！/2！=3种可能。

分别是 前序、中序、后序 这个“前中后“是针对root的。

#### Pre-Order 

把自己放在最前面打印，然后左，然后右

```python
def pre_order(root):
    if not root:
        return
    print(root.val)
    pre_order(root.left)
    pre_order(root.right)
```

把结果存进list

```python
class Solution(object):
  def preOrder(self, root):
    """
    input: TreeNode root
    return: Integer[]
    """
    # write your solution here
    result = []
    self.helper(root,result)
    return result

  def helper(self, root, result):
    if not root:
      return result
    result.append(root.val)
    self.helper(root.left, result)
    self.helper(root.right, result)
```

Time Complexity O\(c\*n\)=O\(n\)    c=3

Space Complexity O\(height\)  O\(logn\)=&lt;hight&lt;=O\(n\)

#### In-Order

先左节点，然后处理自己，最后右子树 所以调整上面code的位置，4、5换一下

```python
class Solution(object):
  def inOrder(self, root):
    """
    input: TreeNode root
    return: Integer[]
    """
    # write your solution here
    result = []
    self.helper(root, result)
    return result

  def helper(self, root, result):
    if not root:
      return result
    self.helper(root.left, result)
    result.append(root.val)
    self.helper(root.right, result)
```

#### Post-Order

```python
class Solution(object):
  def postOrder(self, root):
    """
    input: TreeNode root
    return: Integer[]
    """
    # write your solution here
    result = []
    self.helper(root,result)
    return result
  
  def helper(self, root, result):
    if not root:
      return result
    self.helper(root.left, result)
    self.helper(root.right, result)
    result.append(root.val)
```

面试Trick: Base Case: 通常，空节点是base case （但也有些时候叶子是base case）

## 常见Binary Tree类型

所有的问题都可以简化成一个问题, ask for boolean: 是否BST，是否BBT or ask for value: max, integer of sth. 因为：

* 每层node具备的性质，传递的值和下一层的性质 往往一致，很容易定义recursive rule
* Base case \(generally\): null pointer under the leaf node 

每层遵循同样的逻辑，比如 getheight的谁高取谁然后+1

### Balanced Binary Tree

The depth difference between the left and the right subtrees of **every node** differ by 1 or less. 不满足的节点不一定是root。反例：大雁似的左右两撇。

所以要特别注意，如果一开始没有BBT，那么不能assume h=logn，因为此时应该是O\(n\)，糖葫芦状。

### Complete Binary Tree

最后一层以上的都满，最后一层没有泡泡 

* complete binary tree一定是balanced binary tree 
* 好处是有很好的locality，可以很简单的用array等物理连续的存储 做level order traversal时有紧凑的表示 Left Child Node Index = Parent Node Index \* 2 +1  __Right Child Node Index = Parent Node Index \* 2 +2

### Binary Search Tree

**每一个**node的左子树的**所有node值**比node小，右子树的所有值比node大

* 好处1是in-order print时是ascending order
* 好处2是方便查找，binary search似的，知道search的时候该往左还是往右
* 也可以存duplicated value 

![](../.gitbook/assets/image%20%287%29.png)

## Bottom Up \(从下往上返值 灵魂三问\)

1. what do we expect from the left child/ right child?
2. what do you want to do in the current layer?
3. what do you want to return to your parent \(same as 1\)

以下这几个都是Time O\(n\) Space O\(h\)

### 求二叉树的高度

```python
class Solution(object):
  def findHeight(self, root):
    """
    input: TreeNode root
    return: int
    """
    # write your solution here
    if not root:
      return 0
    left = self.findHeight(root.left)
    right = self.findHeight(root.right)
    return max(left, right)+1
```

### 求二叉树的Size

```python
def get_size(root):
    if not root:
        return 0
    left=get_size(root.left)
    right=get_size(root.right)
    return left+right+1
```

### 求二叉树的value

```python
def get_value(root):
    if not root:
        return
    left=get_value(root.left)
    right=get_value(root.right)
    return left+right+root.value
```

### 求二叉树的left child总数

```python
class TreeNode:
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None
        self.total_left = 0

def total_left(self, node)
    if not node:
        return
    left_total=get_value(node.left)
    right_total=get_value(node.right)
    node.total_left = left_total       # functionality 
    return left+right+1     # tell parent
```

### Max difference in 左子树和右子树

```python
global_max = -1   #因为None 和 一个节点是两种情况 这就做了区分
res = None

def node_diff(root):
    if root is None:
        return 0
    left_total = node_diff(root.left)
    right_total = node_diff(root.right)
    
    global global_max
    global res
    
    if abs(left_total - right_total)>global_max:
        global_max = abs(left_total - right_total)
        res = root
        
    return left_total+right_total+1
    
def get_max_dif(root):
    global global_max
    global res
    node_diff(root)
    return res
```

其实更好的做法是把两个global封装到一个class里，用函数调用reference指向object的方式去维护这两个global值。

```python
class TreeNode:
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None
    
class ResultWrapper:
    def __init__(self):
        self.global_max = -1
        self.solution = None
    
    def max_diff_node(root, res):
        if not root:
            return 0
        left_total = max_diff_node(root.left, res)
        right_total = max_diff_node(root.right, res)
        if abs(left_total-right_total)>res.global_max:
            res.global_max = abs(left_total - right_total)
            res.solution = root
        return left_total+right_total+1
    
    def max_diff(root):
        res = ResultWrapper()  #生成object/instance 
        max_diff_node(root, res)
        return res.solution 
```

### Find minimum Depth

From the root down to the nearest leaf node

Base Case不是None的例子

```python
def minDepth(root):
    if not root:
        return 0
    if root.left is None and root.right is None:
        return 1
        
    left = minDepth(root.left) if root.left else float('inf')
    right = minDepth(root.right) if root.right else float('inf')
    
    return min(left, right) + 1
```

```python
def minDepth(root): 
    # Corner Case. Should never be hit unless the code is  
    # called on root = NULL 
    if root is None: 
        return 0 
      
    # Base Case : Leaf node.This acoounts for height = 1 
    if root.left is None and root.right is None: 
        return 1
      
    # If left subtree is Null, recur for right subtree 
    if root.left is None: 
        return minDepth(root.right)+1
      
    # If right subtree is Null , recur for left subtree 
    if root.right is None: 
        return minDepth(root.left) +1 
      
    return min(minDepth(root.left), minDepth(root.right))+1
```

### Longest Ascending Path Binary Tree

```python
def longest(curr, parent, currlen):
    if not curr:
        return currlen
    size = 1
    if parent and curr.val == parent.val+1
        size = currlen+1
    return max(currlen, longest(curr.left, curr, size), longest(curr.right, curr, size))
    
longest(root, None, 0)
```

### Lowest Common Ancestor 最小公共祖先

假设两个数pq一定存在。那么只有2种情况，

* 在某一个root的左右两边找到了p和q
* 自己是root，另一个值在自己下，返回自己

```python
class Solution (object):
    def lowestCommonAncestor(self, root, p, q):
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

## 一系列经典

### 是否Balanced Binary Tree

```python
class Solution(object):
  def isBalanced(self, root):
    """
    input: TreeNode root
    return: boolean
    """
    # write your solution here
    if not root:
      return True
    left = self.getheight(root.left)
    right = self.getheight(root.right)
    if abs(left-right)>1:
      return False
    return self.isBalanced(root.left) and self.isBalanced(root.right)
    

  def getheight(self, root):
    if not root:
      return 0
    left = self.getheight(root.left)
    right = self.getheight(root.right)
    return max(left, right)+1
```

但因为调用get\_height太多次，这不是最优解。  

#### 时间复杂度的分析

recursion tree画出来后每一个node的时间复杂度之和。单独分析

* Best Case：退化成单链表的tree，第一次算left和right时就return False, O\(n\) 下面的isBalanced就没有执行，只有root上的那一对getHeight
* **Worst Case**: 刚好是个**Balanced Tree**，必须每个node都要算一遍isBalanced  **O\(nlogn\)**

#### 空间复杂度的分析

* Worst Case：退化成单链表的tree, O\(n\) 
* **Best Case：**刚好是个**Balanced Tree  O\(logn\)**

![](../.gitbook/assets/image%20%2858%29.png)

![](../.gitbook/assets/image%20%2870%29.png)

更好的，改一下get\_height 让找到不平衡时提前终止， Time Complexity O\(n\)

```python
class Solution (object):
    def isBalanced(self, root):
        if self.helper(root) == -1
            return False
        else:
            return True
    
    def helper(self, root):
        if not root:
            return 0
        
        left=self.get_height(root.left)
        right=self.get_height(root.right)
        
        if left==-1 or right==-1:
            return -1
        elif abs(left-right)>1:
            return -1
        else:
            return max(left, right)+1
```

### Symmetric Binary Tree

四叉树，在上面code的基础上加一个or就行。

#### 时间复杂度：

Worst Case: 假如input的左右两个tree都是balanced，那么每层分别1， 4， 16， 64， 256...个tree node， 总共有$$h=log_2n$$ 层，最后一层的时间复杂度是 $$O(4^{log_2n})=O(n^2)$$ 

Best Case: 假如input不是balanced，极端情况是两个linked list，此时的recursion tree是一叉树，时间复杂度是O\(n\)

{% tabs %}
{% tab title="Python" %}
```python
class Solution(object):
  def isSymmetric(self, root):
    """
    input: TreeNode root
    return: boolean
    """
    # write your solution here
    if not root:
      return True

    return self.helper(root.left, root.right)

  def helper(self, left, right):
    if not left and not right:
      return True
    elif not left or not right:
      return False
    elif left.val!=right.val:
      return False
    return self.helper(left.left, right.right) and self.helper(left.right, right.left)
```
{% endtab %}

{% tab title="Java" %}
```java
boolean isSymmetric(TreeNode left, TreeNode right) {
    if (left==null && right==null) {
        return true;
    } else if (left==null || right==null){
        Return false;
    } else if (left.value!=right.value) {
        return false;
    }
        return isSymmetric(left.left, right.right) && isSymmetric(left.right, right.left);
}
```
{% endtab %}
{% endtabs %}

### Tweaked Identical Binary Trees

{% tabs %}
{% tab title="Python" %}
```python
class Solution(object):
  def isTweakedIdentical(self, one, two):
    """
    input: TreeNode one, TreeNode two
    return: boolean
    """
    # write your solution here
    if not one and not two:
      return True
    elif not one or not two:
      return False
    elif one.val!=two.val:
      return False

    return (self.isTweakedIdentical(one.left, two.left) and self.isTweakedIdentical(one.right, two.right)) or (self.isTweakedIdentical(one.left, two.right) and self.isTweakedIdentical(one.right, two.left))
```
{% endtab %}

{% tab title="Java" %}
```java
boolean isSymmetric(TreeNode left, TreeNode right) {
    if (left==null && right==null) {
        return true;
    } else if (left==null || right==null){
        return false;
    } else if (left.value!=right.value) {
        return false;
    }
        return isSymmetric(left.left, right.left) && isSymmetric(left.right, right.right) //case 1
        ||
        isSymmetric(left.left, right.right) && isSymmetric(left.right, right.left);
}
```
{% endtab %}
{% endtabs %}

### Invert a Binary Tree

```python
class Solution:
    def invertTree(self, root):
        if root is None:
            return None
        if root.left:
            self.invertTree(root.left)
        if root.right:
            self.invertTree(root.right)
        root.left, root.right = root.right, root.left
        return root
```

```python
class Solution:
    def invertTree(self, root):
        if root is None:
            return None
        tmp_right = root.right
        root.right = self.invertTree(root.left)
        rootlleft = self.invertTree(tmp_right)
        return root
```

### Binary Tree Upside Down

Base Case不是None的例子

Given a binary tree where all the right nodes are either leaf nodes with a sibling \(a left node that shares the same parent node\) or empty, flip it upside down and turn it into a tree where the original right nodes turned into left leaf nodes. Return the new root. 

![](../.gitbook/assets/image%20%2844%29.png)

根变成了左孩子的右孩子（line8），根的左孩子变成了根，根的右孩子变成了左孩子的左孩子\(line 7\)

这和linked list的题目，reverse a singly linked list有点像。

1—&gt; 2 —&gt; 3 —&gt; 4 —&gt; None

cur. next. next =cur \(line7&8\)

cur.next = None \(line9&10\)

那么对于这个tree来说，root.right=root.left root.left=root

```python
def upside_tree(root):
    if not root:
        return root
    if not root.left and not root.right:
        return root
    left_tree = upside_tree(root.left)
    root.left.left = root.right #根的右孩子变成了左孩子的左孩子
    root.left.right = root #根变成了左孩子的右孩子
    root.left = None
    root.right = None
    return left_tree
```

Time: O\(n\)

Space: O\(h\)

## Queue/Stack + Tree

### Print iteratively binary tree

用stack 区别只是 在访问每个节点第几次的时候打印它 

如果是先序，第一次就打印；如果是中序，第二次打印；如果是后序，第三次打印

stack里存Node和第几次访问该Node 

```python
def preorder_traversal(root):
    output=[]
    if not root:
        return output 
    stack =[(root,1)]
    
    while stack:
        node, count = stack.pop()
        if count == 1:
            output.append(node.val)
            stack.append((node, count+1))
            if node.left:
                stack.append((node.left, 1))
        if count ==2:
            if node.right:
                stack.append((node.right,1))
        return output
```

```python
def inorder_traversal(root):
    output=[]
    if not root:
        return output 
    stack =[(root,1)]
    
    while stack:
        node, count = stack.pop()
        if count == 2:
            output.append(node.val)
            if node.right:
                stack.append((node.right, 1))
        if count ==1:
            stack.append((node, count+1))
            if node.left:
                stack.append((node.left,1))
        return output
```

```python
def postorder_traversal(root):
    output=[]
    if not root:
        return output 
    stack =[(root,1)]
    
    while stack:
        node, count = stack.pop()
        if count == 3:
            output.append(node.val)
        if count ==1:
            stack.append((node, count+1))
            if node.left:
                stack.append((node.left, 1))
         if count ==2:
            stack.append((node, count+1))
            if node.right:
                stack.append((node.right, 1))
        return output
```

### 按照Level打印二叉树（广度搜索）

维护一个队列，q=\[\]，root进，看是否有左右孩子，如果有，deque，左右孩子enque，依此类推。同时还要注意每一层的空行 方法有很多，比如可以建立一个临时队列，q1是当前队列，q2是下一层的队列。q1打印完后，如果q2有值，把值给q1，q2接收下一行。如果q2是空，结束。

```python
def print_by_level(root):
    q = [root]
    nextline = []
    line = []
    
    while q:
        head = q.pop(0)
        if head.left:
            nextline.append(head.left)
        if head.right:
            nextline.append(head.right)
        line.append(head.val)
        
        if not q:
            print(line)
            if nextline:
                q=nextline
                nextline = []
                line = []
```

Time: O\(n\)

Space: O\(max\(len\(q\)\)\)

这个思路也可以用来解minDepth的题

```python
def minDepth(root): 
    # Corner Case 
    if root is None: 
         return 0 
  
    # Create an empty queue for level order traversal 
    q = [] 
      
    # Enqueue root and initialize depth as 1 
    q.append({'node': root , 'depth' : 1}) 
  
    # Do level order traversal 
    while(len(q)>0): 
        # Remove the front queue item 
        queueItem = q.pop(0) 
      
        # Get details of the removed item 
        node = queueItem['node'] 
        depth = queueItem['depth'] 
        # If this is the first leaf node seen so far 
        # then return its depth as answer 
        if node.left is None and node.right is None:     
            return depth  
          
        # If left subtree is not None, add it to queue 
        if node.left is not None: 
            q.append({'node' : node.left , 'depth' : depth+1}) 
  
        # if right subtree is not None, add it to queue 
        if node.right is not None:   
            q.append({'node': node.right , 'depth' : depth+1}) 
```

或者，使用一个queue，做一个counter记录需要打印几次。

```python
def levelOrder(self, root):
    if not root:
        return []
    queue = deque([root])  #queue=deque() deque.append(root) 注意这里需要[] 因为deque需要接收iterable的参数
    result = []
    while queue:
        count = len(queue)
        current_level = []
        while count > 0:
            node = queue.popleft()
            current_level.append(node.value)
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
            count-=1
        result.append(current_level)
    return result
```

> 面试题目 Suppose a tree is stored on the disk, and each node may have a large number of fan-out. We only have limited size of memory. How can we access the tree in level order?
>
> 用preorder traversal模拟level order 假设root是2，在level=1的时候打印。

再followup 如果来回从左到右-从右到左，zigzag打印？

——必须用2个queue，再加一个层数的变量

### Get Keys In Binary Tree Layer By Layer Zig-Zag Order

```python
def zigzag (self, root):
    if not root:
        return []
    
    reverse = True
    res = []
    q = [root]
    next = []
    line = []
    
    while q:
        head = q.pop(0)
        if head.left:
            next.append(head.left)
        if head.right:
            next.append(head.right)
        line.append(head.val)
        
        if not q:
            if reverse:
                res.extend(line[::-1])
            else:
                res.extend(line)
            if next:
                q = next
                reverse = not reverse
                next = []
                line = []
        
        return res
```

## Top Down （从上往下传值）

1. 参数传递信息，用全局变量记录结果
2. 通常不需要返值
3. 思维更简单，但代码不如bottom up简洁
4. 如果需要输出完整path，top down更实用 但是如果依赖subtree的结果，用bottom up  如果我们认为bottom up是post-order，那么top down就可以认为是preorder 即先做完自己这层的事情再做children的

### 求二叉树的高度

```python
class Solution (object):
    def findHeight(self, root):
        if root is None:
            return 0
        self.result=0
        self.helper(root, 0) #还没有任何操作，没有任何计算，height=0
        return self.result 

    def helper(self, root, depth):
        if root is None:
            self.result = max(self.result, depth)
            return
        
        self.helper(root.left, depth+1)
        self.helper(root.right, depth+1)
        return 
```

如果在上面的代码加一个\_\_init\_\_， 把self.result=0放进init去，那么，每次调用时，上一次的result值并没有重置，每次需要加一个 s=Solution，创建一个新的object。

### 求二叉树的最大值

{% tabs %}
{% tab title="Top Down" %}
```python
class Solution:
    def maxValue(self, root):
        self.max = None
        self.helper(root)
        return self.max
    
    def helper(self, root):
        if root is None:
            return
        self.max = max(self.max, root.value)
        self.helper(root.left)
        self.helper(root.right)
```
{% endtab %}

{% tab title="Bottom Up" %}
```python
class Solution:
    def maxValue(self, root):
        if root is None
            return None
        left = self.maxValue(root.left)
        right = self.maxValue(root.right)
        
        return max(root.vale, left, right)
```
{% endtab %}
{% endtabs %}

### Path Sum to Target

#### Solution 1: Top Down

```python
class Solution():

    def hasPathSum(self, root, sum):
        self.sum = sum
        self.ret = False  #先假定False，只要在Line13找到一条True，之后的ret都是True
        self.helper(root, 0)
        return self.ret
        
    def helper(self, root, current):
        if self.ret == True：
            return
        if root is None:
            return
        if root.left is None and root.right is None
            self.ret = (self.ret or (current+root.val == self.sum))
            return
        
        self.helper(root.left, current+root.val)
        self.helper(root.right, current+root.val)
        return
```

变种

```python
class Solution(object):
    
    def hasPathSum(self, root, sum):
        self.sum = sum
        if root is None:
            return False
        self.ret = False
        self.helper(root,root.val)
        return self.ret
    
    def helper(self, root, current):
        if root is None:
            return 
        
        if root.left is None and root.right is None:
            self.ret = (self.ret or current== self.sum)
            return
        
        if root.left:
            self.helper(root.left, current+root.left.val) 
        
        if root.right:
            self.helper(root.right, current+root.right.val)
```

Time: O\(n\)

Space: O\(hight\)

#### Solution 2: bottom up

```python
class Solution(object):
    
    def hasPathSum(self, root, sum):
        return self.sumPath(root, 0, target)
    
    def sumPath(self, curr, partial, target):
        
        if curr is None:
            return False
        
        partial += curr.val
        
        if not curr.left and not curr.right:
            return partial==target
            
        return self.sumPath(curr.left, partial, target) or self.sumPath(curr.right, partial, target)
```

```python
class Solution(object):
    
    def hasPathSum(self, root, sum):
        if not root:
            return False
        
        if not root.left and not root.left and root.val==sum:
            return True
        
        sum-=root.val
        
        return self.hasPathSum(root.left, sum) or self.hasPathSum(root.right, sum)
```

