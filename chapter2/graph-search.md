---
description: BFS解决的主要问题是如何systematically从一个点出发走完所有相关点
---

# Graph Search BFS

## Graph Application

1. web crawling 
2. social networking
3. garbage collection
4. ... 

## Graph Representation 

1. 如果把图存成list，每次的访问会是O\(n\)。

2. 所以更方便的应该是存成一个matrix。一个无向图的matrix应该是关于对角线对称的，还可以节省空间只存一个对角线以下的。这个matrix叫做adjacency matrix。

缺点是（1）如果想要增加点，需要多加一行+一列，对于某些编程语言就需要recreate整个矩阵。对于删除来说也是如此。（2）matrix非常sparse，浪费了很多存储空间。n个节点的无向图需要浪费n\*\(n-1\)/2的空间，Cn2. 即便是100个节点，50条边，也要浪费 $$10^{4}$$ 的存储空间。

3. 存成adjacency list，也就是一系列的key-value pair。key是节点，value是节点对应的那些邻居节点。  
a:{b,c}  
c:{a.b}  
b:{a,c}  
换用dict存。如果想要让query的操作是O\(1\),那么就可以在dict里存set；如果想要做遍历操作，用list或者linkedlist。在dict里加节点和加边也更方便。

## Graph Exploration

1. Given a source vertex s and a target vertex t, is there a path from s to t?  
2. Given a source vertex s, find all reachable vertices and edges from s.  
3. Given a graph, traverse all the nodes inside it.  
4. ...

{% tabs %}
{% tab title="伪代码" %}
```python
MetaGraphSearchAlgorithm(graph, s):
    #systematically explore all the vertices that are reachable from s.
    # put in Bag as well as mark s accordingly
    while bag is not empty:
        extract a node from the bag
        for neighbor in graph.neighbors(node):
            if neighbor is not marked:
                put neighbor in bag and mark neighbor.
                

# O(V+E*T)
# T: the time complexity for putting a node n bag 如果是queue
# 的结构，T=1
```
{% endtab %}
{% endtabs %}

### Breadth First Search

就像是近视的人找眼镜，剥洋葱似的先从最近的一圈开始找。For breadth first search algorithm, we will finish visiting all vertices that are reachable from source by k moves before visiting vertices that are reachable from source by k+1 moves. Thus we just need to replace the bag with a **queue**. 先放进bag的要先拿出来。

{% tabs %}
{% tab title="伪代码" %}
```python
BFS(graph, s):
    frontier = [s]
    has_seen = set(s)#防止走出一个环，无法停止
    while frontier:
        next=[]
        for u in frontier:
            for v in neighbors(u):
                if v not in has_seen:
                    next.append(v)
                    has_seen.add(v)
        frontier = next
```
{% endtab %}
{% endtabs %}

