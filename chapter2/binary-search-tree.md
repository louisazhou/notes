---
description: 每个node，比左child最大的大，比右child最小的小
---

# Binary Search Tree

左节点, lower bound来自parent的lower bound, upper bound来自parent的值；

右节点, lower bound 来自parent的值，upper bound来自parent的upper bound

方法一：从上往下传值

```python
def BST(root):
    if root is None:
        return True
    min_val = float('-inf')
    max_val = float('inf')
    return isBST(root, min_val, max_val)
    
def isBST(root, min_val, max_val):
    if root is None:
        return True
    if root.val <= min_val or root.val >= max_val:
        return False
    return isBST(root.left, min_val, root.val) and isBST(root.right, root.val, max_val)
```



方法二：从下往上返值

左边的max，右边的min，以及一个boolean 分别是否是BST 只是需要注意，如果左max右min是None的话，就用root.val

```python
def isBSTHelper(root):
    if not root:
        return (True, None, None)
    
    left_res = isBSThelper(root.left)
    right_res = isBSThelper(root.right)
    
    if not left_res[0] or not right_res[0]:
        return (False, None, None)
    
    if left_res[2] and root.val <= left_res[2]:
        return (False, None, None)
    
    if right_res[1] and right_res[1] <= root.val:
        return (False, None, None)
        
    return (True, left_res[1] or root.val, right_res[2] or root.val)
    
def isValidBST(root):
    return isBSTHelper(root)[0]
```



方法三：inorder打印，应该ascending order

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

