# Linear Regression

## Central Limit Theorem

> 如何用Random（5）生成Random（25）？ 1. 如果是6个相加，出现0的概率极小，是6个\math\(1/5\)相加，而1的概率却要比0的概率大，因此概率不均等 2. 正确解法：5\*random\(5\)+random\(5\)

思路\(1\) 认为这是一维空间到二维空间的转变

| index | 0 | 1 | 2 | 3 | 4 |
| :---: | :---: | :---: | :---: | :---: | :---: |
| 0 | 0 | 1 | 2 | 3 | 4 |
| 1 | 0 | 1 | 2 | 3 | 4 |
| 2 | 0 | 1 | 2 | 3 | 4 |
| 3 | 0 | 1 | 2 | 3 | 4 |
| 4 | 0 | 1 | 2 | 3 | 4 |

思路\(2\) 五进制

> 其实使用这样的思想，也可以生成5的任意次方；或者任意一个小于25的数，比如Random（21）依然可以用Random（25），只是只要大于21就扔了，再random一次；相似的，如果需要Random（7）也是Random（21）再对3求余就行。

上面的1.其实结果是一个中间概率大，两边概率小的分布，此时引出“中心极限定理”

如果一个随机变量由大量相同分布且独立（i.i.d.）的随机因素综合影响所造成，则这种随机变量一般近似服从正态分布。（这其实解释了Linear Regressio的公式里的 $$\epsilon$$ 为什么是高斯分布 也就是正态分布。此外，这也就意味着given X，Y服从高斯分布，因为函数右边的前两项都是固定值。）

如果变量趋于无穷多个，研究其标准化的随机变量，服从标准正态分布。

## Linear Regression

$$y=\beta_{0}+\beta_{1}X+\epsilon$$

这是一个超定系统（overdetermined system\)，所以我们只能求得近似解。

因为我们想要拟合的y要尽可能接近 $$\hat{Y}$$ ，所以可以定义一个loss function来判断拟合的好坏。

如果真实值是 $$Y$$ 我们的估计值是 $$\hat{Y}$$ ，直观理解，我们的误差（error）就是 $$Y-\hat{Y}$$ 

Linear Regression中，使用 $$Loss=error^{2}$$ 

对于单元：

$$
\begin{array}{l}{\sum_{i=1}^{n} \varepsilon_{i}^{2}=\sum_{i=1}^{n}\left(y_{i}-\beta_{0}-\beta_{1} x_{j}\right)^{2}} \\ 
\
{\operatorname{argmin}_{\beta} \sum_{i=1}^{n}\left(y_{i}-\beta_{0}-\beta_{1} x_{i}\right)^{2}}\end{array}
$$

多元

$$
\begin{array}{c}{\sum_{i=1}^{n} \varepsilon_{i}^{2}=\sum_{i=1}^{n}\left(y_{i}-\beta_{0}-\beta_{1} x_{1 i}-\beta_{2} x_{2 i}-\ldots-\beta_{m} x_{m i}\right)^{2}} \\ 
\
{\sum_{i=1}^{n} 
\varepsilon_{i}^{2}=\sum_{i=1}^{n}\left(y_{i}-\beta_{0}-\sum_{j=1}^{m} \beta_{j} x_{j i}\right)^{2}}\end{array}
$$

> 注意
>
>  \(1\) 这个loss不一定是平方项（least square），也可以是绝对值（此时它叫least absolute deviation）甚至三次方。之所以我们使用平方，是因为我们对噪声又一个假设：它服从高斯分布。如果我们的噪声服从其它分布，自然也可以使用其它的loss function。
>
> \(2\) error的定义也不一定是直接做减法（a line vertically done\), 也可以是点线的距离（垂线）的形式。

为什么平方？

背后的原理是最大似然估计（maximum likelihood estimation）

## 最大似然估计（maximum likelihood estimation）

