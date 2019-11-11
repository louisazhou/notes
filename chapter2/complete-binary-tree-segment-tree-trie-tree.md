---
description: 字典树，segment tree
---

# Complete Binary Tree, Segment Tree, Trie Tree

## Complete Binary Tree

最后一层以上的都满，最后一层没有泡泡 complete binary tree一定是balanced binary tree 于是就可以用list或者array来存储complete binary tree，它具有以下性质：

left\_child\_node\_index = parent\_node\_index\*2+1  
right\_child\_node\_index = parent\_node\_index\*2+2  
parent\_node\_index = \(child\_node\_index-1\)/2

{% tabs %}
{% tab title="常用表示" %}
```python
class GeneralTreeNode:
    def __init__(self, children, value):
        self.children = children #list
        self.value=value
```
{% endtab %}
{% endtabs %}

Heap也可以用数组表示,因为本质上heap是一个complete binary tree

## Segment Tree

### Question

Given an array, \(1\) update\(\) at given index \(2\) query range sum, get sum of number from index start to end.

这是一个设计数据结构的问题，涉及两种操作，分别有不同的时间复杂度。所以会涉及不同的use-case，比如有些情况更改的比较少，但是求和的次数比较多，这个时候下面的时间/空间复杂度就不行了。

O\(1\) .    update\(index, value\): array\[index\]=value  
O\(n\).     query\_sum\(start, end\): return sum of array\[start:end\]

这个时候就有了第二种做法，update O\(n\), prefix\_sum O\(1\); 并且还有额外的space cost O\(n\). 在sum的时候预先求好prefix\_sum，预先存好了array\[0:index\]的和。比如对于\[1,2,3,5\],它的prefix\_sum是\[1,3,6,11\]。prefix\_sum\[i\]=prefix\_sum\[i-1\]+array\[i\], prefix\_sum\[0\]=array\[0\]. 此时如果想知道某个区间的和，只需要query\_sum\(start, end\): prefix\_sum\[end\]-prefix\_sum\[start-1\]. 

这两种方式都相对极端，要么极端偏update，要么极端偏sum。于是就有了第三种解法，平均了这两种，让两者的时间和空间复杂度都是O\(logn\). 

SegmentTree内部存list的原始index信息，根节点是本身的array，往下的treenode是这个array的均分，直到leafnode每个children都是一个元素。这也是为什么叫segmenttree：每个leafnode都是单个数字的和，treenode都是array的某一个部分。

在这样一个结构下，如果\(1\)想要更新一个数字，那么就要更新：root、它的某一个孩子、受影响的那个node、... 直到最后一个叶子。每一层又一个node会被影响，有height这么多，所以更新的时间复杂度是O\(logn\). \(2\)求sum，先从root开始看start和end的区间，看左右分别需要分到多少区间。对于某一边可能只走了一步或者几步，对于另一边可能走了整个树的高度，所以时间复杂度也是O\(n\). 

{% tabs %}
{% tab title="" %}
```python
class SegmentTreeNode:
        def __init__(self, start, end):
                self.start = start
                self.end = end
                self.sum = 0
                self.left = None
                self.right = None

class SegmentTree:
        def __init__(self, array):
                self.array = array
                self.root = self._build_segment_tree(array, 0, len(array)-1)
        
        def _build_segment_tree(self, array, start, end):
                # sanity check
                if start > end:
                        return None
                cur = SegmentTreeNode(start, end)
               
                 #base case single number
                if start == end:
                        cur.sum=array[start]
                        return cur
                
                mid = start + (end - start)/2
                cur.left = self._build_segment_tree(array, start, mid)
                cur.right = self._build_segment_tree(array, mid+1, end)
               
                if cur.left is not None:
                        cur.sum += cur.left.sum
                if cur.right is not None:
                        cur.sum += cur.right.sum
                return cur
        
        def update(self, index, value):
                diff = value - self.array[index]
                self.array[index] = value
                cur = self.root
                while cur != None:
                        cur.sum += diff
                        mid = cur.start + (cur.end-cur.start)/2
                        if index <= mid:
                                cur = cur.left
                        else:
                                cur = cur.right
        
        def sum(self, start, end):
                return self._get_sum_from_tree(self.root, start, end)
                
        def _get_sum_from_tree(self, cur, start, end):
                if cur is None or cur.start>end or cur.end<start:
                        return 0
                
                if cur.start >= start and cur.end <= end:
                        return cur.sum
                        
                return self._get_sum_from_tree(cur.left, start, end) + self._get_sum_from_tree(cur.right, start, end)
                

# testcase
s_tree = SegmentTree([1,2,3,4,5])
print s_tree.sum(1,2)
s_tree.update(1,3)
print s_tree.sum(1,2)
```
{% endtab %}
{% endtabs %}

## Trie Tree 字典树

实际应用：搜索引擎，前缀搜索

如果有一系列word：ab, abd, abch, cg, ef. 按照list的方式存储，space是O\(infinity\)，因为对于一个字典，它可以无限大，不停有新单词加入。可以优化的地方是很多单词拥有相同的前缀，那就可以节约前缀的空间。

root分成26个分岔，代表了26个字母，也就是分别以这26个字母为首。而每一个子叉都继续分成了26个叉。走到一个单词的结尾就为它加一个标记，比如加一个True。

```python
class TrieTreeNode:
    def __init__(self):
        self.children = [None]*26
        self.is_end = False
        self.count = 0
    
class TrieTree:
    def __init__(self):
        self.root = TrieTreeNode()
    
    def search(self, word):
        cur = self.root
        for i in range(len(word)):
            index = ord(word[i])=ord('a')
            if cur.children[index] is None:
                return None
            else:
                cur = cur.children[index]
        return cur.is_end
    
    def add(self, word):
        cur = self.root
        for i in range(len(word)):
            index = ord(word[i])-ord('a')
            if cur.children[index] is None:
                cur.children[index] = TrieTreeNode()
            cur.count += 1
            cur = cur.children[index]
        cur.is_end = True
    
    def delete(self, word):
        self._delete_helper(self.root, word, 0)
        
    def _delete_helper(self, cur, word, index):
        if index==len(word):
            cur.is_end = False
            return
        i = ord(word[index])-ord('a')
        self._delete_helper(cur.children[i], word, index+1)
        
        if cur.children[i].count == 0:
            cur.children[i] = None
        cur.count -= 1
```