![](https://cdn.mathpix.com/snip/images/W9Nqthp7ZJilgYE1mDZGuBKXNrhMUX20lKl2OIrOVo8.original.fullsize.png)

对于这样一幅图，如果从B出发，3步能走完所有节点。BFS有2个很好的性质，（1）每一步都是从距离起点最短的路径出发往外走，所以能保证每一步的**跳转次数**都是最少的。BFS找的不是最短路径而是花最短代价跳转的次数，因为有可能路径的权重不一样。如果想要让跳转次数与距离等价，那就让有权重的图变成距离之间有对应权重的边的图，比如AB之间的距离是10，那就等价于AB之间有10个边。但是缺点是这样操作后，图会非常大，有很多个边和很多个节点。

（2）另外，连通、无cycle，也就是tree的结构, "BFS Spanning Tree"。每个节点之间有且只有一条路径，并且路径没有环。因为每一步都是从没有被探索的点走下一步的。而至于没有被探索的路径，可能会在tree的同一层，也可能会是跨一层，但是绝对不会跨两层。因为BFS需要保证从起点到终点走的路径是最短的。如果有跨两层的边，那就意味着存在更短的路径。

## Problems

### zigzag printing

1. Given a binary tree, return the zigzag level order traversal of its nodes' values.也就是每一层变换访问顺序，从左到右-从右到左-从左到右。

{% tabs %}
{% tab title="参考伪代码的结构" %}
```python
BFS(root):
    if not root:
        return []
    frontier = [root]
    reverse = False
    ans=[]
   #在tree的结构不会需要has_seen，因为不会从叶子往根找。
    while frontier:
        next=[]
        curr=[]
        for u in frontier:
            curr.append(u.value)
            if u.left:
                next.append(u.left)
            if u.right:
                next.append(u.right)
        ans.append(curr if not reverse else curr[::-1])
        frontier = next
        reverse = not reverse
```
{% endtab %}
{% endtabs %}

### Check Islands

2. Given a 2d grid map of '1's\(land\) and '0's\(water\), count the number of islands. An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. 

Claim: At a given cell that contains '1', we perform a BFS from this node. After this is done, we find all the lands that is reachable from this '1' and all these lands define an island. When we do BFS, we shoud start from a land that 

```python
def BFS(grid, r, c, marked):
    dr, dc = [-1,0,1,0],[0,1,0,-1]
    marked.add((r,c))
    while frontier:
        next=[]
        for r,c in frontier:
            for d in xrange(4):
                nr,nc = r+dr[d], c+dr[d]
                if 0<=nr<len(grid) and 0<=nc<len(grid[0] #合法
                and grid[nr][nc])=='1'#陆地
                and (nr,nc) not in marked:#没走过
                    node = (nr, nc)
                    marked.add(node)
                    next.apped(node)
        frontier = next
        
class Solution(object):
    def numIslands(self, grid):
        res, marked = 0, set()
        for r in xrange(len(grid)):
            for c in xrange(len(grid[0])):
                if grid[r][c]=='1' and (r,c) not in marked:
                    res += 1
                    BFS(grid, r, c, marked)
        return res
```

### beginWord endWord

3. Given two words \(beginWord and endWord\), and a dictionary's word list, find the length of shortest transformation sequence from beginWord to endWord such that:

* only 1 letter can be changed at a time
* each transformed word must exist in the word list. \(beginWord is not a transformed word\)

This is a BFS problem, because the vertex are the words, and edges are the changes.

两种做法，可从每个单词出发找到所有它可能得到的新的单词；或者用已有的dict先建立一个图，再BFS。

```python
import string
class Solution(object):
    def ladderLength(self, beginWord, endWord, wordList):
        wordList = set(wordList)
        if endWord not in wordList:
            return 0
        ans = 1
        frontier = [beginWord]
        used = set(frontier)
        while frontier:
            next = []
            for word in frontier:
                for p in xrange(len(word)):    
                    for c in string.ascii_lowercase:
                        newWord = word[:p] + c + word[p+1:]
                        if newWord == endWord:
                            return ans+1
                        if newWord in wordList and newWord not in used:
                            used.add(newWord)
                            next.append(newWord)
            frontier = next
            ans += 1
        return 0
```

### Bipartite

4. Given an undirected graph, return true if and only if it is bipartite. 

Bipartite: 2染色，使一个图的两个短点不一样的颜色；if we can split its set of nodes into 2 independent subsets A and B such that every edge in the graph has 1 node in A and another node in B.

input是adjacency list.   

A graph is bipartite &lt;-&gt; this graph does not contain odd cycle. 对于BFS的spanning tree结构来说，就是有连接同一层的边, 因为跨层的环一定包含的是偶数条边，偶数条边一定是bipartite的。所以在算法的层面，实际上是在每一层检查一个node是否被探索过的同时还要检查它能不能和同一层的某一个node相连；如果相连，就不是二分图（bipartite）。直到把所有node都查看了位置。在修改模板伪代码的时候，（1）把has\_seen变成一个hashset（2）利用BFS的性质，邻居的距离+1就是现在到root的距离 （3）比较两个距离。

```python
def bfs(graph, visited, u):
    visited[u] = 0
    frontier = [u]
    while frontier:
        next = []
        for u in frontier:
            for v in graph[u]:
                if v not in visted:
                    visited[v] = visited[u] + 1
                    next.append(v)
                elif visited[v] == visited[u]:
                    return False
        frontier = next
    return True

class Solution(object):
    def isBipartite(self, graph)
        visited = {}
        for v in xrange(len(graph)):
            if v not in visited and not bfs(graph, visited, v):
                return False
        return True
```

> 在写graph的时候要注意，如果题目没有说明这个图是联通的，要想到孤岛的情况。这也是为什么这道题写了for循环

### Closest Door

5. Given a m\*n 2D grid initialized with 3 possible values: -1 for wall, 0 for a gate, inf means an empty room; fill each empty room with the distance to its nearest gate. If it is impossible to reach a gate, it should be filled with inf.

```python
class Solution(object):
    def wallAndGates(self, rooms):
        if not rooms:
            return
        dr, dc = [-1,0,1,0],[0,1,0,-1]
        N, C = len(rooms), len(rooms[0])
        frontier, INF = [(r,c) for r in xrange(N) 
        for c in xrange(C) if rooms[r][c]==0], 2147483647
        distance = 0
        while frontier:
            next = []
            for r, c in frontier:
                for d in xrange(4):
                    nr, nc = r+dr[d], c+dc[d]
                    if 0<=nr<N and 0<=nc<C and rooms[nr][nc]==INF:
                        rooms[nr][nc] = distance + 1
                        next.append((nr, nc))
            frontier = next
            distance += 1
```



