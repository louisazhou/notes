---
description: 直观引入、数学引入、与Linear Regression的区别
---

# Logistic Regression

## Logistic Regression 直观引入：图形的角度理解

![](https://cdn.mathpix.com/snip/images/GgRig_wBwKiNUo5H3JkwAPcQb7UD3AxW6cujzjB2Rsk.original.fullsize.png)



1. 直观理解，Linear Regression和Logistic Regression的区别，或者说，我们为什么要使用logistic?

如果我们在做一个基于肿瘤大小预测恶性肿瘤的预测，在数据组A中使用了我们唯一会的模型linear regression，拟合了这样一条曲线，似乎还不错。此时来了一个新的数据，我们依然用这个linear model，为了让model更make sense，这条线需要斜率变小。 但如果计算它此时的loss function（比如就用least square），就会很糟糕...  因为linear regression对于extreme observations比较敏感（不管它是不是outlier）。

2. 从1的例子中知道，linear regression把每一个数据点以相同的权重来对待，这并不是我们想要的。“区别对待“指的是中间的分界线变化剧烈，两边的斜率变化不太大。如此，不同的x对于P的contribution也就不同了。

在上面的例子中，y轴是一个P，概率值，所以x与y的关系转化成了x与P的关系，P与y之间的联系又是一个Bernouli：P\(y=1\)=p, P\(y=0\)=1-p. 那么我们就得到了

$$
p=g(z)=\frac{e^{z}}{e^{z}+1}=\frac{1}{1+e^{-z}}
$$

## Logistic Regression 数学引入：值域的角度理解

想建立x和p的关系，从值域的角度，因为概率的值域是01之间，我们的y取值却是正负无穷。odds也有一个概率的含义在其中，所以可以取一个odds，凑半个无穷，再对odds取个log，凑出正负无穷

$$
\begin{array}{l}{p \rightarrow[0,1],  } {1-p \rightarrow[0,1],} \\ {\text { odds }=p /(1-p) \rightarrow[0, \text { + infinite }]} \\ {\log (\text {odds}) \rightarrow[\text { -infinite, }+\text { infinite }]}\end{array}
$$

细节，可能性=feature的线性组合

$$
\begin{array}{c}{\log \frac{p}{1-p}=z=a x+b \Leftrightarrow p=\frac{1}{1+e^{-\alpha x-b}}} \\ 

{\log \frac{p}{1-p}=a x+b} \\ {\log \frac{1-p}{p}=-(a x+b)} \\ {\frac{1-p}{p}=e^{-(a x+b)}} \\ {\frac{1}{p}=1+e^{-(a x+b)}} \\ {p=\frac{1}{1+e^{-(a x+b)}}}\end{array}
$$

从现象看是否能解释，从后往前推，这并不是在推导Logistic Regression。

总之， $$p=F(x)=\frac{1}{1+e^{-\left(\beta_{0}+\beta_{1} x\right)}}$$ 

{% hint style="info" %}
（Good to know\) Logistic Regression算线性模型（linear regression model）吗？  
  
属于Generalized Linear Model. 

  
**Ordinary Linear:**  只要服从 $$E(Y | X)=X \beta$$ 就是linear model  
**Generalized Linear:** 如果数据经历一个transformation g可以得到$$E(Y | X)=g^{-1}(X \beta)$$ ，其中g就叫link function. 这么一来，理论上任何一个model都可以是linear mode；但是因为link function在数学上很难找，所以现在发现的也没几个。就像是SVM，理论上都可以把数据放在更高维，但实际... 

