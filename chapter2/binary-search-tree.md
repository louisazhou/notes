---
description: 每个node，比左child最大的大，比右child最小的小
---

# Binary Search Tree

左节点, lower bound来自parent的lower bound, upper bound来自parent的值；

右节点, lower bound 来自parent的值，upper bound来自parent的upper bound

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

