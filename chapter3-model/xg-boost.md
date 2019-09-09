---
description: 'eXtreme Gradient Boosting, 对decision tree做boosting'
---

# XG Boost

## Recap supervised learning

![](../.gitbook/assets/image%20%286%29.png)

Kernels: kernel trick, 在svm、knn、linear regression中都可以用到。空间映射。

混合feature：NN、SVM、KNN对于“数值和categorical mixed”的情况不太行，需要转化，但是Decision Tree不会因此受到影响。

缺失值：NN和SVM受missing value影响比较敏感，尤其SVM，如果missing是在kernel附近的。但如果k选的足够好，就对missing value不敏感。

计算复杂度：NN、SVM、KNN的计算复杂度都很大，而tree都是和数据点的个数有关的。

线性关系：NN和SVM都能捕捉到feature中的线性关系，因为本质是做线性变换。而tree考察的是不同属性，不知道不同属性之间的联系。

可解释性：tree model还有一个feature importance，但是其他model没有什么可解释性。

从表的比较来看，tree除了某些点之外，其他都还不错~ 

