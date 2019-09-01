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



## 基本题目

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

### Leetcode 156 Binary Tree Upside Down

Base Case不是None的例子

Given a binary tree where all the right nodes are either leaf nodes with a sibling \(a left node that shares the same parent node\) or empty, flip it upside down and turn it into a tree where the original right nodes turned into left leaf nodes. Return the new root. 

![](../.gitbook/assets/image%20%283%29.png)

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



## 相关题目

Leetcode 226 104 110 236 103 109

