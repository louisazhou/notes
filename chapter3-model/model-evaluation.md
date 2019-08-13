---
description: Model Error，Bias-Variance tradeoff，Regularization
---

# Regularization and Feature Selection

机器学习的本质不是“Given x, y=\*\*“，而是“Given x, a probability distribution “。得到的是概率分布，也就是model的那些assumption，解出来的是那些beta系数。这就有了所谓的confidence interval的说法。

最后是决策人自己根据y的distribution，得到y的取值。

![](https://cdn.mathpix.com/snip/images/jDR8vTD7Qf9MwcKo_u7kpbh_NxIBQ6uTHhenaJcPXW4.original.fullsize.png)



## Model Error

$$
\text {Error}=\text {bias}^{2}+\text {variance}+\text {Irreducible error}
$$

上面的公式不是一个“计算bias或variance“的方法，只是一个大家理解error来源的方法。我们喜欢用least square来构造loss function，是因为使用least square时irreducible error消失了。

### Mean Squared Error \(MSE\)

表现形式上看着是least square（这是一个function）的形式，但在这里这是用来表现error的方法。如果我们把公式中的theta看作y，那么它就是least square。引用MSE是为了衡量model的，是一个确认值，可以理解为 a static metric of random variable。

$$
\begin{aligned} \operatorname{MSE}(\hat{\theta}) \equiv \mathbb{E}\left((\hat{\theta}-\theta)^{2}\right) &=\mathbb{E}\left[(\hat{\theta}-\mathbb{E}(\hat{\theta})+\mathbb{E}(\hat{\theta})-\theta)^{2}\right] \\ &=\mathbb{E}\left[(\hat{\theta}-\mathbb{E}(\hat{\theta}))^{2}+2((\hat{\theta}-\mathbb{E}(\hat{\theta}))(\mathbb{E}(\hat{\theta})-\theta))+(\mathbb{E}(\hat{\theta})-\theta)^{2}\right] \\ &=\mathbb{E}\left[(\hat{\theta}-\mathbb{E}(\hat{\theta}))^{2}\right]+2 \mathbb{E}[(\hat{\theta}-\mathbb{E}(\hat{\theta}))(\mathbb{E}(\hat{\theta})-\theta)]+\mathbb{E}\left[(\mathbb{E}(\hat{\theta})-\theta)^{2}\right] \\ &=\mathbb{E}\left[(\hat{\theta}-\mathbb{E}(\hat{\theta}))^{2}\right]+2(\mathbb{E}(\hat{\theta})-\theta) \overline{\mathbb{E}(\hat{\theta})-\mathbb{E}(\hat{\theta})}+\mathbb{E}\left[(\mathbb{E}(\hat{\theta})-\theta)^{2}\right] \\ &=\mathbb{E}\left[(\hat{\theta}-\mathbb{E}(\hat{\theta}))^{2}\right]+\mathbb{E}\left[(\mathbb{E}(\hat{\theta})-\theta)^{2}\right] \\ &=\operatorname{Var}(\hat{\theta})+\operatorname{Bias}(\hat{\theta}, \theta)^{2} \end{aligned}
$$

**Irreducible error:** 无论换什么model，这个error都存在；它与model无关，cannot be reduced

**模型的准确性bias：**这个model在 _训练集稍有变化下_ 的平均输出结果**与真实值**相比的平均准确性

**模型的稳定性variance：**某一次model的数据结果与这个model的**平均水平**的差距 的平方的期望

打个比方，bias考验的是平均水平，variance考验的是本次的发挥水平（运气）



对于Bias的理解：注意对于bias的计算，变的是model而不是y=3x+5中的x。因为拿着这样一个equation，只要x=3, 得到的y就是固定值，这就没什么bias好算的。 我们其实是每一次稍微改一点训练数据创一个个有细微差异的model，然后再回来还是在给定的x下去训练y，这一系列的y理论上来说是近似的，这些y的平均水平是bias。

对于Variance的理解：如果有1000行数据，换了1行就结果导致模型完全不一样了，那此时就是high variance，它没有很好的generality。所谓的“over fitting“其实不是error更高了，而是variance高，因为bias永远是随着model的复杂度变高而降低的，如下图。

![](https://cdn.mathpix.com/snip/images/SsK7WrhvodQpFBmbOBuPPhKR-x9vGvmj_rvmGcz-iR4.original.fullsize.png)

此外，注意optimum model complexity的位置并不是bias和variance交叉的点！这是很多教科书画的图有问题的地方... 因为optimal其实是total error的最低点，bias和variance可能在任何位置，就像这张图。

这里的total error指的是testing error 或者说validation error。假如我们把数据分成三类，training, validation 和holdout，那么这个error就是validation；如果分成三类，training和testing，那么这个error就是testing error。

![](https://cdn.mathpix.com/snip/images/9ftC6__Z5eJi1W2vu_MW9oioQDImo_qZBS79MWNHBOw.original.fullsize.png)

右上角, high variance, low bias;

左下角, low variance, high bias;

右下角, high variance, high bias.

图中的蓝点代表在x是同一个值的时候稍稍变换模型后（参数细微变化）得到的预测结果，并不是多个不同的x值！

### 解决overfitting的问题

1. 提高样本量
2. 解决模型过于复杂的问题  - filter out features 减少feature的个数（eg PCA） - regularization 正则化（Ridge，Lasso\) 使模型的稳定性提高

另外overfitting是一个相对的概念，如果手头只有一个model，再差也得用。

面试题：如果我们把training data的数量增加了，发现validation error减小，说明overfitted。因为在这个过程中bias是不变的，模型的复杂度一模一样，我们通过加数据的方式竟然能显著减小error，那原来一定是overfitted。这只是个认为设计的面试题，工作中没人这么干🤷‍♀️，没事干为啥要留一部分数据专门看是不是overfitting...

## Regularization 正则化

$$
\text {Training Error}=\sum_{i=1}^{n}\left(y_{i}-f\left(\mathrm{x}_{i}\right)\right)^{2}+\text { something }
$$

$$
\text {Loss Function}=\sum_{i=1}^{n}\left(y_{i}-f\left(\mathrm{x}_{i}\right)\right)^{2}, \text { subject to something}
$$

上2式应该等价，拉格朗日乘子法

机器学习的目的是让validation error小，但是我们在训练模型的时候的error是training error，我们一直在（不管是minimum absolute value还是minimum square）让training error小，这两个error之间很明显有一个gap。something就是模型在validation和testing中表现的差异。

惩罚项，惩罚想优化least square的行为，其实是把所有x的系数拿出来惩罚。惩罚项是由lamba（一个hyperparameter）的大小来衡量。lambda是试出来的，利用含有label的training data去做cross validation，判断最好的lambda值。

### Lasso: L1 regularization

Linear:          $$\sum_{i=1}^{n}\left(y_{i}-\beta_{0}-\sum_{j=1}^{p} \beta_{j} x_{i j}\right)^{2}+\lambda \sum_{j=1}^{p}\left|\beta_{j}\right|$$ 

Logistic:       $$\operatorname{argmin}_{\beta} \sum_{i=1}^{n}\left[-y_{i} \log \left(h_{\beta}\left(x_{i}\right)\right)-\left(1-y_{i}\right) \log \left(1-h_{\beta}\left(x_{i}\right)\right)\right]+\lambda\|\beta\|_{1}$$ 

### Ridge: L2 regularization

 Linear:            $$\sum_{i=1}^{n}\left(y_{i}-\beta_{0}-\sum_{j=1}^{p} \beta_{j} x_{i j}\right)^{2}+\lambda \sum_{j=1}^{p} \beta_{j}^{2}$$ 

Logistic:       $$\operatorname{argmin}_{\beta} \sum_{i=1}^{n}\left[-y_{i} \log \left(h_{\beta}\left(x_{i}\right)\right)-\left(1-y_{i}\right) \log \left(1-h_{\beta}\left(x_{i}\right)\right)\right]+\lambda\|\beta\|_{2}^{2}$$ 



只要是基于距离定义的loss function都可以加regularization，这和是否是generalized linear model无关。



> 面试考点 L1和L2有什么区别？
>
> 如果已经问到这里了，其实不只是在问overfitting的角度上regularization的区别，因为它们解决overfitting的能力是一样的，没有区别。这里其实在问feature selection，L2对于correlated的feature同等对待，给出的系数都一样，这样更stable；而L1可以给出feature。平方项给的penalty很大，倾向于让所有feature磨平，而绝对值其实让某些feature保留，某些系数变成了0。此外也可以用random forest的feature importance做feature selection。 但是这两种认为的feature importance其实不太一样，因为RF是non-linear model。



## 工作中如何解决feature selection

1. PCA 从100个feature留下3个，但是这3个feature的物理意义已经不知道了，所以一般不用。 
2. 丢feature 先做feature importance，把不太重要的扔了，为了model performance，提高速度 
3. regularization 

