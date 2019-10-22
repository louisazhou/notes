---
description: Clustering
---

# Unsupervised Learning

## K means 

> 1. K means不是一个稳定的算法 只有当中心点的变动距离小于某个数值时认为结束，所以有可能k means得到的结果是不好的。cannot make sure to converge very quickly; some cases, may not converge at all.
> 2. 所以除了distance之外，还需要计算所有点距离中心点的距离和，然后再处以cluster size，让现在的这个值越小越好（其实是在惩罚很大的cluster）

number of cluster k的选取: 组间距离大，组内距离小 能得到最小的satisfy value的就是最好的k

## sklearn library 中的K means解读 （面试）

class sklearn.cluster.KMeans \(n\_clusters=8, init='k-means++', n\_init=10, max\_iter=300, tol=0.0001, precompute\_distance='auto', verbose=0, random\_state=None, copy\_x=True, n\_jobs=1\)

n\_init: 代表做几次k means（可能是一开始的初始点就不同），max\_iter=300 表示变几次中心点， tol=0.0001表示中心点移动距离小于万分之一。要特别清楚前两个参数的意义。

init='k-means++'：优化普通的k means，避免了有时cluster的结果很糟糕的问题 第一个中心点从已知的数据点中随便取；第二个中心点，从剩下的数据点中选取，12之间的距离的平方就是第二个点被选到的概率；第三个点，每个数据点计算和前两个中心点的距离的平方的最小值。



## Clustering

* Distance Based: K means
* Density Based: DB scan 