以上的所有log都是自然对数，而不是10为底的对数，因为这种构造会让模型的使用比较简单。  
Exponential Family是GLM的核心，是在给定限定条件下，作出最少假设的分布家族。  
\[参考\]\([https://www.cs.ubc.ca/~murphyk/MLbook/](https://www.cs.ubc.ca/~murphyk/MLbook/)\)
{% endhint %}

## 从Bernoulli Distribution到Logistic Regression

### 翻硬币

从data，model，induction principle的角度考虑一个“翻硬币”的问题，

$$
\begin{array}{l}{\text { Data: }<x_{1}, x_{2}, \cdots, x_{n}>, x_{i} \in\{0,1\}} \\ {\text { Prior Knowledge: } P\left(x_{i}=1\right)=\theta, P\left(x_{i}=0\right)=1-\theta} \\ {\text { Induction Principle: Maximum Likelihood } \hat{\theta}=\underset{\theta}{\arg \max } \prod_{i=1}^{n} P\left(x=x_{i}\right)}\end{array}
$$

直观理解，$$\hat{\theta}$$ 的结果应该是 $$avg(x)=\frac{\sum_{i} x_{i}}{n}$$ 。来推导一下对不对：

$$
P\left(x=x_{i}\right)=\left\{\begin{array}{c}{\theta, x_{i}=1} \\ {1-\theta, x_{i}=0}\end{array}=\theta^{x_{i}} \cdot(1-\theta)^{\left(1-x_{i}\right)}\right.
$$

$$
\begin{aligned} \hat{\theta} &=\underset{\theta}{\arg \max } \prod_{i=1}^{n} P\left(x=x_{i}\right) \\ &=\arg \max _{\theta} \log \left(\prod_{i=1}^{n} P\left(x=x_{i}\right)\right) \\ &=\underset{\theta}{\arg \max } \sum_{i=1}^{n} \log \left(P\left(x=x_{i}\right)\right) \\ &\left.=\underset{\theta}{\arg \max } \sum_{i=1}^{n} \log \left(\theta^{x_{i}} \cdot(1-\theta)^{1-x_{i}}\right)\right) \\ &=\underset{\theta}{\arg \max } \sum_{i=1}^{n} x_{i} \log \theta+\left(1-x_{i}\right) \log (1-\theta) \end{aligned}
$$

$$
\begin{aligned} \frac{d L}{d \theta} &=\sum_{i}\left(\frac{x_{i}}{\theta}-\frac{1-x_{i}}{1-\theta}\right) \\ &=\sum_{i} \frac{x_{i}(1-\theta)-\theta\left(1-x_{i}\right)}{\theta(1-\theta)} \\ &=\frac{\sum_{i} x_{i}-x_{i} \theta-\theta+x_{i} \theta}{\theta(1-\theta)} \\ &=\frac{\sum_{i} (x_{i}-\theta)}{\theta(1-\theta)} \\ &=\frac{\sum_{i} x_{i}-n \cdot \theta}{\theta(1-\theta)} \end{aligned}
$$

令上面的导数等于零，所以 $$\sum_{i} x_{i}=n \cdot \theta$$ ，得到 $$\theta=\frac{\sum_{i} x_{i}}{n}$$ 。

### 条件翻硬币

$$
\begin{aligned}
\text {Data: }  
\\&
{ \left\langle\left(x_{1}, y_{1}\right),\left(x_{2}, y_{2}\right), \ldots,\left(x_{n}, y_{n}\right)\right\rangle} \\& { y \in\{0,1\}} \\& {x_{i} \in \mathbb{R}^{m}}
\end{aligned}
\begin{aligned}
\text{Model: } 
\\&
{P(Y=y | x ; \alpha, \beta)=\frac{1}{1+e^{-\left(\alpha+\sum_{j=1}^{m} \beta_{j} x_{j}\right)}}} \\& {\alpha \in \mathbb{R}, \beta \in \mathbb{R}^{m}}
\end{aligned}
$$

$$
\begin{aligned}
\text {Induction Principle: }  
\\&
<\hat{\alpha}, \hat{\beta}>=\underset{\alpha, \beta}{\arg \max } \prod_{i} P\left(y_{i} | x_{i} ; \alpha, \beta\right) \\ &=\underset{\alpha, \beta}{\arg \max } \log \left(\prod_{i} P\left(y_{i} | x_{i} ; \alpha, \beta\right)\right) \\ &=\underset{\alpha, \beta}{\arg \max } \sum_{i} \log \left(P\left(y_{i} | x_{i} ; \alpha, \beta\right)\right)

\end{aligned}

\\
L_{i}=\sum_{i} \log \left(P\left(y_{i} | x_{i} ; \alpha, \beta\right)\right)
$$

所以现在的问题变成了 $$\begin{array}{l}{=\log \left(\left\{\begin{array}{c}{P_{i}, y_{i}=1} \\ {1-P_{i}, y_{i}=0}\end{array}\right)\right.} \\ {=\log \left(P_{i}^{y_{i}} \cdot\left(1-P_{i}\right)^{1-y_{i}}\right)}\end{array}$$ 。此时，把P和Z引入， $$\begin{aligned} P_{i} &=\frac{1}{1+e^{-z_{i}}} \\ z_{i} &=\alpha+\sum_{k} \beta_{k} x_{i k} \end{aligned}$$ 

利用chain rule： $$\frac{\delta L}{\delta \beta_{j}}=\sum_{i=1}^{n} \frac{\delta L_{i}}{\delta P_{i}} \cdot \frac{\delta P_{i}}{\delta z_{i}} \cdot \frac{\delta z_{i}}{\delta \beta_{j}}$$ ，每一项拿出来：

$$
\begin{aligned} \frac{\delta L_{i}}{\delta P_{i}} &=\frac{y_{i}}{P_{i}}-\frac{1-y_{i}}{1-P_{i}} \\ &=\frac{y_{i}-y_{i} P_{i}-P_{i}+y_{i} P_{i}}{P_{i}\left(1-P_{i}\right)} \\ &=\frac{y_{i}-P_{i}}{P_{i}\left(1-P_{i}\right)} \end{aligned}

\begin{aligned} P_{i} &=\frac{1}{1+e^{-z_{i}}} \\ \frac{\delta P_{i}}{\delta z_{i}} &=-\frac{1}{\left(1+e^{-z_{i}}\right)^{2}} \cdot(-1) \cdot e^{-z_{i}} \\ &=\frac{1}{1+e^{-z_{i}}} \cdot \frac{e^{-z_{i}}}{1+e^{-z_{i}}} \\ &=P_{i} \cdot\left(1-P_{i}\right) \end{aligned}

\begin{aligned} z_{i} &=\alpha+\sum_{k=1}^{m} \beta_{k} x_{i k} \\ \frac{\delta z_{i}}{\delta \beta_{j}} &=x_{i j} \end{aligned}
$$

最后，

$$
\begin{aligned} \frac{\delta L}{\delta \beta_{j}} &=\sum_{i=1}^{n} \frac{\delta L_{i}}{\delta P_{i}} \cdot \frac{\delta P_{i}}{\delta z_{i}} \cdot \frac{\delta z_{i}}{\delta \beta_{j}} \\ &=\sum_{i=1}^{n} \frac{y_{i}-P_{i}}{P_{i}\left(1-P_{i}\right)} \cdot P_{i}\left(1-P_{i}\right) \cdot x_{i j} \\ &=\sum_{i=1}^{n}\left(y_{i}-P_{i}\right) \cdot x_{i j} \end{aligned}

\begin{aligned} \frac{\delta L}{\delta \beta_{j}} &=\sum_{i=1}^{n}\left(y_{i}-P_{i}\right) \cdot x_{i j} \\ \frac{\delta L}{\delta \alpha} &=\sum_{i=1}^{n}\left(y_{i}-P_{i}\right) \end{aligned}
$$

从 $$\frac{\delta L}{\delta \alpha}=0$$ 可以推出 $$\sum_{i=1}^{n} y_{i}=\sum_{i=1}^{n} P_{i}$$ 。 所以说，logistic regression is well calibrated. 

### （面试）Logistic Regression中的Loss Function的推导

虽然也有使用信息熵Entropy的方式进行的定义，但是更直观的还是使用maximum likelihood。

第一步：伯努利分布的P

$$
\begin{array}{c}{P\left(Y=y_{i} | x_{j}\right)=p^{y_{i}}(1-p)^{1-y_{i}}, 0<p<1, y_{i}=\{0,1\}} \\ 
{p=h_{\beta}\left(x_{j}\right)=\frac{1}{1+e^{-\left(\beta_{0}+\beta_{1} x\right)}}}\end{array}
$$

$$
\text { i.e. } P\left(Y=y_{i} | x_{j}\right)=h_{\beta}\left(x_{j}\right)_{i}^{y_{i}}\left(1-h_{\beta}\left(x_{j}\right)\right)^{1-y_{i}}
$$

第二步: 用MLE算Loss Function

$$
\begin{array}{c}{L\left(\hat{\beta}_{0}, \hat{\beta}_{1}\right)=P\left(Y_{1}, Y_{2}, \ldots, Y_{n} | X\right)=P\left(Y_{1} | X_{1}\right) * P\left(Y_{2} | X_{2}\right) * \ldots * P\left(Y_{n} | X_{n}\right)} \\ {L\left(\hat{\beta}_{0}, \hat{\beta}_{1}\right)=\prod_{i=1}^{n} h_{\beta}\left(x_{j}\right)^{y_{i}}\left(1-h_{\beta}\left(x_{j}\right)\right)^{1-y_{i}}}\end{array}
$$

$$
\log \left(L\left(\hat{\beta}_{0}, \hat{\beta}_{1}\right)\right)=\sum_{i=1}^{n}\left[y_{i} \log \left(h_{\beta}\left(x_{i}\right)\right)+\left(1-y_{i}\right) \log \left(1-h_{\beta}\left(x_{i}\right)\right)\right]
$$

$$
\begin{array}{l}{\operatorname{argmax}_{\beta} \sum_{i=1}^{n}\left[y_{i} \log \left(h_{\beta}\left(x_{i}\right)\right)+\left(1-y_{i}\right) \log \left(1-h_{\beta}\left(x_{i}\right)\right)\right]} \\ 
\
{\operatorname{argmin}_{\beta} \sum_{i=1}^{n}\left[-y_{i} \log \left(h_{\beta}\left(x_{i}\right)\right)-\left(1-y_{i}\right) \log \left(1-h_{\beta}\left(x_{i}\right)\right)\right]}\end{array}
$$

## Logistic Regression 也可以有不止一个x, 也可以是多次方

$$
F(x)=g\left(\beta_{0}+\beta_{1} x_{1}+\beta_{2} x_{2}+\beta_{3} x_{1}^{2}+\beta_{4} x_{2}^{2}\right)
$$

每次对不同的beta求偏导数就行

![](https://cdn.mathpix.com/snip/images/XH-SqQeadMPeNMhShIDBUm1hhY7hwpOzclBWBSUsPPE.original.fullsize.png)

Fun Fact: 这上面的蓝线都是怎么画的？

其实，它就是 $$P(Y=y | X) = 1/(1+exp(***))=1/2$$ 

## 两个以上的Label的Logistic Regression

1. One Vs.  All
2. Softmax 

（其实这两个差不多）

## 区分Ordinary Linear和Logistic的Assumption

Linear Regression： $$p(y | x)$$ 是 $$\text { mean }=\mu=\beta_{0}+\beta_{1}x$$ , $$\text { variance }=\sigma$$ \(与x无关的值）的高斯分布

Logistic Regression: y 服从 $$P(Y=y|X=x)=\frac{1}{1+e^{-(a x+b)}}$$ 的伯努利分布



> 面试问题：logistic默认是0.5为分类决策值吗？
>
> 默认是的，但其实可以调，根据具体的use case调整。  
> 面试问题实际是“如何提高logistic regressor“的precision？ 答案是改变分类决策值。



> 面试问题：logistic regression和linear regression的区别是什么？
>
> 一步步回答
>
> （1）处理的问题不同，logistic的Y是离散的
>
> （2）所以这就涉及到：x连续，y离散，那么我们就认为它服从伯努利分布，有一个变量p
>
> （3）变量p和x都是连续的，于是就有了对应关系
>
> （4）区别在于 linear是线性关系，而logistic是指数函数关系，对应的是logic function
>
> 如果追问：指数关系是什么关系，为什么指数？
>
> 本质是因为x与p有非线性关系，所以我们需要惩罚离分类决策面很远的x对分类决策的影响；换句话说，不同的x对于loss的contribution需要区别对待。





## 经验之谈

\(1\) Logistic Regression 适用于很多feature的dataset， feature少的更适合random forest。

\(2\) Logistic Regression can handle cases where features have strong correlation.

\(4\) The IID assumption of Logistic Regression is weaker than Naive Bayes. For Naive Bayes, we need strong IID. But for Logistic Regression, all we need is 'given x, y is iid'. 

\(3\) Logistic Regression is resilient to unbalanced labels \(1:100 is fine, but beyond that, need down sampling\). 





