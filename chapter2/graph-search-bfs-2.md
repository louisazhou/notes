---
description: 最短路径
---

# Graph Search BFS 2

找最短路径时，如果edge weight都是1，那么用BFS1就可以；如果edge weight不是1，用BFS2. Best First Search, Dijkstra's Algorithm。Dijkstra's Algorithm 找最短距离的**限制条件**是边的权重必须是正数。它不是点到一个点的算法，而是从出发点到所有点的最短路径的算法，而“两点间的距离”只是其中一个子产品。

和BFS1的区别是，BFS1把neighbor放在了FIFO的queue，而BFS2把每个点的最短距离放在了priority queue \(min heap\)。

generate的顺序不需要规定，expand才会控制min path

lazy deletion 如果删除或修改比较难，那么暂时先不改，先把新的照样放，直到不得不删除时删除。所以在generate一样的数时，完全可以把新的直接加进去，不管旧的。

1. 一个Node只被expand一次
2. 一个可以被generated多次 每次generate的路径递减
3. 所有expand的node都是单调non-decreasing
4. 棋盘格的 O\(4logn\*n\) = nlogn 一般的O\(ElogV\)
5. Termination Condition 可以是
   * 它要求的截止\(when a conflict is found\)
   * 找到了要求的node \(target node is expanded, or kth element s expanded\)
   * queue空了

### kth smallest in 2D matrix

initial state: 从最小值开始，\(0,0\)  
expansion/generation rule:   
- expand input\[i\]\[j\] generate比它大的最小                        pop  
-- generate input\[i\]\[j+1\]                                                         insert  
-- generate input\[j+1\]\[i\]                                 
termination condition:  
- when the k-th element is expanded   
deduplication:  
- only generate each element once, can use a boolean array visited\[\]\[\]; Extra Space O\(n^2\)  
- or use a Hash Table; Extra Space O\(k\)

Time: while loop k 轮，每轮净增1个（因为是出一个进两个），所以logk \(heap operation, pop \)  


