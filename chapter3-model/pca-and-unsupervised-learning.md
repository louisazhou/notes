---
description: PCA的步骤
---

# PCA

## PCA做什么

降维，把高维空间数据变成低维。这个操作有一个副产品，就是所得到的feature都是线性无关的。另外，因为它减少了feature，就减少了overfitting的问题。所以缺点就是最后得到的结果失去了原来的物理意义，模型也就不具有可解释性了。

“Given original data in d-dimensional space, we want to find a low-dimensional projection \(k-dimensional space, where k&lt;d\) such that it captures the most of the variability in the original data and minimizes the reconstruction error“ 所以它的目的其实是 减少重构误差。

## PCA方法的严格数学证明

$$\begin{aligned} \sum_{i=1}^{N}\left(\vec{x}_{i} \cdot \vec{v}\right)^{2} &=\|X \vec{v}\|^{2} \\ &=(X \vec{v})^{\top}(X \vec{v}) \\ &=\vec{v}^{\top} X^{\top} X \vec{v} \\ &=\vec{v}^{\top}\left(X^{\top} X\right) \vec{v} \\ &=\vec{v}^{\top} C \vec{v} \end{aligned}$$ 

V是projection vector, 在 $${v}^{T}{v}=1$$ 下 $$C=X^{T} X$$ 

拉格朗日乘子法，上面式子的最大值等价于求下面的最小值

$$L=\vec{v}^{\top} C \vec{v}+\lambda\left(\vec{v}^{\top} \vec{v}-1\right)$$ 

 $$\frac{\partial}{\partial \vec{v}} L=0$$ 

$$\frac{\partial}{\partial \vec{v}} L=\frac{\partial}{\partial \vec{v}} \vec{v} C \vec{v}+\lambda\left(\vec{v}^{\top} \vec{v}-1\right)=2 C \vec{v}-2 \lambda \vec{v}=0$$ 

所以 $$C \vec{v}=-\lambda \vec{v}$$ 这和特征向量的表达式一样，v就是构造的x矩阵的eigen vector 所以要计算的projection vector其实就是协方差矩阵C的“具有最大特征值的特征向量“。

## PCA的合理性, intuitively 

所以我们想构造的新轴，一定要两轴两两正交。需要让两轴之间的相关性尽可能的小。

如果现在两轴的相关性比较大，想把相关性变小；描述相关性：covariance

为了保留信息量多的，要用奇异值分解；去除相关性：eigen decomposition

描述轴的大小，用特征值。找到前k大的：eigen value

![](https://cdn.mathpix.com/snip/images/T4v86d3jsA5yYS1zpj6jRqGoPoSGhVlFuGJEKKUgETA.original.fullsize.png)

![](https://cdn.mathpix.com/snip/images/QZ9zkFDsPliOFUAdJCF5XMqqh-aJq3YwplhdHcpB0Dc.original.fullsize.png)

方差大的方向有更多的信息量。

## PCA的步骤

Step 1: 对样本矩阵做feature normalization，**数据归一化处理 \(非常关键）**

Step 2: 计算样本矩阵的协方差矩阵 covariance matrix

Step 3: 对协方差矩阵做奇异值分解eigen decomposition，选取最大的前k个特征值所对应的特征向量，构成投影矩阵projection matrix 

Step 4: 利用投影矩阵对样本矩阵进行projection变换，得到降维后的新矩阵



注意 不要把feature的轴和做model的过程中的feature importance搞混了，因为这是两码事。



