---
description: 'Decision Tree, Ensemble Learning and Random Forest, SVM and KNN'
---

# Nonlinear Models

## Decision Tree

Decision Tree是supervised prediction model，可以做regression，也可以做classification。

一棵决策树的生成过程主要分为下3个部分：

​ 1、特征选择：从训练数据中众多的特征中选择一个特征作为当前节点的分裂标准，如何选择特征有着很多不同量化评估标准，从而衍生出不同的决策树算法。

​ 2、决策树生成：根据选择的特征评估标准，从上至下递归地生成子节点，直到数据集不可分则决策树停止生长。树结构来说，递归结构是最容易理解的方式。

​ 3、剪枝：决策树容易过拟合，一般来需要剪枝，缩小树结构规模、缓解过拟合。剪枝技术有预剪枝和后剪枝两种。



在特征选择上，描述信息系统所带的信息量的物理量不同，有Gini index，有entropy.

### 以ID3为例

熵：度量随机变量的不确定性。这里的负号是因为想取到正值，因为 $$log_{2}p_{i}$$ 是负的。

$$
I_{e}=-\log _{2} p_{i}
$$

定义：假设随机变量X的可能取值有 $$x_{1},x_{2},...,x_{n}$$ ，对于每一个可能的取值 $$x_{i}$$ ，其概率为 $$P(X=x_{i})=p_{i},i=1,2...,n$$ 。随机变量的熵为：

$$
H(X)=-\sum_{i=1}^{n}p_{i}log_{2}p_{i}
$$

​对于样本集合，假设样本有k个类别，每个类别的概率为 $$\frac{|C{k}|}{|D|}$$ _其中_ $${|C{k}|}$$为类别为k的样本个数， $$|D|​$$ 为样本总数。样本集合D的熵为：

$$
H(D)=-\sum_{k=1}^{k}\frac{|C_{k}|}{|D|}log_{2}\frac{|C_{k}|}{|D|}
$$

熵可以表示样本集合的不确定性，熵越大，样本的不确定性就越大。因此可以使用划分前后集合熵的差值来衡量使用当前特征对于样本集合D划分效果的好坏。 ​ 假设划分前样本集合D的熵为H\(D\)。使用某个特征A划分数据集D，计算划分后的数据子集的熵为H\(D\|A\)。

信息增益： $$g(D,A)=H(D)-H(D|A)$$ 

​ 在决策树构建的过程中我们总是希望集合往最快到达纯度更高的子集合方向发展，因此我们总是选择使得信息增益最大的特征来划分当前数据集D。  
​ 

思想：计算所有特征划分数据集D，得到多个特征划分数据集D的信息增益，从这些信息增益中选择最大的，因而当前结点的划分特征便是使信息增益最大的划分所使用的特征。  
​ 

> 理解：为什么说Decision Tree是一个非线性模型？
>
> 因为取值不仅与x有关，还与x所处的为止有关；位置又是所有的x一起决定的。
>
> 假如我们把Decision Tree Model写成这样的函数关系$$ f(X)=v_{root}+\sum_{k=1}^{K} \operatorname{contribution}\left(x_{k}\right) $$， $$v_{root}$$ 是根节点上的值，contribution是输入数据第k个feature对输出结果产生的作用（eg Entropy的减小、Gini Impurity的减小等），那和linear regression的 $$f(X)=a+\sum_{k=1}^{K} b_{k} x_{k}$$ 相比，LR里的每一项x对f\(X\)的contribution取决于x的值和系数b，而DT中的f\(X\)和path的先后顺序有关、和x的位置有关。这是一种非线性关系。

ID3: Entropy

C4.5: Gain Rate

CART \(Classification and Regression Tree\): Gini Impurity 



**决策树算法的缺点**：

1、对连续性的字段比较难预测。

2、容易出现过拟合。

3、当类别太多时，错误可能就会增加的比较快。

4、在处理特征关联性比较强的数据时表现得不是太好。

5、对于各类别样本数量不一致的数据，在决策树当中，信息增益的结果偏向于那些具有更多数值的特征。



#### 剪枝处理的作用及策略:

剪枝处理是决策树学习算法用来解决过拟合问题的一种办法。

​ 在决策树算法中，为了尽可能正确分类训练样本， 节点划分过程不断重复， 有时候会造成决策树分支过多，以至于将训练样本集自身特点当作泛化特点， 而导致过拟合。 因此可以采用剪枝处理来去掉一些分支来降低过拟合的风险。

​ 剪枝的基本策略有预剪枝（pre-pruning）和后剪枝（post-pruning）。

​ 预剪枝：在决策树生成过程中，在每个节点划分前先估计其划分后的泛化性能， 如果不能提升，则停止划分，将当前节点标记为叶结点。