Maximum Likelihood Estimation is a method of estimating the parameter of a statistical model given observation, by finding the parameter values that maximize the likelihood of making the observations given the parameters. 最合理的参数估计量应该使得 （猜一个参数，使得我们猜测的分布和实际的分布尽可能一样）

这是一种概率角度的参数估计，就意味着需要对整体的分布有一个假设。在Linear Regression中，给的assumption是

![](https://cdn.mathpix.com/snip/images/jDR8vTD7Qf9MwcKo_u7kpbh_NxIBQ6uTHhenaJcPXW4.original.fullsize.png)

即， $$p(y | x)$$ 是 $$\text { mean }=\mu=\beta_{0}+\beta_{1}x$$ , $$\text { variance }=\sigma$$ \(与x无关的值）的高斯分布，数学表达式：

$$
p(y)=\frac{1}{\sigma \sqrt{2 \pi}} e^{-\frac{1}{2 \sigma^{2}}(y-\mu)^{2}}, \quad-\infty<y<\infty
$$

其中 $$p(y|x)$$ 是概率密度。

那么什么样的分布是最好的分布？我们需要让概率密度最大。因为有某个observation是既定事实了，所以我们需要找到一个parameter让我们猜出的估计和observation的分布相同，这就需要让我们估计出的最大可能就是observation，即概率密度那项最大，最后对应的就是指数部分不要负号的项最小值。

### Loss Function的推导过程

> P. S. 一般不会让推导LR的loss function，因为太简单，但是Logistic Regression的会在面试问。

首先，Given x, Y服从高斯分布

$$
Y_{i} \sim N\left(\hat{\beta}_{0}+\hat{\beta}_{1} X_{i}, \sigma^{2}\right)
$$

$$P(Y_{i}|X) （Y_{i}的概率密度）$$ 

$$
P\left(Y_{i}\right)=\frac{1}{\sigma \sqrt{2 \pi}} e^{-\frac{1}{2 \sigma^{2}}\left(Y_{i}-\hat{\beta}_{0}-\hat{\beta_{1}} X_{i}\right)^{2}}
$$

因为 $$Y_{i}$$ 是i.i.d.的，所以Y的所有样本的似然函数是

$$
\text {Likelihood }=L\left(\hat{\beta}_{0}, \hat{\beta}_{1}, \sigma^{2}\right)=P(Y | X)=P\left(Y_{1} | X_{1}\right) * P\left(Y_{2} | X_{2}\right) * \ldots * P\left(Y_{n} | X_{n}\right)
$$

$$
\text {Likelihood }=L\left(\hat{\beta}_{0}, \hat{\beta}_{1}, \sigma^{2}\right)=\frac{1}{\sigma^{n}(2 \pi)^{n / 2}} e^{-\frac{1}{2 \sigma^{2}} \sum\left(Y_{i}-\hat{\beta}_{0}-\hat{\beta}_{1} X_{i}\right)^{2}}
$$

对数似然函数

$$
\ln L=-n \ln (\sqrt{2 \pi} \sigma)-\frac{1}{2 \sigma^{2}} \sum_{i=1}^{n}\left(Y_{i}-\hat{\beta}_{0}-\hat{\beta_{1}} X_{i}\right)^{2}
$$

为了让它最大，等价于

$$
\begin{array}{c}{\operatorname{argmax}_{\beta}\left[-\sum_{i=1}^{n}\left(Y_{i}-\hat{\beta}_{0}-\hat{\beta}_{1} X_{i}\right)^{2}\right]} \\ {\quad \operatorname{argmin}_{\beta} \sum_{i=1}^{n}\left(Y_{i}-\hat{\beta}_{0}-\hat{\beta}_{1} X_{i}\right)^{2}}\end{array}
$$

## 那么，Logistic Regression呢？

如果y是离散值，categorical labels，该怎么做？

分三步去想新模型 1. 能不能用现有模型（linear regression）解决这个问题 2. 如果不行，哪里不行，能否基于LR做一些修改 3. 如果此时可以了，那么未来有什么潜在问题让它不再使用.



