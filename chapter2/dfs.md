---
description: 图上的DFS
---

# Graph Search DFS

如果说BFS是找眼镜，先从自己身边最近的地方摸一圈再往外找的话，那么DFS就是走迷宫，它的本质是一个backtracking的过程：从一个路口出发，走一个可以走的路口，走到底为止，再从里往外走之前所有没有走过的路口，直到回到最初的那个路口，找下一个没走过的路径... Recursively explore the graph, backtracking as necessary. 

{% code title="伪代码模板" %}
```python
def DFS(graph, visited, s):
    # recursively visit every reachable vertices from s that are still not being visited
    for u in graph.neighbors(s):
        if u not in visited:
            visited.add(u)
            DFS(graph, visited, u)

def DFS_All(graph):
    visited = set()
    for v in graph:
        if v not in visited:
            visited.add(v)
            DFS(graph, visited, v)
```
{% endcode %}

对于有向图来说，有tree edges（遍历时真的走的边），forward edge（没有走，从ancestor指向descendant的边），backward edge（没有走，从descendant指回去的边），cross edge（没有走，cousin或者uncle关系的边）。对于无向图，只有tree edge和backward edge。

![](https://cdn.mathpix.com/snip/images/FNblW4msCb0aUhGTDnnsgJybmxgpmfy62JfcKqqTbl0.original.fullsize.png)

比如这张图，如果是有向图，绿色是tree edge，蓝色是backward edge，紫色是cross edge，红色是forward edge。而如果是有向图，a-c-b-d-f-g-e-a，只有b往回看到a的这个backward edge，没有cross或者forward。

有了这样的性质，就能知道一个graph里是否有环了：检验是否有backward edge。

其他的具有特殊性质的点或边就是cut和bridge

## 题目

### 判断无向图是树

更general定义的tree就是 联通、无环的结构。

* 联通：走一遍之后看visited是否等于n
* 无环：backward edge的存在等价于graph存在loop

但是需要小心的是，走过的parent需要记录下来排除掉。比如上图中d的邻居是b和c，b是刚走过的，本来也不会从d回去。

输入：adjacency list   
input    n=5, edges=\[\[0,1\],\[0,2\],\[0,3\],\[1,4\]\]  
output True

```python
from collections import defaultdict

def has_cycle(graph, visited, parent, u):
    visited.add(u)
    for v in graph[u]:
        if v!= parent:
            if v in visited or has_cycle(graph, visited, u, v):
                return True
    return False

class Solution(object):
    def valid_tree(self, n, edges):
        visited=set()
        graph = defaultdict(list)
        for edge in edges:
            graph[edge[0]].append(edge[1])
            graph[edge[1]].append(edge[0])
        return not has_cycle(graph, visited, -1, 0) and len(visited) == n
```

### 判断有向图是树

需要加一种状态，因为现在不是“非tree edge即backward edge”的两种状态了，还需要判断它是在哪一次被访问过，因为下面这张图并不是环。但是假如2能指向1，那就是环。   

![](../.gitbook/assets/image%20%2839%29.png)

所以要在之前的基础上多加一个对backward edge的判断，因为只要不是backward edge，我们其实并不care它是forward还是cross。对于上图来说，如果我们从0开始做dfs，那么走到2的时候2没有可以往下走的下一个node，所以会返回到1、0；如果调整下面的箭头，让2指回1，那么走到2的时候在2这个node昨backtracking的时候它是可以走回0的。也就意味着这里是一个backward edge。从这里可以发现，如果每一个节点做完dfs之后还没有visit过自己的parent，那它就没有backward edge。

originally，对于每一个节点只有两种状态， not\_visited, visited   
now，每一个节点多了一个状态，not\_visited （对应着tree\_edge）, visiting（对应着backward\_edge）, visited（cross/forward）。

现在，对于一个有环的图，2看到0的时候0的label是visiting，因为这还是在0做dfs的期间。这就有效判断出来了一个有向图里是否有环

回到模板，在原来的DFS里，一遍DFS意味着访问完它邻居的所有节点，所以可以在进出dfs的时候加一个marker记录这个节点访问的状态。

```python
def DFS(graph, visited, s):
    mark s as visiting
    # recursively visit every reachable vertices from s that are still not being visited
    for u in graph.neighbors(s):
        if u not in visited:
            visited.add(u)
            DFS(graph, visited, u)
    mark s as visited
```

调整后的代码

```python
def dfs(directed_graph, visit_status, u):
    # 0 represents we are still actively visiting or dfsing a node
    # 1 represents we are done with the dfs of a node
    visit_status[u]=0
    for v in directed_graph[u]:
        if v not in visit_status:
            if dfs(directed_graph, visit_status, v):
                return True
            elif visit_status[v] == 0:
                return True
                
    visit_status[u]=1
    return False

def has_cycle(directed_graph):
    visit_status={}
    for v in directed_graph:
        if v not in visit_status and dfs(directed_graph, visit_status, v):
            return True
    return False
```

### 判断无向图是否bipartite

也是判断状态就行（和上题一样，3种状态，已经染了色且和我一样，已经染了色但和我不一样，还没有染色）。不需要counter，因为如果从一个节点出发发现自己的邻居已经被染色了而且和自己颜色一样，就意味着有奇数个环，所以就不能作为bipartite。

input: 每个node对应的邻居，eg：

input \[\[1,3\], \[0,2\], \[1,3\], \[0,2\]\]  
output True

```python
def can_color(graph, colors, u, color):
    colors[u] = color
    for v in graph[u]:
        if color[v] == color:
            return False #我的邻居已经染了色且和我一样
        elif not color[v] and not can_color(graph, colors, v, -color): 
            return False #我的邻居还没有染色，但是我的邻居发现给他染完色之后和自己的邻居冲突
    return True

class Solution(object):
    def isBarpartite(self, graph):
        colors = [0]*len(graph) #一开始都没有染色/not_visited
        for v in xrange(len(graph)):
            if not colors[v] and not can_color(graph, colors, v, 1): #not_visited而且染不了色
                return False
        return True
```

### 拓扑排序的应用（选课、安装软件包有dependency）

先构建一个graph，每一个node就是课程或者安装包的id，edges就是它们之间的dependency：如果有A指向B的edge，表示要先走完A才能走B。拓扑排序不唯一，取决于边定义的先后顺序。拓扑排序的前提是没有环，不然会死循环，鸡生蛋蛋生鸡的问题。

对于DFS来说，一个有向图的边{u,v}, v执行完之后才会执行u。所以只要在DFS的时候创建一个list存放走过的node的结果集，然后在输出时reverse the order就是我们想要的正确的dependency。但是这里其实只会返回任意一个可以满足dependency的排序。如果想要在结果集里生成所有的可能，就要在外面包一层backtracking，检查is\_compatible。

输入：2，\[\[1,0\]\] 意思是2门课，要先上完0才能上1 所以output \[0,1\]  
输入：4，\[\[1,0\],\[2,0\],\[3,1\],\[3,2\]\] output \[0,1,2,3\] or \[0,2,1,3\]

```python
from collections import defaultdict

class Solution(object):
    def findOrder(self, numCourses, prerequisites):
        graph = defaultdict(list)
        for p in prerequisites:
            graph[p[1]].append(p[0])
        courses = []
        visited = [-1]*numCourses
        
        def dfs(u):
            visited[u] = 0
            for v in graph[u]:
                if visited[v]==0:
                    return False
                elif visited[v]==-1 and not dfs(v):
                    return False
            visited[u]=1
            courses.append(u)
            return True
        
        for u in xrange(numCourses):
            if visited[u] == -1 and not dfs(u):
                return []
        
        return courses
```

## Graph的总结

{% code title="Meta Template" %}
```python
def whatever_search_first(graph, s):
    put s in the bag
    mark s
    while bag still contain nodes:
        take node from bag
        for neighbor in graph.neighbors(node):
            if neighbor is not visited:
                mark neighbor
                put neighbor in bag
```
{% endcode %}

如果bag是stack：depth first search；是queue：breadth first search；priority queue：best first search。

BFS的变种，可以双端BFS；DFS的变种... 算了凭我的智商学不会的

