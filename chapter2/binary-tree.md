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
def preorder_traversal(self, root):
    res = []
    self.helper(root,res)
    return res

def helper(self, root, res):
    if not root:
        return
    res.append(root.val)
    pre_order(root.left, res)
    pre_order(root.right, res)
```

Time Complexity O\(c\*n\)=O\(n\)    c=3

Space Complexity O\(height\)  O\(logn\)=&lt;hight&lt;=O\(n\)

#### In-Order

先左节点，然后处理自己，最后右子树 所以调整上面code的位置，4、5换一下

```python
def in_order(root):
    if not root:
        return
    in_order(root.left)
    print(root.val)
    in_order(root.right)
```

#### Post-Order

```python
def post_order(root):
    if not root:
        return
    post_order(root.left)
    post_order(root.right)
    print(root.val)
```

面试Trick: Base Case: 通常，空节点是base case （但也有些时候叶子是base case）



## 常见Binary Tree类型

### Balanced Binary Tree

The depth difference between the left and the right subtrees of every node differ by 1 or less. 不满足的节点不一定是root

### Complete Binary Tree

最后一层以上的都满，最后一层没有泡泡 complete binary tree一定是balanced binary tree 

### Binary Search Tree

每一个节点左子树的所有值比root小，右子树的所有值比root大



## Bottom Up \(从下往上返值 灵魂三问\)

1. what do we expect from the left child/ right child?
2. what do you want to do in the current layer?
3. what do you want to return to your parent \(same as 1\)

### 求二叉树的高度

```python
def get_height(root):
    if not root:
        return
    left=get_height(root.left)
    right=get_height(root.right)
    return max(left, right)+1
```

Time O\(n\)

Space O\(h\)

### 求二叉树的Size

```python
def get_size(root):
    if not root:
        return
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
    
    if abs(left_total = right_total)>global_max:
        global_max = abs(left_total = right_total)
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

### 按照Level打印二叉树（广度搜索）

维护一个队列，q=\[\]，root进，看是否有左右孩子，如果有，deque，左右孩子enque，依此类推。

同时还要注意每一层的空行 方法有很多，比如可以建立一个临时队列，q1是当前队列，q2是下一层的队列。q1打印完后，如果q2有值，把值给q1，q2接收下一行。如果q2是空，结束。

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

> 面试题目 Suppose a tree is stored on the disk, and each node may have a large number of fan-out. We only have limited size of memory. How can we access the tree in level order?
>
> 用preorder traversal模拟level order 假设root是2，在level=1的时候打印。

### Leetcode 156 Binary Tree Upside Down

Base Case不是None的例子

Given a binary tree where all the right nodes are either leaf nodes with a sibling \(a left node that shares the same parent node\) or empty, flip it upside down and turn it into a tree where the original right nodes turned into left leaf nodes. Return the new root. 

![](../.gitbook/assets/image%20%2812%29.png)

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

### Find minimum Depth

From the root down to the nearest leaf node

Base Case不是None的例子

```python
def getHeight(root):
    if not root:
        return 0
    if root.left is None and root.right is None:
        return 1
        
    left = getHeight(root.left) if root.left else float('inf')
    right = getHeight(root.right) if root.right else float('inf')
    
    return min(left, right) + 1
```

### 如何用非递归的方式打印binary tree

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

### 二叉树上找一个连续序列的长度（必须从parent到child）

```python
def longest(curr, parent, currlen):
    if not curr:
        return currlen
    size = 1
    if parent and curr.val == parent.val+1
        size = currlen_1
    return max(currlen, longest(curr.left, curr, size), longest(curr.right, curr, size))
    
longest(root, None, 0)
```



## 相关题目

Leetcode 226 104 110 236 103 109



## Top Down （从上往下传值）

1. 参数传递信息，用全局变量记录结果
2. 通常不需要返值
3. 思维更简单，但代码不如bottom up简洁
4. 如果需要输出完整path，top down更实用 但是如果依赖subtree的结果，用bottom up  如果我们认为bottom up是post-order，那么top down就可以认为是preorder

函数 参数传值

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