​ 后剪枝：生成决策树以后，再自下而上对非叶结点进行考察， 若将此节点标记为叶结点可以带来泛化性能提升，则修改之。



但其实在工业界中更常用的是Ensemble Learning的方法。

## Ensemble Learning

### Bagging \(Bootstrap Aggregating\)

本质是Sampling with replacement, 有放回的抽样，这其实是Bootstrap的工作。通过引入随机性来解决overfitting的问题。 

如果原先有800行数据，那么bagging就是让不同树看到这800行数据的不同行，但是每棵树看到的数据总数是相同的，这就意味着每棵树都会看到一些重复的数据，这个重复的来源就是每次抽样时的有放回抽样。boostrap有放回抽样也保证了数据的分布相同，这和普通的做sampling相比就更好了。



### Boosting

——放在XG Boost的section

把很多Decision Tree组合在一起，再引入一些randomness

## Random Forest

**Decision Tree + Bagging + Feature Sampling =Random Forest**

假设总数据有N行，M个feature

1. 每次从原数据中有放回地随机选取N个观测值（有重复，也有没被包含的数据）
2. 从Feature中每次抽不同的K个特征，K&lt;M \(k一般是floor\(sqrt\(M\)\)\)
3. 多次重复，得到不同的decision tree，整合在一起构成forest

### Random Forest的优势

1. Less overfitting
2. Parallel Implementation \(相比较boosting，boosting只能sequential\)

### Feature Importance in Random Forest （应用很多）

对于linear model，我们可以根据linear model的系数beta的绝对值判断feature的重要程度，但对于non-linear model，就没有系数让我们一眼判断了，所以业界常用RF的feature importance来判断某一个feature的重要程度。

论原理，如果一个feature很重要，那么它在所有树的Gini impurity都很重要，所以RF的feature importance也可以看作是对gini impurity的平均值。事实上，在scikit learn的package中，它也是如此实现的。

简单来说，就是去掉一个feature，然后给这一列输入各种随机变量，看没有了这个feature之后新模型的表现如何，这个performance的差值就体现了这个feature的重要性。按理来说，所有feature的importance都应该是个正数，否则就意味着'it's worse than randomness', 它的存在只是在捣乱，很明显就不合理了。

另一种理解方式是，这个importance是“在分叉时以某个feature作为主干分叉的概率“

$$
\text {importance}(\text {feature } i)=\text {performance}(R F)-\text {performance}\left(R F^{(\text {random } i)}\right)
$$

## Support Vector Machine 

慢！真的慢 所以现在没人用了

基本思想：Maximize minimum margin 找到一条线，距离它最近的点越远越好，这样就有更大的容错率。所以在training的过程中，离线最近的点要尽可能的远。

五六年前的面试曾经考推导，所以意思一下吧：

$$
M=\frac{2}{\|w\|}=\frac{2}{\sqrt{w \cdot w}}
$$

M是margin width，w是法向量 

$$
\begin{array}{l}{w \cdot x^{+}+b=1} \\ {w \cdot x^{-}+b=-1} \\ {x^{+}=x^{-}+\lambda w} \\ {\left|x^{+}-x^{-}\right|=M}\end{array}
$$

$$
\begin{array}{l}{w \cdot\left(x^{-}+\lambda w\right)+b=1} \\ {\Rightarrow w \cdot x^{-}+b+\lambda w \cdot w=1} \\ {\Rightarrow \lambda w \cdot w=2} \\ {M=\left|x^{+}-x^{-}\right|=|\lambda w|=\lambda \sqrt{w \cdot w}=\frac{2}{\sqrt{w-w}}}\end{array}
$$

$$
\min {1/2}\|w\|^{2}, \text { s.t. } y_{i}\left(w^{T} x_{i}+b\right) \geqslant 1, i=1, \ldots, n
$$

Support Vector Regressor 刚好相反，其实是“离分割平面最远的点离分割平面越近越好“。

直观理解，SVM的表达式里有一个长得很像loss function的项，所以它不容易over-fitting。 

## K Nearest Neighbor 

K Nearest Neighbor的prediction stage是找距离，没有training的过程。

有个有趣的面试题目，推荐系统，如果amazon有1billion个用户的历史购买信息、他们的feature，那就得有所有的data points，把新来的用户拉过来一个个算distance，这个操作的时间复杂度是O\(n\)。你会如何优化？  
  
答案是空间换时间，有一个算法叫Locality Sensitivity Hashing. 先把data分成一批区域，用横纵坐标划分块，然后再算。利用hash，利用feature在O（1）算出是什么颜色，然后再计算对应distance是什么。这其实牺牲了一点精准度，因为在颜色的分界区域其实不太好定义。但对于时间要求比较高的推荐系统，这个非常合理。



![](../.gitbook/assets/image%20%281%29.png)

[https://github.com/spotify/annoy](https://github.com/spotify/annoy) 

